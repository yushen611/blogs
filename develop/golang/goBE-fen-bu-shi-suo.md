---
title: '分布式锁-基于redis或基于mysql的go语言实现'
date: 2023-06-19 12:23:16
tags: [go]
published: true
hideInList: true
feature: 
isTop: false
---
<meta name="referrer" content="no-referrer"/>



# 一、分布式锁的概念



## 概念

分布式锁是一种在分布式系统中实现互斥控制的技术。它可以保证在多个线程或者多台服务器同时访问共享资源时，只有一个线程或服务器能够获得锁并对资源进行操作，其他线程或服务器需要等待锁的释放才能继续访问。分布式锁一般使用一些共享存储系统（如Redis、Zookeeper等）提供的原子操作实现，比如setnx操作、CAS操作等。这样就可以避免多个线程或服务器在同一时间同时访问共享资源导致数据出错的问题。



## 举例

假设有一个在线抽奖系统，多个用户同时进入页面想要参与抽奖，但是系统只能允许一个用户抽奖，其他用户需要等待前一个用户中奖后才能再次抽奖。这时候就需要用到分布式锁来实现。

分布式锁的实现方式可以是基于共享存储系统，例如Redis。假如多个用户同时发起请求，系统可以先在Redis中创建一个名为“lottery_lock”的键值对，设置过期时间为5秒，然后通过setnx操作尝试获取这个键的锁。如果获取到锁，其中一个用户就可以进行抽奖，抽完奖后再通过del操作或者设置过期时间来释放锁。如果没有获取到锁，代码需要等待，并且在等待过程中定时刷新过期时间。

因为Redis是一个单线程处理请求的系统，所以setnx和del操作都是原子性操作，可以保证资源在分布式环境下被正确地占用和释放。

这就是一个简单的使用分布式锁的例子，它可以确保在线抽奖系统在多用户同时参与时，只有一个用户能够中奖。



 

# 二、基于redis的实现

> 源码已在github上分享:[StudyDemo/distributedLock at main · yushen611/StudyDemo (github.com)](https://github.com/yushen611/StudyDemo/tree/main/distributedLock)

## 0.全部代码

所有代码都在一个main.go文件里

```go
package main

import (
	"fmt"
	"sync"
	"time"

	"github.com/go-redis/redis"
)

var (
	redisClient *redis.Client
	once        sync.Once
)

func GetRedis() *redis.Client {
	once.Do(func() {
		redisClient = initRedis()
	})
	return redisClient
}

func initRedis() *redis.Client {
	client := redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "", //No password set
		DB:       0,  // Use default DB
	})

	return client
}

const (
	lockExpire = 10 * time.Second // 锁的超时时间
	lockValue  = "1"              // 锁的内容
)

func acquireLock(lockName string, timeout time.Duration) bool {
	/*
		将lockKey作为锁的名称，lockValue作为锁的值，lockExpire作为锁的超时时间。
		如果SetNX命令返回1，说明成功地设置了键值对，并且这个锁被当前请求的客户端获取，
		如果SetNX命令返回0，说明当前锁已经被其他客户端获取，请求的这个客户端没有获取成功。
	*/
	startTime := time.Now()
	for {
		result, err := GetRedis().SetNX(lockName, lockValue, lockExpire).Result()
		if err == nil && result == true {
			return true
		}
		if timeout > 0 && time.Now().Sub(startTime) > timeout {
			return false
		}
		time.Sleep(time.Millisecond * 100)
	}

}

func releaseLock(lockName string) bool {
	result, err := GetRedis().Del(lockName).Result()
	if err != nil || result == 0 {
		return false
	}
	return true
}

// 示例代码
func main() {

	const lockKey = "mylock" // 锁的名称
	if acquireLock(lockKey, time.Second*1) {
		// 执行需要加锁的代码
		fmt.Println("DO SOMETHING")
		defer releaseLock(lockKey)
	} else {
		// 未获取到锁
		fmt.Println("Failed to acquire lock")
	}

}

```



## 1.与redis连接

这里采用单例模式与redis连接

```go
var (
	redisClient *redis.Client
	once        sync.Once
)

func GetRedis() *redis.Client {
	once.Do(func() {
		redisClient = initRedis()
	})
	return redisClient
}

func initRedis() *redis.Client {
	client := redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "", //No password set
		DB:       0,  // Use default DB
	})

	return client
}
```



## 2.获取锁操作与释放锁操作

  获取锁操作是基于redis的SetNX功能，可以实现原子化操作，且在设置key,value的同时可以设置获取时间。如果锁过期了，在redis里就会自动删除，redis这一特性避免了手动删除过期的锁的麻烦。

  同时，获取锁并不是只获取一次而是获取多次，引入了超时机制。如果在规定时间内没有获取成功，才放弃获取。

```go
const (
	lockExpire = 10 * time.Second // 锁的超时时间
	lockValue  = "1"              // 锁的内容
)

func acquireLock(lockName string, timeout time.Duration) bool {
	/*
		将lockKey作为锁的名称，lockValue作为锁的值，lockExpire作为锁的超时时间。
		如果SetNX命令返回1，说明成功地设置了键值对，并且这个锁被当前请求的客户端获取，
		如果SetNX命令返回0，说明当前锁已经被其他客户端获取，请求的这个客户端没有获取成功。
	*/
	startTime := time.Now()
	for {
		result, err := GetRedis().SetNX(lockName, lockValue, lockExpire).Result()
		if err == nil && result == true {
			return true
		}
		if timeout > 0 && time.Now().Sub(startTime) > timeout {
			return false
		}
		time.Sleep(time.Millisecond * 100)
	}

}

func releaseLock(lockName string) bool {
	result, err := GetRedis().Del(lockName).Result()
	if err != nil || result == 0 {
		return false
	}
	return true
}
```

## 3.使用的实例代码

```go
// 示例代码
func main() {

	const lockKey = "mylock" // 锁的名称
	if acquireLock(lockKey, time.Second*1) {
		// 执行需要加锁的代码
		fmt.Println("DO SOMETHING")
		defer releaseLock(lockKey)
	} else {
		// 未获取到锁
		fmt.Println("Failed to acquire lock")
	}

}
```



# 三、基于mysql实现

> 源码已在github上分享:[StudyDemo/distributedLock at main · yushen611/StudyDemo (github.com)](https://github.com/yushen611/StudyDemo/tree/main/distributedLock)

基于mysql实现与redis的唯一不同是，mysql需要手动删除过期的锁。

## 0.全部代码

所有代码都在一个main.go文件里

```go
package main

import (
	"errors"
	"fmt"
	"path/filepath"
	"sync"
	"time"

	"gorm.io/driver/mysql"
	"gorm.io/gorm"
)

var (
	db   *gorm.DB
	once sync.Once
)

func GetDB() *gorm.DB {
	once.Do(func() {
		db = initDB()
	})
	return db
}

func initDB() *gorm.DB {

	var user string = "root"
	var password string = "123456"
	var host string = "localhost"
	var port string = "3306"
	var dbname string = "offermaker"
	var Dsn = fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=utf8mb4&parseTime=True&loc=Local",
		user,
		password,
		host,
		port,
		filepath.Join(dbname),
	)

	DB, err := gorm.Open(mysql.Open(Dsn), &gorm.Config{})
	if err != nil {
		panic("failed to connect database, error=" + err.Error())
	}
	fmt.Println("successfully connect database")

	return DB
}

const (
	LOCK_EXPIRE_TIME = 2000 // 锁的过期时间（毫秒）
)

type DistributedLock struct {
	Name       string `gorm:"type:varchar(255);primary_key"`
	ExpireTime int64  `gorm:"type:bigint(20)"`
}

func FindLock(name string) (lock *DistributedLock, err error) {
	// 查询记录
	result := GetDB().First(&lock, "name = ?", name)
	// 处理查询结果
	if result.RowsAffected == 0 {
		return nil, errors.New("lock not found")
	} else if result.Error != nil {
		return nil, result.Error
	} else {
		return lock, nil
	}
}

func DeleteLock(name string) (bool, error) {
	db := GetDB()
	tx := db.Begin()

	lock := DistributedLock{
		Name: name,
	}
	if err := tx.Delete(&lock).Error; err != nil {
		tx.Rollback()
		return false, err
	}

	if err := tx.Commit().Error; err != nil {
		tx.Rollback()
		return false, err
	}
	return true, nil
}

func (lock *DistributedLock) AcquireLock(timeout time.Duration) (success bool, err error) {
	startTime := time.Now()                               //当前时间
	now := startTime.UnixNano() / int64(time.Millisecond) // 获取当前时间的毫秒数
	lock.ExpireTime = now + LOCK_EXPIRE_TIME              // 计算锁的过期时间
	//检查如果存在锁，其是否过期
	existlock, err := FindLock(lock.Name)
	if err != nil {
		fmt.Println("查找存在错误") //注：这里无论查找成功或失败与否均不要返回，因为还未尝试获取锁呢
	}
	if existlock != nil {
		fmt.Printf("查找成功,存在该锁")
		//如果过期就删除
		if now > existlock.ExpireTime {
			fmt.Println("已过期")
			isDelete, err := DeleteLock(lock.Name)
			if err != nil {
				//删除失败
				fmt.Println("删除出现错误") //注：这里无论删除成功或失败与否均不要返回，因为还未尝试获取锁呢
			}
			if isDelete != true {
				fmt.Println("未删除成功") //注：这里无论删除成功或失败与否均不要返回，因为还未尝试获取锁呢
			}

		} else {
			fmt.Println("未过期")
		}

	}
	for {
		result := GetDB().Create(lock)
		if result.Error == nil || result.RowsAffected != 0 {
			fmt.Println("AcquireLock Successfully")
			return true, nil
		}
		if timeout > 0 && time.Now().Sub(startTime) > timeout {
			return false, nil
		}
		time.Sleep(time.Millisecond * 100)
	}
}

func (lock *DistributedLock) ReleaseLock() (success bool, err error) {
	result := GetDB().Where("name = ?", lock.Name).Unscoped().Delete(&DistributedLock{})
	if result.Error != nil || result.RowsAffected == 0 { // 如果删除失败，则锁不存在或已过期
		fmt.Println("ReleaseLock UnSuccessfully")
		return false, nil
	}
	fmt.Println("ReleaseLock Successfully")
	return true, nil
}

func main() {
	const LOCK_NAME = "my_lock" // 定义锁名称
	// 获取锁
	lock := &DistributedLock{Name: LOCK_NAME, ExpireTime: 0}
	if ok, err := lock.AcquireLock(time.Second * 1); err == nil {
		if ok {
			// 获取锁成功后的执行业务逻辑操作
			fmt.Println("获取锁成功，我要做一些操作了")
			defer lock.ReleaseLock() //释放锁
		} else {
			// 获取锁超时
			fmt.Println("timeout: fail to get DistributedLock")
		}
	} else {
		// 获取锁失败后的执行业务逻辑操作
		fmt.Println("获取锁失败")
	}

}

```

## 1.mysql中分布式锁的表结构

```mysql
CREATE TABLE `distributed_locks` (
  `name` varchar(255) NOT NULL COMMENT '锁的名称',
  `expire_time` BIGINT(20) NOT NULL COMMENT '过期时间',
  PRIMARY KEY (`name`)
) COMMENT '分布式锁表';
```



## 2.与mysql连接的代码

与mysql的连接同样采用单例模式

```go
var (
	db   *gorm.DB
	once sync.Once
)

func GetDB() *gorm.DB {
	once.Do(func() {
		db = initDB()
	})
	return db
}

func initDB() *gorm.DB {

	var user string = "root"
	var password string = "123456"
	var host string = "localhost"
	var port string = "3306"
	var dbname string = "offermaker"
	var Dsn = fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=utf8mb4&parseTime=True&loc=Local",
		user,
		password,
		host,
		port,
		filepath.Join(dbname),
	)

	DB, err := gorm.Open(mysql.Open(Dsn), &gorm.Config{})
	if err != nil {
		panic("failed to connect database, error=" + err.Error())
	}
	fmt.Println("successfully connect database")

	return DB
}
```



## 3.定义gorm结构体与基础操作

gorm结构体 是指与数据库表的映射

基础操作在这里包括了 查找与删除数据

```go
type DistributedLock struct {
	Name       string `gorm:"type:varchar(255);primary_key"`
	ExpireTime int64  `gorm:"type:bigint(20)"`
}

func FindLock(name string) (lock *DistributedLock, err error) {
	// 查询记录
	result := GetDB().First(&lock, "name = ?", name)
	// 处理查询结果
	if result.RowsAffected == 0 {
		return nil, errors.New("lock not found")
	} else if result.Error != nil {
		return nil, result.Error
	} else {
		return lock, nil
	}
}

func DeleteLock(name string) (bool, error) {
	db := GetDB()
	tx := db.Begin()

	lock := DistributedLock{
		Name: name,
	}
	if err := tx.Delete(&lock).Error; err != nil {
		tx.Rollback()
		return false, err
	}

	if err := tx.Commit().Error; err != nil {
		tx.Rollback()
		return false, err
	}
	return true, nil
}
```



## 4.获取锁与释放锁操作

```go
const (
	LOCK_EXPIRE_TIME = 2000 // 锁的过期时间（毫秒）
)

func (lock *DistributedLock) AcquireLock(timeout time.Duration) (success bool, err error) {
	startTime := time.Now()                               //当前时间
	now := startTime.UnixNano() / int64(time.Millisecond) // 获取当前时间的毫秒数
	lock.ExpireTime = now + LOCK_EXPIRE_TIME              // 计算锁的过期时间
	//检查如果存在锁，其是否过期
	existlock, err := FindLock(lock.Name)
	if err != nil {
		fmt.Println("查找存在错误") //注：这里无论查找成功或失败与否均不要返回，因为还未尝试获取锁呢
	}
	if existlock != nil {
		fmt.Printf("查找成功,存在该锁")
		//如果过期就删除
		if now > existlock.ExpireTime {
			fmt.Println("已过期")
			isDelete, err := DeleteLock(lock.Name)
			if err != nil {
				//删除失败
				fmt.Println("删除出现错误") //注：这里无论删除成功或失败与否均不要返回，因为还未尝试获取锁呢
			}
			if isDelete != true {
				fmt.Println("未删除成功") //注：这里无论删除成功或失败与否均不要返回，因为还未尝试获取锁呢
			}

		} else {
			fmt.Println("未过期")
		}

	}
	for {
		result := GetDB().Create(lock)
		if result.Error == nil || result.RowsAffected != 0 {
			fmt.Println("AcquireLock Successfully")
			return true, nil
		}
		if timeout > 0 && time.Now().Sub(startTime) > timeout {
			return false, nil
		}
		time.Sleep(time.Millisecond * 100)
	}
}

func (lock *DistributedLock) ReleaseLock() (success bool, err error) {
	result := GetDB().Where("name = ?", lock.Name).Unscoped().Delete(&DistributedLock{})
	if result.Error != nil || result.RowsAffected == 0 { // 如果删除失败，则锁不存在或已过期
		fmt.Println("ReleaseLock UnSuccessfully")
		return false, nil
	}
	fmt.Println("ReleaseLock Successfully")
	return true, nil
}

```

## 5.使用的示例代码

```go
func main() {
	const LOCK_NAME = "my_lock" // 定义锁名称
	// 获取锁
	lock := &DistributedLock{Name: LOCK_NAME, ExpireTime: 0}
	if ok, err := lock.AcquireLock(time.Second * 1); err == nil {
		if ok {
			// 获取锁成功后的执行业务逻辑操作
			fmt.Println("获取锁成功，我要做一些操作了")
			defer lock.ReleaseLock() //释放锁
		} else {
			// 获取锁超时
			fmt.Println("timeout: fail to get DistributedLock")
		}
	} else {
		// 获取锁失败后的执行业务逻辑操作
		fmt.Println("获取锁失败")
	}

}
```

