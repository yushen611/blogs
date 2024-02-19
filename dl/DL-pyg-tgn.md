---
title: 'tgn基础'
date: 2023-08-26 16:32:34
tags: []
published: true
hideInList: true
feature: 
isTop: false
---
<meta name="referrer" content="no-referrer"/>

本文记录了学习tgn代码的过程。

学习的代码是pyg实现tgn的代码例子：[pytorch_geometric/examples/tgn.py at master · pyg-team/pytorch_geometric (github.com)](https://github.com/pyg-team/pytorch_geometric/blob/master/examples/tgn.py)

下面以工程的角度去拆分代码逻辑

# TGN原理(未完待续)



# TGN代码实现(pyg版本)

## step1:导入依赖

```python
import os.path as osp
import torch
from sklearn.metrics import average_precision_score, roc_auc_score
from torch.nn import Linear
from torch_geometric.datasets import JODIEDataset
from torch_geometric.loader import TemporalDataLoader
from torch_geometric.nn import TGNMemory, TransformerConv
from torch_geometric.nn.models.tgn import (
    IdentityMessage,
    LastAggregator,
    LastNeighborLoader,
)
```

1. `TemporalDataLoader`与`dataloader`的不同

> `TemporalDataLoader` 类是用于加载时序图数据的数据加载器。
>
> 时序图数据指的是图数据的时间序列版本，例如在社交网络中，节点和边可能随着时间的推移而变化。`TemporalDataLoader` 的目的是将这种时序图数据加载到 PyTorch 的张量中，以便进行训练和分析。

2. 时序图数据是什么

> 时序图数据是指在图结构中考虑了时间维度的图数据。**在时序图中，节点和边可能会随着时间的推移而变化**。下面是一个简单的示例，展示了一个时序图数据的数据结构：
>
> 假设我们有一个社交网络，其中用户是节点，他们之间的互动是边。我们将考虑不同时间点的图快照，其中**节点和边的状态可能随着时间而变化**。

## step2:导入数据集并划分

```python
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

path = osp.join(osp.dirname(osp.realpath(__file__)), '..', 'data', 'JODIE')
dataset = JODIEDataset(path, name='wikipedia')
data = dataset[0]

# For small datasets, we can put the whole dataset on GPU and thus avoid
# expensive memory transfer costs for mini-batches:
data = data.to(device)
```

1. 这里为什么要`data = dataset[0]`,因此这个dataset只有一个数据

![image-20230827183258533](https://gitee.com/yushen611/img/raw/master/image-20230827183258533.png)

2. `data`的类型是什么：`TemporalData`

<img src="https://gitee.com/yushen611/img/raw/master/image-20230827183618147.png" alt="image-20230827183618147" style="zoom:90%;" />

官方文档：[torch_geometric.data.TemporalData — pytorch_geometric documentation (pytorch-geometric.readthedocs.io)](https://pytorch-geometric.readthedocs.io/en/latest/generated/torch_geometric.data.TemporalData.html)

官方源码：[torch_geometric.data.temporal — pytorch_geometric documentation (pytorch-geometric.readthedocs.io)](https://pytorch-geometric.readthedocs.io/en/latest/_modules/torch_geometric/data/temporal.html)

```python
from torch import Tensor
from torch_geometric.data import TemporalData

events = TemporalData(
    src=Tensor([1,2,3,4]),
    dst=Tensor([2,3,4,5]),
    t=Tensor([1000,1010,1100,2000]),
    msg=Tensor([1,1,0,0])
)

# Add additional arguments to `events`:
events.y = Tensor([1,1,0,0])

# It is also possible to set additional arguments in the constructor
events = TemporalData(
    ...,
    y=Tensor([1,1,0,0])
)

# Get the number of events:
events.num_events
>>> 4

# Analyzing the graph structure:
events.num_nodes
>>> 5

# PyTorch tensor functionality:
events = events.pin_memory()
events = events.to('cuda:0', non_blocking=True)
```

参数：

- **src** ([*torch.Tensor*](https://pytorch.org/docs/master/tensors.html#torch.Tensor)*,* *optional*) – A list of source nodes for the events with shape `[num_events]`. (default: [`None`](https://docs.python.org/3/library/constants.html#None))
- **dst** ([*torch.Tensor*](https://pytorch.org/docs/master/tensors.html#torch.Tensor)*,* *optional*) – A list of destination nodes for the events with shape `[num_events]`. (default: [`None`](https://docs.python.org/3/library/constants.html#None))
- **t** ([*torch.Tensor*](https://pytorch.org/docs/master/tensors.html#torch.Tensor)*,* *optional*) – The timestamps for each event with shape `[num_events]`. (default: [`None`](https://docs.python.org/3/library/constants.html#None))
- **msg** ([*torch.Tensor*](https://pytorch.org/docs/master/tensors.html#torch.Tensor)*,* *optional*) – Messages feature matrix with shape `[num_events, num_msg_features]`. (default: [`None`](https://docs.python.org/3/library/constants.html#None))
- ***\*kwargs** (*optional*) – Additional attributes.

> ==个人理解==：
>
> 这个TemporalData就是在Data的基础上，加了一个t表示每个节点的时间戳。在TemporalData中**边与边之间的关**系称之为**事件**，**每个事件都有一个时间戳**。**事件的特征向量**被记为**msg**。如果事件有其他属性，还可以自行添加。

```python
# 数据集划分
train_data, val_data, test_data = data.train_val_test_split(
    val_ratio=0.15, test_ratio=0.15)

train_loader = TemporalDataLoader(
    train_data,
    batch_size=200,
    neg_sampling_ratio=1.0,
)
val_loader = TemporalDataLoader(
    val_data,
    batch_size=200,
    neg_sampling_ratio=1.0,
)
test_loader = TemporalDataLoader(
    test_data,
    batch_size=200,
    neg_sampling_ratio=1.0,
)
neighbor_loader = LastNeighborLoader(data.num_nodes, size=10, device=device)
```

* `TemporalDataLoader`是什么

源码：[torch_geometric.loader.temporal_dataloader — pytorch_geometric documentation (pytorch-geometric.readthedocs.io)](https://pytorch-geometric.readthedocs.io/en/latest/_modules/torch_geometric/loader/temporal_dataloader.html)

文档：[torch_geometric.loader — pytorch_geometric documentation (pytorch-geometric.readthedocs.io)](https://pytorch-geometric.readthedocs.io/en/latest/modules/loader.html)

>  `TemporalDataLoader`:A data loader which merges succesive events of a [`torch_geometric.data.TemporalData`](https://pytorch-geometric.readthedocs.io/en/latest/generated/torch_geometric.data.TemporalData.html#torch_geometric.data.TemporalData) to a mini-batch.



* `LastNeighborLoader`是什么

官方源码：[torch_geometric.loader.link_neighbor_loader — pytorch_geometric documentation (pytorch-geometric.readthedocs.io)](https://pytorch-geometric.readthedocs.io/en/latest/_modules/torch_geometric/loader/link_neighbor_loader.html)

官方文档：在官方文档页面有个`doc`，点击就能看到文档了[torch_geometric.loader — pytorch_geometric documentation (pytorch-geometric.readthedocs.io)](https://pytorch-geometric.readthedocs.io/en/latest/modules/loader.html#torch_geometric.loader.LinkNeighborLoader)

`LastNeighborLoader`是从节点为基础的 `torch_geometric.loader.NeighborLoader` 扩展而来。这个加载器**允许在无法进行完全批量训练的大规模图上进行小批量训练的操作**。

简单来说，这个加载器的**目标**是在**大规模图上进行训练时，通过对边进行采样，并构建邻居子图来实现小批量训练**。这使得**在无法进行完全批量训练的情况下**，仍然可以高效地进行图神经网络的训练。

## step3:创建模型及其组件

```python
class GraphAttentionEmbedding(torch.nn.Module):
    def __init__(self, in_channels, out_channels, msg_dim, time_enc):
        super().__init__()
        self.time_enc = time_enc
        edge_dim = msg_dim + time_enc.out_channels
        self.conv = TransformerConv(in_channels, out_channels // 2, heads=2,
                                    dropout=0.1, edge_dim=edge_dim)

    def forward(self, x, last_update, edge_index, t, msg):
        rel_t = last_update[edge_index[0]] - t
        rel_t_enc = self.time_enc(rel_t.to(x.dtype))
        edge_attr = torch.cat([rel_t_enc, msg], dim=-1)
        return self.conv(x, edge_index, edge_attr)


class LinkPredictor(torch.nn.Module):
    def __init__(self, in_channels):
        super().__init__()
        self.lin_src = Linear(in_channels, in_channels)
        self.lin_dst = Linear(in_channels, in_channels)
        self.lin_final = Linear(in_channels, 1)

    def forward(self, z_src, z_dst):
        h = self.lin_src(z_src) + self.lin_dst(z_dst)
        h = h.relu()
        return self.lin_final(h)


memory_dim = time_dim = embedding_dim = 100

memory = TGNMemory(
    data.num_nodes,
    data.msg.size(-1),
    memory_dim,
    time_dim,
    message_module=IdentityMessage(data.msg.size(-1), memory_dim, time_dim),
    aggregator_module=LastAggregator(),
).to(device)

gnn = GraphAttentionEmbedding(
    in_channels=memory_dim,
    out_channels=embedding_dim,
    msg_dim=data.msg.size(-1),
    time_enc=memory.time_enc,
).to(device)

link_pred = LinkPredictor(in_channels=embedding_dim).to(device)
```

总结这段代码，其实就是在干如下几件事：

1. 创建`GraphAttentionEmbedding`层，作为`gnn`（其实就是用了一个`TransformerConv`）
2. 创建`LinkPredictor`层,作为全连接层
3. 创建了一个`TGNMemory`对象

`TGNMemory`介绍（目前不是很懂原理）

>  `TGNMemory` 是 PyTorch Geometric（PyG）中的一个类，用于实现时间序列图神经网络（Time-Graph Neural Network，TGN）的记忆模块。TGN 是一种用于处理时间序列图数据的神经网络，它能够有效地对节点和边在时间上的演化进行建模。
>
> 记忆模块在 TGN 中扮演着重要的角色，它可以捕获节点和边的状态随时间的变化，从而帮助网络进行时间序列的预测和建模。`TGNMemory` 类的主要功能是维护图中节点的状态，以及它们在时间序列中的变化情况。
>
> 具体而言，`TGNMemory` 类可以执行以下功能：
>
> - 为每个节点维护一个记忆状态，用于捕获节点在时间上的状态变化。
> - 在给定的时间戳上更新节点的记忆状态。
> - 提供查询接口，使得模型可以获取某个节点在给定时间戳上的状态。
>
> 通过使用 `TGNMemory`，TGN 网络可以更好地理解节点和边的时间演化特性，从而更准确地进行时间序列预测、链接预测等任务。

## step4:训练前的代码准备

```python
# 定义损失函数与优化器
optimizer = torch.optim.Adam(
    set(memory.parameters()) | set(gnn.parameters())
    | set(link_pred.parameters()), lr=0.0001)
criterion = torch.nn.BCEWithLogitsLoss()
#assoc 张量定义
assoc = torch.empty(data.num_nodes, dtype=torch.long, device=device)
```

1. `assoc`：简单来说，就是帮你快速获取节点的索引的，后面训练时用的到

> 创建一个名为 `assoc` 的张量，用于将全局节点索引映射到本地索引。在时间序列图数据中，节点索引通常是全局唯一的，而在每个小批量训练中，您可能需要将这些全局索引映射到当前小批量中的局部索引。
>
> 具体来说，`assoc` 张量的长度为 `data.num_nodes`，这意味着它有足够的元素来映射数据集中的每个节点。这个张量的目的是为每个节点分配一个本地索引，使得在当前小批量训练中可以使用这些本地索引来获取对应的节点特征或其他信息。

2. `torch.empty` 

> `torch.empty` 是 PyTorch 中的一个函数，用于创建一个未初始化的张量（tensor），并分配内存空间。未初始化的张量意味着它的元素值在创建时没有被明确初始化，因此这些值可能是任意的，取决于内存中的随机数据



定义 `train` 函数与`test`函数进行训练与测试

```python
def train():
    memory.train()
    gnn.train()
    link_pred.train()

    memory.reset_state()  # Start with a fresh memory.
    neighbor_loader.reset_state()  # Start with an empty graph.

    total_loss = 0
    for batch in train_loader:
        optimizer.zero_grad()
        batch = batch.to(device)

        n_id, edge_index, e_id = neighbor_loader(batch.n_id)
        assoc[n_id] = torch.arange(n_id.size(0), device=device)

        # Get updated memory of all nodes involved in the computation.
        z, last_update = memory(n_id)
        z = gnn(z, last_update, edge_index, data.t[e_id].to(device),
                data.msg[e_id].to(device))
        pos_out = link_pred(z[assoc[batch.src]], z[assoc[batch.dst]])
        neg_out = link_pred(z[assoc[batch.src]], z[assoc[batch.neg_dst]])

        loss = criterion(pos_out, torch.ones_like(pos_out))
        loss += criterion(neg_out, torch.zeros_like(neg_out))

        # Update memory and neighbor loader with ground-truth state.
        memory.update_state(batch.src, batch.dst, batch.t, batch.msg)
        neighbor_loader.insert(batch.src, batch.dst)

        loss.backward()
        optimizer.step()
        memory.detach()
        total_loss += float(loss) * batch.num_events

    return total_loss / train_data.num_events


@torch.no_grad()
def test(loader):
    memory.eval()
    gnn.eval()
    link_pred.eval()

    torch.manual_seed(12345)  # Ensure deterministic sampling across epochs.

    aps, aucs = [], []
    for batch in loader:
        batch = batch.to(device)

        n_id, edge_index, e_id = neighbor_loader(batch.n_id)
        assoc[n_id] = torch.arange(n_id.size(0), device=device)

        z, last_update = memory(n_id)
        z = gnn(z, last_update, edge_index, data.t[e_id].to(device),
                data.msg[e_id].to(device))
        pos_out = link_pred(z[assoc[batch.src]], z[assoc[batch.dst]])
        neg_out = link_pred(z[assoc[batch.src]], z[assoc[batch.neg_dst]])

        y_pred = torch.cat([pos_out, neg_out], dim=0).sigmoid().cpu()
        y_true = torch.cat(
            [torch.ones(pos_out.size(0)),
             torch.zeros(neg_out.size(0))], dim=0)

        aps.append(average_precision_score(y_true, y_pred))
        aucs.append(roc_auc_score(y_true, y_pred))

        memory.update_state(batch.src, batch.dst, batch.t, batch.msg)
        neighbor_loader.insert(batch.src, batch.dst)
    return float(torch.tensor(aps).mean()), float(torch.tensor(aucs).mean())
```

==TODO==: 这里有关于memory的不是很懂，之后看完原理回来填坑



## step5:训练与过程记录

```python
for epoch in range(1, 51):
    loss = train()
    print(f'Epoch: {epoch:02d}, Loss: {loss:.4f}')
    val_ap, val_auc = test(val_loader)
    test_ap, test_auc = test(test_loader)
    print(f'Val AP: {val_ap:.4f}, Val AUC: {val_auc:.4f}')
    print(f'Test AP: {test_ap:.4f}, Test AUC: {test_auc:.4f}')
```



