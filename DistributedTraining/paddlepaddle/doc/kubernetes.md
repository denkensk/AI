# kubernetes分布式训练

* [kubernetes分布式训练](#kubernetes分布式训练)
    * [作业训练](#作业训练)
        * [Master Server Process](#master-server-process)
        * [Trainer Process](#trainer-process)
        * [Parameter Server Process](#parameter-server-process)
        * [优化算法](#优化算法)
    * [容错性](#容错性)

## 作业训练

1. master server process：任务分配

2. 1个或多个trainer process：分布式训练及同步梯度/模型

3. 1个或多个parameter server process：共享全局的模型，接受来自每个trainer上传的梯度，然后运行优化函数更新参数

  ​

  ![Alt text](./pic/paddle-model-sharding.png?raw=true "Title")




### Master Server Process

The master server process will:

- Partition a dataset into tasks and dispatch tasks to trainers.
- Keep track of training progress on the dataset with task queue. A training job will iterate on the dataset for a full pass until it goes into next pass.

### Trainer Process

The trainer process will:

- Request tasks from the master.
- Work on the tasks
- Upload gradient to parameter servers, and update local model by downloading new parameters from parameter servers.

### Parameter Server Process

Parameter server processes通过协作维护parameters。参数被分割在不同的parameter servers。

The parameter server will:

- Receive gradient from the trainers, update its parameters, and give the trainers the latest parameters.
- Periodically save its parameters to distributed file system by overriding the previous save.

### 优化算法

trainer和pserver之间的交互取决于选择哪种优化算法

- Synchronous Stochastic Gradient Descent(sync-SGD 同步随机梯度下降)

  Pserver需要等待所有的trainer完成n-th mini-batch的计算并且接受到他们的梯度，之后才将新的参数广播给每一个tranier。同样每一个trainer需要等待新的参数到达之后才能开始n+1-th mini-batch

- Asynchronous Stochastic Gradient Descent (async-SGD 异步随机梯度下降)

  不同的trainers之间不需要同步，pserver只要接收到新的梯度就会更新参数

  - 每个trainer在每n个mini-batches之后上传计算得到的梯度
  - 每m个mini-batches之后，trainer从pserver上下载新的参数
  - n和m不一定相等

## 容错性


