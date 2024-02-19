---
title: '开始你的第一个solidity合约ERC20（真实网络环境版）[fantom testnet/remix]'
date: 2023-08-21 02:12:47
tags: []
published: true
hideInList: true
feature: 
isTop: false
---
# 正确的部署合约与验证合约的流程
ERC20是一个最基础的发行自己在以太坊上的代币的合约接口。
本教程于2023年08月21日，由于使用的是0.8.20版本，会出现合约部署成功但是验证失败的问题

一般合约写完后的步骤就是

> 1.部署
>
> 2.验证

## step0: metaMask钱包连接fantom testnet真实测试网络

> Setup Fantom testnet in wallet:

https://docs.fantom.foundation/wallet/set-up-metamask-testnet

> Faucet (You can request 5 fantom from here for free): 免费领5个测试币

https://faucet.fantom.network/



## step1:编译一份实现了ERC20的合约复制到remix

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

// https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v3.0.0/contracts/token/ERC20/IERC20.sol
interface IERC20 {
    function totalSupply() external view returns (uint);

    function balanceOf(address account) external view returns (uint);

    function transfer(address recipient, uint amount) external returns (bool);

    function allowance(address owner, address spender) external view returns (uint);

    function approve(address spender, uint amount) external returns (bool);

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
}

contract ERC20 is IERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "G2303465C";
    string public symbol = "YWY";
    uint8 public decimals = 18;

    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```
注意，这份代码原本是0.8.20版本，但是经过实践发行，如果用20版本以及20版本的编译器，在高级设置里需要如下设置（EVM的地址以及把代码优化打开） 后面的编译器设置都是**paris的EVM**以及**Enable optimization**，以及**Auto compile**

下图以0.8.19版本的编译器配置为例：

![image-20230821022718150](https://gitee.com/yushen611/img/raw/master/image-20230821022718150.png)



## step2:部署到测试网络上



<img src="https://gitee.com/yushen611/img/raw/master/image-20230821023133245.png" alt="image-20230821023133245" style="zoom:80%;" />



成功的话remix下会有合约地址显示出来给你复制，也可以在metamask上找到交易地址，从而找到合约地址

![image-20230821023248662](https://gitee.com/yushen611/img/raw/master/image-20230821023248662.png)



## step3:验证

先找到你的合约地址

找到的网站：

Explorer: https://testnet.ftmscan.com/

<img src="https://gitee.com/yushen611/img/raw/master/image-20230821023526451.png" alt="image-20230821023526451" style="zoom:67%;" />

然后进行验证即可

验证时需要选择一些内容，如下：

<img src="https://gitee.com/yushen611/img/raw/master/image-20230821023636754.png" alt="image-20230821023636754" style="zoom:67%;" />

进入之后把你的代码直接复制上去即可，记得把验证时的优化打开

<img src="https://gitee.com/yushen611/img/raw/master/image-20230821023806701.png" alt="image-20230821023806701" style="zoom:67%;" />

验证成功之后即可看到你的合约具体信息了

<img src="https://gitee.com/yushen611/img/raw/master/image-20230821023855570.png" alt="image-20230821023855570" style="zoom:67%;" />

我的合约地址：0x8701c965a59b56225Bd13D430FF6f539C91327d2



# 踩坑记录

## BUG1: EVM shanghai BUG-导致部署不通过

如果evm不更改，有可能默认给你选上海。编译可以通过，但是部署通过不了



## BUG2: Compiler version high Bug-导致验证不通过

我一开始是用的0.8.20 version（sol文件写的是^0.8.20,以及编译器用的也是0.8.20版本），但是编译部署都没问题了，验证不通过了。

经过尝试，发现需要降版本到0.8.19版本，下次如果发现代码并未用到新版本特性，可以考虑降低声明的版本与编译器，来解决这种离谱的bug



