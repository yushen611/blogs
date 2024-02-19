---
title: 'pyg基础'
date: 2023-08-26 16:32:34
tags: []
published: true
hideInList: true
feature: 
isTop: false
---
<meta name="referrer" content="no-referrer"/>

本文记录了如何pyg中的数据结构，数据集，以及点分类如何写代码的流程

# data,dataset,dateloader

## torch_geometric.data.Data：表示图

### Data的两种构建方式

- **data的构建方式一：[2,edge_nums] + [node_nums,feature_dim]**

pyg中的**data是图**，图由**每个节点代表的特征向量**，以及大小为**[2,edge_num]的source to target的边集合列表**构成 

```python
import torch
from torch_geometric.data import Data

edge_index = torch.tensor([[0, 1, 1, 2],
                           [1, 0, 2, 1]], dtype=torch.long)
x = torch.tensor([[-1], [0], [1]], dtype=torch.float)

data = Data(x=x, edge_index=edge_index)
>>> Data(edge_index=[2, 4], x=[3, 1])
```

edge_index：指明了图的边信息，大小为[2,edge_nums]

x:指明了每个节点的特征向量，大小为[node_nums,feature_dim]

![](https://pytorch-geometric.readthedocs.io/en/latest/_images/graph.svg)

**data的构建方式二：[edge_nums,2].t().contiguous() + [node_nums,feature_dim]**

```python
import torch
from torch_geometric.data import Data

edge_index = torch.tensor([[0, 1],
                           [1, 0],
                           [1, 2],
                           [2, 1]], dtype=torch.long)
x = torch.tensor([[-1], [0], [1]], dtype=torch.float)

data = Data(x=x, edge_index=edge_index.t().contiguous())
>>> Data(edge_index=[2, 4], x=[3, 1])
```

### Data的属性

- `data.x`: 节点node的特性向量矩阵 ，大小为`[num_nodes, num_node_features]`
- `data.edge_index`: source to target 边edge矩阵 `[2, num_edges]``torch.long`
- `data.edge_attr`: 边Edge的特征向量矩阵 ，大小为`[num_edges, num_edge_features]`
- `data.y`: 对于node-level targets，其大小为。对于 graph-level targets ,其大小为 `[num_nodes, *]``[1, *]` 

> 1. **Node-level targets (节点级别目标):** 这里提到的 `data.y` 可以是节点级别的目标。在这种情况下，每个节点都有一个对应的目标值。目标值可以具有任意形状，这意味着它可以是一个标量（例如节点的类别标签），也可以是一个向量或矩阵（例如节点的属性向量）。`data.y` 的形状可能是 `[num_nodes, *]`，其中 `num_nodes` 是图中节点的数量，`*` 表示任意维度。例如，如果每个节点的目标是一个长度为 3 的向量，那么 `data.y` 的形状可能是 `[num_nodes, 3]`。
> 2. **Graph-level targets (图级别目标):** 另一种情况是，`data.y` 可以是图级别的目标。这意味着整个图有一个对应的目标值。同样，目标值可以具有任意形状。在这种情况下，形状可能是 `[1, *]`，其中 `*` 表示任意维度。例如，如果你的图代表一个文档，目标可以是文档的主题或情感标签，形状可能是 `[1, num_topics]`，其中 `num_topics` 是主题的数量。
>
> 总之，`data.y` 是图数据对象中用于存储目标信息的属性。它可以表示节点级别或图级别的目标，具体形状取决于任务的要求。在训练图神经网络或其他图数据相关的模型时，你可以使用 `data.y` 来指导模型的训练和优化过程。

- `data.pos`: 存储节点的坐标，形状是`[num_nodes, num_dimensions]`

> `data.pos` 是图数据对象中的一个属性，用于存储节点在空间中的位置信息，形状为 `[num_nodes, num_dimensions]`。
>
> - `data.pos`：表示图数据对象中的节点位置矩阵。
> - `num_nodes`：图中节点的数量。
> - `num_dimensions`：表示每个节点的位置在多少维度上进行描述，通常为 2 或 3。
>
> 这意味着对于一个包含多个节点的图，`data.pos` 是一个矩阵，其形状为 `[num_nodes, num_dimensions]`。在二维情况下，每一行表示一个节点的位置，而列则对应于该位置的 x 和 y 坐标。在三维情况下，每一行表示一个节点的位置，而列则对应于 x、y 和 z 坐标。
>
> 通常情况下，`data.pos` 的信息用于表示节点在某个空间中的位置，例如在二维空间中的坐标或三维空间中的坐标。这在许多图数据任务中都是有用的，比如可视化图、空间分析等。

* `data.face` 属性来存储三维网格中三角形的连接信息

> `data.face` 属性用于存储三维网格中三角形的连接信息。该张量的形状为 `[3, num_faces]`，其中 `num_faces` 表示网格中三角形的数量。张量中的每一列对应一个三角形的顶点，而形状中的 `3` 表示每个三角形由三个顶点索引定义。

* `data` 对象可以具有 `train_mask`、`val_mask` 和 `test_mask` 等属性

> 这三个属性用于指示哪些节点被用于训练、验证和测试。这些属性通常在图神经网络模型的训练、验证和测试过程中用于节点的划分和选择。这些布尔掩码属性可以用于根据你的训练、验证和测试需求，对图数据中的节点进行划分。在模型训练、验证和测试过程中，你可以使用这些属性来选择适当的节点，以便进行相应的操作和计算。这在图神经网络的实验和评估中非常有用
>
> 这些属性是布尔掩码（Boolean mask）数组，其长度与图中的节点数量相同。每个元素的值指示对应节点是否在对应的集合中（例如，训练集、验证集、测试集）。
>
> - **`train_mask`：** 用于指示哪些节点被用于模型的训练。对于训练集中的节点，相应位置的元素值为 `True`，否则为 `False`。
> - **`val_mask`：** 用于指示哪些节点被用于模型的验证。对于验证集中的节点，相应位置的元素值为 `True`，否则为 `False`。
> - **`test_mask`：** 用于指示哪些节点被用于模型的测试。对于测试集中的节点，相应位置的元素值为 `True`，否则为 `False`。

```python
data = ...  # 创建你的 Data 对象

# 指定哪些节点用于训练、验证和测试
data.train_mask = torch.tensor([True, True, False, False, True, False, ...])  # 训练集
data.val_mask = torch.tensor([False, False, True, True, False, False, ...])   # 验证集
data.test_mask = torch.tensor([False, False, False, False, False, True, ...]) # 测试集
```

查看被标记为训练、验证、测试的节点数量

```python
data.train_mask.sum().item() # 计算训练集中被标记为 `True` 的节点数量
>>> 140

data.val_mask.sum().item() # 计算验证集中被标记为 `True` 的节点数量
>>> 500

data.test_mask.sum().item() # 计算测试集中被标记为 `True` 的节点数量
>>> 1000
```

训练，测试，验证时，怎么使用`train_mask`,`val_mask`,`test_mask`

模型假设定义如下

```python
import torch
import torch.nn.functional as F
from torch_geometric.data import DataLoader
from torch_geometric.nn import GCNConv

# 假设 data 是图数据对象，包含 x（节点特征）和 train_mask,test_mask ,val_mask 
data = ...

# 创建一个图神经网络模型
class GCN(torch.nn.Module):
    def __init__(self):
        super(GCN, self).__init__()
        self.conv1 = GCNConv(in_channels=data.num_features, out_channels=16)
        self.conv2 = GCNConv(in_channels=16, out_channels=data.num_classes)

    def forward(self, x, edge_index):
        x = self.conv1(x, edge_index)
        x = F.relu(x)
        x = F.dropout(x, training=self.training)
        x = self.conv2(x, edge_index)
        return F.log_softmax(x, dim=1)

model = GCN()
```

`train_mask`用法

```python
model = GCN()
# 设置模型为训练模式
model.train()

# 将数据送入模型进行训练
optimizer = torch.optim.Adam(model.parameters(), lr=0.01)
optimizer.zero_grad()

out = model(data.x, data.edge_index)

# 使用 train_mask 选择训练节点
loss = F.nll_loss(out[data.train_mask], data.y[data.train_mask])
loss.backward()
optimizer.step()
```

`test_mask`用法

```python
model = GCN()

# 设置模型为评估模式（不使用 dropout）
model.eval()

# 将数据送入模型进行验证
out = model(data.x, data.edge_index)

# 使用 val_mask 选择验证节点
loss = F.nll_loss(out[data.val_mask], data.y[data.val_mask])

# 进行后续的性能评估、超参数调整等操作
```

`val_mask`用法

```python
model = GCN()

# 设置模型为评估模式（不使用 dropout）
model.eval()

# 将数据送入模型进行验证
out = model(data.x, data.edge_index)

# 使用 val_mask 选择验证节点
loss = F.nll_loss(out[data.val_mask], data.y[data.val_mask])

# 进行后续的性能评估、超参数调整等操作
```

`model.train()` 和 `model.eval()` 方法是pytorch中的标准方法

> 在 PyTorch Geometric 中，通过调用 `model.train()` 和 `model.eval()` 方法，你可以将模型设置为训练模式和评估模式，分别用于训练和测试阶段。**这些方法是 PyTorch 中的标准方法**，用于启用或禁用某些模型层（如 dropout 或 batch normalization）中的特定行为。

### Data的其他用法

`data.to(device)`把data放到GPU上跑

```python
device = torch.device('cuda')
data = data.to(device)
```

`data.validate(raise_on_error=True) `检查图的合法性

>在 PyTorch Geometric（PyG）库中，`Data` 对象具有一个名为 `validate` 的方法，用于验证数据对象的一致性和有效性。这个方法可以帮助你确保你的数据对象的各个属性设置正确，以便在使用图神经网络和其他模型时不会出现错误。
>
>具体来说，当你调用 `data.validate()` 时，PyG 将检查 `Data` 对象的各个属性是否满足一些常见的合法性要求。如果任何属性不符合预期，根据 `raise_on_error` 参数的值，它会抛出一个异常（如果为 `True`）或者仅输出一条警告消息（如果为 `False`，这是默认值）。

```python
data = ...  # 创建你的 Data 对象
data.validate(raise_on_error=True)  # 验证并在错误时引发异常
```

其他用法

```python
print(data.keys())
>>> ['x', 'edge_index']

print(data['x'])
>>> tensor([[-1.0],
            [0.0],
            [1.0]])

for key, item in data:
    print(f'{key} found in data')
>>> x found in data
>>> edge_index found in data

'edge_attr' in data
>>> False

data.num_nodes # 节点数量
>>> 3

data.num_edges # 边数量
>>> 4

data.num_node_features #节点的特征维度
>>> 1

data.has_isolated_nodes() #检查图数据对象中是否存在孤立节点（isolated nodes）
>>> False

data.has_self_loops() #检查图数据对象是否包含自环（self-loops）
>>> False

data.is_directed() # 检查图是否为有向图
>>> False
data.is_undirected()# 检查图是否为无向图
>>> True

```

## torch_geometric.datasets



### 使用自带数据集

pyg自带的数据集有五大类数据集:Homogeneous Datasets, Heterogeneous Datasets ,Synthetic Datasets ,Graph Generators ,Motif Generators

> 1. **Homogeneous Datasets (同质数据集):** 同质数据集是指其中所有节点和边都属于相同的类型。例如，在社交网络中，如果所有的节点都表示用户，而所有的边表示关注关系，那么这就是一个同质数据集。这意味着数据集中的节点和边之间没有明显的类型差异。
> 2. **Heterogeneous Datasets (异质数据集):** 异质数据集是指其中节点和边可以属于不同的类型。例如，在一个学术网络中，节点可能表示作者、论文和会议，而边可能表示作者-论文和论文-会议的关系。这种数据集反映了图中的实体之间的不同类型关联。
> 3. **Synthetic Datasets (合成数据集):** 合成数据集是通过模拟或人工创建的数据集，用于测试和评估算法。这些数据集不来自真实世界，而是根据特定规则或模型生成的。合成数据集可用于验证算法的性能、稳定性和适用性。
> 4. **Graph Generators (图生成器):** 图生成器是用于生成图数据的算法或工具。这些生成器可以创建不同类型的图，如随机图、小世界图、无标度网络等。常见的图生成器包括 Erdős-Rényi 模型、Watts-Strogatz 模型、Barabási-Albert 模型等。
> 5. **Motif Generators (模体生成器):** 模体是图中的小型子图模式，具有特定的拓扑结构和功能。模体生成器是用于生成这些小型子图的工具。模体在图分析中很有用，因为它们可以帮助揭示图中的重要模式和结构。
>
> 综上所述，这些术语涵盖了不同类型的图数据集和生成方法。同质和异质数据集之间的区别在于实体之间的类型关系。合成数据集是人工创建的，用于测试算法。图生成器可以自动生成图，而模体生成器则用于生成图中的模体，以揭示图的局部结构。

 

An initialization of a dataset **will automatically download its raw files and process them to the previously described [`Data`](https://pytorch-geometric.readthedocs.io/en/latest/generated/torch_geometric.data.Data.html#torch_geometric.data.Data) format**. *E.g.*, to load the ENZYMES dataset (consisting of 600 graphs within 6 classes), type:

```python
from torch_geometric.datasets import TUDataset

dataset = TUDataset(root='/tmp/ENZYMES', name='ENZYMES')
>>> ENZYMES(600)

len(dataset) # dataset的长度是指图的数量
>>> 600

dataset.num_classes # 图的类数
>>> 6

dataset.num_node_features # 图的节点特征数
>>> 3
```

另一个例子

```python
from torch_geometric.datasets import Planetoid
dataset = Planetoid(root="tutorial1",name= "Cora")
data = dataset[0]

print(dataset)
>>> Cora()
print("number of graphs:\t\t",len(dataset))
>>> number of graphs:		 1
print("number of classes:\t\t",dataset.num_classes)
>>> number of classes:		 7
print("number of node features:\t",dataset.num_node_features)
>>> number of node features:	 1433
print("number of edge features:\t",dataset.num_edge_features)
>>> number of edge features:	 0
```

### 创建一个自己的数据集

在官方文档中有链接（点进dataset，点next到下一页就能看到），可以看到每个数据集的子类，要么继承于`InMemoryDataset`，要么继承于`torch_geometric.data.Dataset`。后者需要比前者额外实现len和get函数。

- InMemoryDataset：使用这个`Dataset`会一次性把数据全部加载到内存中。
- Dataset: 使用这个`Dataset`每次加载一个数据到内存中，比较常用。



1. **ImMemory的实现**

```python
import torch
from torch_geometric.data import InMemoryDataset


class MyOwnDataset(InMemoryDataset):
    def __init__(self, root, transform=None, pre_transform=None):
        super(MyOwnDataset, self).__init__(root, transform, pre_transform)
        # 在此初始化代码中，你可以指定数据集的根目录、数据的变换和预处理方式
        self.data, self.slices = torch.load(self.processed_paths[0])

    @property
    def raw_file_names(self):
        # 返回未经处理的原始数据文件名列表
        return ['some_file_1', 'some_file_2', ...]

    @property
    def processed_file_names(self):
        # 返回已处理的数据文件名列表
        return ['data.pt']

    def download(self):
        # 在此方法中，实现从网络下载数据集的逻辑（如果本地有可以不实现这个）
        # Download to `self.raw_dir`.

    def process(self):
        # 在此方法中，实现数据的处理和转换为图数据对象的逻辑
        # Read data into huge `Data` list.
        data_list = [...]

        if self.pre_filter is not None:
            data_list = [data for data in data_list if self.pre_filter(data)]

        if self.pre_transform is not None:
            data_list = [self.pre_transform(data) for data in data_list]

        data, slices = self.collate(data_list)
        torch.save((data, slices), self.processed_paths[0])

```

* `raw_file_names()`这个函数给出多张graph所存的路径，假设有graph a，graph b，那么这里return的就应当是两幅图对应的文件名。
* `processed_file_names()`写处理所有graph过后所存的路径，道理同raw_file_names
* `process()`处理数据，成**规定格式**。所谓规定格式，就是`from torch_geometric.data import Data`这个Data类型，就是你要处理成的格式。
* `download()`写怎么获得raw的dataset，显然我们要自定义数据集，往往是在本地就有的，这个可以直接pass return

2. **Dataset的实现：比InMemoryDataset多实现两个函数**

```python
from torch_geometric.data import Dataset

class MyCustomDataset(Dataset):
    def __init__(self, root, transform=None, pre_transform=None):
        super(MyOwnDataset, self).__init__(root, transform, pre_transform)
        # 在此初始化代码中，你可以指定数据集的根目录、数据的变换和预处理方式
        self.data, self.slices = torch.load(self.processed_paths[0])

    @property
    def raw_file_names(self):
        # 返回未经处理的原始数据文件名列表
        return ['some_file_1', 'some_file_2', ...]

    @property
    def processed_file_names(self):
        # 返回已处理的数据文件名列表
        return ['data.pt']

    def download(self):
        # 在此方法中，实现从网络下载数据集的逻辑（如果本地有可以不实现这个）
        # Download to `self.raw_dir`.

    def process(self):
        # 在此方法中，实现数据的处理和转换为图数据对象的逻辑
        # Read data into huge `Data` list.
        data_list = [...]

        if self.pre_filter is not None:
            data_list = [data for data in data_list if self.pre_filter(data)]

        if self.pre_transform is not None:
            data_list = [self.pre_transform(data) for data in data_list]

        data, slices = self.collate(data_list)
        torch.save((data, slices), self.processed_paths[0])
        
    #多的两个函数
	def len(self):
        # 返回数据集中的图数量
        return len(self.processed_file_names)

    def get(self, idx):
        # 返回指定索引处的图数据对象
        data = torch.load(osp.join(self.processed_dir, 'data_{}.pt'.format(idx)))
        return data
```

**首先**会判断，在参数root路径下存不存在raw和processed两个路径，raw中存不存在raw_paths中给出的raw data，如果没有，他会调用down_load函数给你下载。想要不进行download，那么要保证raw文件里一定存在你raw path里面返回的文件名。

这里举个例子：
假设我要使用现有的AMiner这个数据集，但是数据是dropbox的，网上不了。我要手动下下来之后使用。
首先我确定这个数据集存在哪里。然后，在这个目录新建dir AMiner，在AMiner下面新建raw和processed，如图：

![在这里插入图片描述](https://gitee.com/yushen611/img/raw/master/20210407161204577.png)

然后进入源码查看所需的raw有什么：

![](https://gitee.com/yushen611/img/raw/master/20210407161204577.png)

然后通过download函数，找到url，下载（并解压）后，全部放到raw那个目录里面。

![在这里插入图片描述](https://gitee.com/yushen611/img/raw/master/20210407161422155.png)

**注意：如果没有全部放进去，那么代码会把raw目录整个删除！**
接下来的就可以直接使用了，process函数也会把数据存入processed，往后不再赘述。

3. 代码例子

* 自己实现的例子

```python
import torch
import pickle
from torch_geometric.data import InMemoryDataset, Data

class TCMDataSet(InMemoryDataset):
    def __init__(self,root,name,feature_size,transform=None,pre_transform=None):
        self.feature_size=feature_size
        print(f'feature size: {feature_size}')

        super(TCMDataSet, self).__init__(root, transform, pre_transform)
        self.data, self.slices = torch.load(self.processed_paths[0])

    @property
    def raw_file_names(self):
        return ['tcm_dataset.pt',]

    @property
    def processed_file_names(self):
        return ['tcm_dataset.pt',]

    def download(self):
        pass

    def process(self):

        # do processing, get x, y, edge_index ready.   

        graph=Data(x=features,edge_index=network.t().contiguous(),y=labels)
        train_idx = torch.tensor([id2inter_id[idx] for idx in herb_with_label_id], dtype=torch.long)
        #加入新的属性
        graph.train_idx = train_idx

        if self.pre_filter is not None:
            graph = [data for data in graph if self.pre_filter(data)]

        if self.pre_transform is not None:
            graph = [self.pre_transform(data) for data in graph]

        data, slices = self.collate([graph])
        torch.save((data, slices), self.processed_paths[0])

```

* 官方文档的例子

```py
import os
import os.path as osp
import shutil
from typing import Callable, List, Optional

import torch

from torch_geometric.data import (
    Data,
    InMemoryDataset,
    download_url,
    extract_zip,
)
from torch_geometric.io import read_tu_data


[docs]class TUDataset(InMemoryDataset):
    r"""A variety of graph kernel benchmark datasets, *.e.g.*,
    :obj:`"IMDB-BINARY"`, :obj:`"REDDIT-BINARY"` or :obj:`"PROTEINS"`,
    collected from the `TU Dortmund University
    <https://chrsmrrs.github.io/datasets>`_.
    In addition, this dataset wrapper provides `cleaned dataset versions
    <https://github.com/nd7141/graph_datasets>`_ as motivated by the
    `"Understanding Isomorphism Bias in Graph Data Sets"
    <https://arxiv.org/abs/1910.12091>`_ paper, containing only non-isomorphic
    graphs.

    .. note::
        Some datasets may not come with any node labels.
        You can then either make use of the argument :obj:`use_node_attr`
        to load additional continuous node attributes (if present) or provide
        synthetic node features using transforms such as
        :class:`torch_geometric.transforms.Constant` or
        :class:`torch_geometric.transforms.OneHotDegree`.

    Args:
        root (str): Root directory where the dataset should be saved.
        name (str): The `name
            <https://chrsmrrs.github.io/datasets/docs/datasets/>`_ of the
            dataset.
        transform (callable, optional): A function/transform that takes in an
            :obj:`torch_geometric.data.Data` object and returns a transformed
            version. The data object will be transformed before every access.
            (default: :obj:`None`)
        pre_transform (callable, optional): A function/transform that takes in
            an :obj:`torch_geometric.data.Data` object and returns a
            transformed version. The data object will be transformed before
            being saved to disk. (default: :obj:`None`)
        pre_filter (callable, optional): A function that takes in an
            :obj:`torch_geometric.data.Data` object and returns a boolean
            value, indicating whether the data object should be included in the
            final dataset. (default: :obj:`None`)
        use_node_attr (bool, optional): If :obj:`True`, the dataset will
            contain additional continuous node attributes (if present).
            (default: :obj:`False`)
        use_edge_attr (bool, optional): If :obj:`True`, the dataset will
            contain additional continuous edge attributes (if present).
            (default: :obj:`False`)
        cleaned (bool, optional): If :obj:`True`, the dataset will
            contain only non-isomorphic graphs. (default: :obj:`False`)

    **STATS:**

    .. list-table::
        :widths: 20 10 10 10 10 10
        :header-rows: 1

        * - Name
          - #graphs
          - #nodes
          - #edges
          - #features
          - #classes
        * - MUTAG
          - 188
          - ~17.9
          - ~39.6
          - 7
          - 2
        * - ENZYMES
          - 600
          - ~32.6
          - ~124.3
          - 3
          - 6
        * - PROTEINS
          - 1,113
          - ~39.1
          - ~145.6
          - 3
          - 2
        * - COLLAB
          - 5,000
          - ~74.5
          - ~4914.4
          - 0
          - 3
        * - IMDB-BINARY
          - 1,000
          - ~19.8
          - ~193.1
          - 0
          - 2
        * - REDDIT-BINARY
          - 2,000
          - ~429.6
          - ~995.5
          - 0
          - 2
        * - ...
          -
          -
          -
          -
          -
    """

    url = 'https://www.chrsmrrs.com/graphkerneldatasets'
    cleaned_url = ('https://raw.githubusercontent.com/nd7141/'
                   'graph_datasets/master/datasets')

    def __init__(self, root: str, name: str,
                 transform: Optional[Callable] = None,
                 pre_transform: Optional[Callable] = None,
                 pre_filter: Optional[Callable] = None,
                 use_node_attr: bool = False, use_edge_attr: bool = False,
                 cleaned: bool = False):
        self.name = name
        self.cleaned = cleaned
        super().__init__(root, transform, pre_transform, pre_filter)

        out = torch.load(self.processed_paths[0])
        if not isinstance(out, tuple) or len(out) != 3:
            raise RuntimeError(
                "The 'data' object was created by an older version of PyG. "
                "If this error occurred while loading an already existing "
                "dataset, remove the 'processed/' directory in the dataset's "
                "root folder and try again.")
        data, self.slices, self.sizes = out
        self.data = Data.from_dict(data) if isinstance(data, dict) else data

        if self._data.x is not None and not use_node_attr:
            num_node_attributes = self.num_node_attributes
            self._data.x = self._data.x[:, num_node_attributes:]
        if self._data.edge_attr is not None and not use_edge_attr:
            num_edge_attrs = self.num_edge_attributes
            self._data.edge_attr = self._data.edge_attr[:, num_edge_attrs:]

    @property
    def raw_dir(self) -> str:
        name = f'raw{"_cleaned" if self.cleaned else ""}'
        return osp.join(self.root, self.name, name)

    @property
    def processed_dir(self) -> str:
        name = f'processed{"_cleaned" if self.cleaned else ""}'
        return osp.join(self.root, self.name, name)

    @property
    def num_node_labels(self) -> int:
        return self.sizes['num_node_labels']

    @property
    def num_node_attributes(self) -> int:
        return self.sizes['num_node_attributes']

    @property
    def num_edge_labels(self) -> int:
        return self.sizes['num_edge_labels']

    @property
    def num_edge_attributes(self) -> int:
        return self.sizes['num_edge_attributes']

    @property
    def raw_file_names(self) -> List[str]:
        names = ['A', 'graph_indicator']
        return [f'{self.name}_{name}.txt' for name in names]

    @property
    def processed_file_names(self) -> str:
        return 'data.pt'

    def download(self):
        url = self.cleaned_url if self.cleaned else self.url
        folder = osp.join(self.root, self.name)
        path = download_url(f'{url}/{self.name}.zip', folder)
        extract_zip(path, folder)
        os.unlink(path)
        shutil.rmtree(self.raw_dir)
        os.rename(osp.join(folder, self.name), self.raw_dir)

    def process(self):
        self.data, self.slices, sizes = read_tu_data(self.raw_dir, self.name)

        if self.pre_filter is not None or self.pre_transform is not None:
            data_list = [self.get(idx) for idx in range(len(self))]

            if self.pre_filter is not None:
                data_list = [d for d in data_list if self.pre_filter(d)]

            if self.pre_transform is not None:
                data_list = [self.pre_transform(d) for d in data_list]

            self.data, self.slices = self.collate(data_list)
            self._data_list = None  # Reset cache.

        torch.save((self._data.to_dict(), self.slices, sizes),
                   self.processed_paths[0])

    def __repr__(self) -> str:
        return f'{self.name}({len(self)})'
```

### dataset的打乱与划分

打乱数据集

```python
# 方法1
dataset = dataset.shuffle()
>>> ENZYMES(600)
# 方法2 （两种方法等价）
perm = torch.randperm(len(dataset))
dataset = dataset[perm]
>> ENZYMES(600)
```

数据集的划分

```python
train_dataset = dataset[:540]
>>> ENZYMES(540)

test_dataset = dataset[540:]
>>> ENZYMES(60)
```

## torch_geometric.loader.DataLoader

`torch_geometric.loader.DataLoader` 是 PyTorch Geometric 中用于加载图数据的数据加载器。它可以帮助你有效地将图数据转换成批量数据，用于模型的训练、验证和测试。

`torch_geometric.data.Batch`继承自`torch_geometric.data.Data`，并且多了一个属性：`batch`。

`batch` 属性**实际上是一个整数列向量**，其中的每个元素对应于批次中的每个节点或边所属的图批次编号。

`batch` is a column vector which maps each node to its respective graph in the batch:

​				$batch = {[0\  ...\  0 \ 1 \ ... \ n-2 \ n-1 ... n-1]}^T$ 

实际使用的时候，直接`x=batch.x`,`y=batch.y`,`edge_index = batch.edge_index`，把`batch`当作`data`用就行了

如果需要**对图进行特征的聚合**时，可以如下使用

> 特征的聚合操作在图神经网络的模型中是需要的，但通常在模型的前向传播过程中自动处理。你在代码中手动进行的特征聚合操作可能是为了数据预处理或者特定的实验需要。

```python
from torch_scatter import scatter_mean
from torch_geometric.datasets import TUDataset
from torch_geometric.data import DataLoader

dataset = TUDataset(root='/tmp/ENZYMES', name='ENZYMES', use_node_attr=True)
loader = DataLoader(dataset, batch_size=32, shuffle=True)

for data in loader:
    data
    #data: Batch(batch=[1082], edge_index=[2, 4066], x=[1082, 21], y=[32])

    x = scatter_mean(data.x, data.batch, dim=0)
    # x.size(): torch.Size([32, 21])
```

> 在图神经网络中，节点特征通常是节点的属性或特征向量。当你有一个包含多个图数据的批次时，每个图数据中的节点数可能不同。然而，模型需要每个图的一个固定大小的表示。
>
> 为了解决这个问题，通常会进行特征聚合操作，将每个图中的节点特征聚合成一个图级别的特征。在你的代码中，`scatter` 函数用于进行这种特征聚合操作。
>
> - `data.x` 是图批次中所有节点的特征矩阵，大小为 `[总节点数, 特征维度]`。
> - `data.batch` 是一个包含每个节点所属图批次索引的张量，大小为 `[总节点数]`。它用于标识每个节点属于哪个图批次。
> - `dim=0` 表示在第一个维度上进行聚合，也就是按照不同的图批次进行聚合。
> - `reduce='mean'` 表示采用平均值进行聚合操作。
>
> 因此，`x = scatter(data.x, data.batch, dim=0, reduce='mean')` 这行代码的作用是将每个图批次中的节点特征按照平均值进行聚合，生成一个图级别的特征表示。这样，每个图数据都有一个固定大小的特征向量，可以输入到图神经网络中进行后续的图级别计算。
>
> 最终，`x` 的大小为 `[图批次数量, 特征维度]`，其中图批次数量为 32（即你的批次大小），特征维度为 21。这就是每个图数据经过特征聚合后的图级别特征表示。

### DataLoader使用实例

```python
import torch
import torch.optim as optim
import torch.nn.functional as F
from torch_geometric.loader import DataLoader

# 假设 dataset 是一个包含图数据的数据集对象
dataset = ...

# 创建 DataLoader 实例
batch_size = 64
dataloader = DataLoader(dataset, batch_size=batch_size, shuffle=True)

# 定义损失函数
loss_fn = torch.nn.CrossEntropyLoss()
# 定义优化器
optimizer = optim.Adam(model.parameters(), lr=0.01)

# 设置模型为训练模式
model.train()
# 在训练循环中使用 DataLoader
for batch in dataloader:
    
    batch# >>> DataBatch(batch=[1082], edge_index=[2, 4066], x=[1082, 21], y=[32])
    batch.num_graphs# >>> 32
    # 在这里执行模型的前向传播、损失计算、反向传播等操作
    optimizer.zero_grad()  # 梯度归零

    # 获取批次的数据
    x = batch.x
    edge_index = batch.edge_index
    y = batch.y
    # 还有batch.num_graphs

    # 进行模型的前向传播
    output = model(x, edge_index)

    # 计算损失
    loss = loss_fn(output, y)

    # 反向传播和参数更新
    loss.backward()
    optimizer.step()

    # 打印损失
    print("Batch Loss:", loss.item())
```



`torch_geometric.loader.DataLoader`与 `torch.utils.data.DataLoader`的不同

> - `torch_geometric.loader.DataLoader`：专门处理图数据对象，可以将图数据有效地组织成批次，同时提供了对图数据预处理、转换和增强的支持。
> - `torch.utils.data.DataLoader`：主要处理传统数据，不提供图数据的特殊支持。



# 模型基础训练实例

## node classify

### step1:环境代码配置

```python
## 检查torch版本以及根据cuda确认device
import os
import torch
os.environ['TORCH'] = torch.__version__
print(torch.__version__)

use_cuda_if_available = True
device = torch.device('cuda' if torch.cuda.is_available() and use_cuda_if_available else 'cpu')
print(device)
```



```python
# 下载库
!pip install -q torch-scatter -f https://data.pyg.org/whl/torch-${TORCH}.html
!pip install -q torch-sparse -f https://data.pyg.org/whl/torch-${TORCH}.html
!pip install -q git+https://github.com/pyg-team/pytorch_geometric.git
```

### step2:数据集加载

```python
# 导入依赖
import torch_geometric
from torch_geometric.datasets import Planetoid
```



```python
#导入数据集
dataset = Planetoid(root="tutorial1",name= "Cora")
```



```python
# 检查dataset的属性：dataset中<图的数量>、<分类的数量>、<节点的特征维度>、<边的特征维度>
print(dataset)  # Cora()
print("number of graphs:\t\t",len(dataset)) # number of graphs:		 1
print("number of classes:\t\t",dataset.num_classes)# number of classes:		 7
print("number of node features:\t",dataset.num_node_features)# number of node features:	 1433
print("number of edge features:\t",dataset.num_edge_features)# number of edge features:	 0
```



```python
#检查data的属性：图的必备属性[edge_index边的大小，节点特征向量x的大小]，其他属性：[y的大小，train_mask]
print(dataset.data) # Data(x=[2708, 1433], edge_index=[2, 10556], y=[2708], train_mask=[2708], val_mask=[2708], test_mask=[2708])
print("edge_index:\t\t",dataset.data.edge_index.shape)
print(dataset.data.edge_index)
print("\n")
print("train_mask:\t\t",dataset.data.train_mask.shape)
print(dataset.data.train_mask)
print("\n")
print("x:\t\t",dataset.data.x.shape)
print(dataset.data.x)
print("\n")
print("y:\t\t",dataset.data.y.shape)
print(dataset.data.y)
```

### step3:写模型结构代码

```python
import torch
import torch.nn.functional as F
from torch_geometric.nn import SAGEConv
```



```python
class Net(torch.nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        
        self.conv = SAGEConv(dataset.num_features,
                             dataset.num_classes,
                             aggr="max") # max, mean, add ...)

    def forward(self):
        x = self.conv(data.x, data.edge_index)
        return F.log_softmax(x, dim=1)
```



### step4:写训练的前置代码



```python
# 定义模型对象，优化器对象，损失函数
model = Net().to(device)
optimizer = torch.optim.Adam(model.parameters(), lr=0.01, weight_decay=5e-4)
loss_fn = torch.nn.NLLLoss()
```



* 损失函数说明：`torch.nn.NLLLoss()` VS `torch.nn.CrossEntropyLoss()`

> 结论：直接用nn.CrossEntropy和nn.LogSoftmax+nn.NLLLoss是一样的结果
>
> ```python
> import torch
> import torch.nn as nn
>  
> a = torch.Tensor([[1,2,3]])
> target = torch.Tensor([2]).long()
> logsoftmax = nn.LogSoftmax()
> ce = nn.CrossEntropyLoss()
> nll = nn.NLLLoss()
>  
> # 测试CrossEntropyLoss
> cel = ce(a,target)
> print(cel)
> # 输出：tensor(0.4076)
>  
> # 测试LogSoftmax+NLLLoss
> lsm_a = logsoftmax(a)
> nll_lsm_a = nll(lsm_a,target)
> # 输出tensor(0.4076)
> ```
>
> 这两个损失函数都是用于多分类任务的，如果最后输出是softmax层，就要用NLLLoss了
>
> [参考博客link]([torch.nn.NLLLoss()与torch.nn.CrossEntropyLoss()_torch.nn.nllloss weight_我是天才很好的博客-CSDN博客](https://blog.csdn.net/weixin_43593330/article/details/113505657))



```python
# 定义用于训练的train函数，以及用于测试的test函数
def train():
    model.train()
    optimizer.zero_grad()
    logits = model()
    loss = loss_fn(logits[data.train_mask], data.y[data.train_mask])
    loss.backward()
    optimizer.step()

def test():
    model.eval()
    logits, accs = model(), []
    for _, mask in data('train_mask', 'val_mask', 'test_mask'):
        pred = logits[mask].max(1)[1]
        acc = pred.eq(data.y[mask]).sum().item() / mask.sum().item()
        accs.append(acc)
    return accs

```

> 说明
>
> 1. `model()`:执行 `model()` 时，实际上是在调用模型对象的 `forward` 方法。这将触发模型对输入数据的计算和转换，最终得到模型的输出（也称为预测结果或logits）
> 2. `logits`:通常指代模型在多类别分类问题中，各个类别的分数或原始输出,**表示模型在没有经过激活函数处理之前的原始输出值**。这些分数可以通过 softmax 函数进行归一化，得到每个类别的概率。在某些情况下，"logits" 也可能指代回归问题中模型的原始输出。

### step5:训练模型并记录

```python
best_val_acc = test_acc = 0
for epoch in range(1,100):
    train()
    _, val_acc, tmp_test_acc = test()
    if val_acc > best_val_acc:
        best_val_acc = val_acc
        test_acc = tmp_test_acc
    log = 'Epoch: {:03d}, Val: {:.4f}, Test: {:.4f}'
    
    if epoch % 10 == 0:
        print(log.format(epoch, best_val_acc, test_acc))
```

### ste6:保存,加载模型并预测

保存模型

```python
import torch

# 假设你的模型是一个 PyTorch 模型实例，例如 model = Net()
# model 的结构和参数会被保存到指定的文件
torch.save(model.state_dict(), 'model.pth')
```

加载模型

```python
import torch
from your_model_module import Net  # 导入你的模型类

# 创建模型实例
model = Net()

# 加载之前保存的模型参数
model.load_state_dict(torch.load('model.pth'))

# 将模型设置为评估模式（如果是用于推理）
model.eval()

```

预测

```python
# 准备输入数据
x, edge_index = data.x.to(device), data.edge_index.to(device) # device一般为'cuda'或'cpu'

# 进行预测
with torch.no_grad():
    output = model(x, edge_index) # 这里需要看具体forward()函数怎么定义的
    predictions = output.argmax(dim=1)

# 将结果移动回 CPU，如果需要
predictions = predictions.cpu()

# 打印预测结果
print(predictions)
```

