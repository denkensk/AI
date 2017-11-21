# kubernetes分布式训练
## 作业训练
1. master server process：任务分配

2. 1个或多个trainer process：分布式训练及同步梯度/模型

3. 1个或多个parameter server process：共享全局的模型，接受来自每个trainer上传的梯度，然后运行优化函数更新参数

  ​

  ![Alt text](./pic/paddle-model-sharding.png?raw=true "Title")





### Master Server Process

### Trainer Process

### Parameter Server Process

### 优化算法

trainer和pserver之间的交互取决于选择哪种优化算法

- Synchronous Stochastic Gradient Descent(sync-SGD 同步随机梯度下降)

  Pserver需要等待所有的trainer完成n-th mini-batch的计算并且接受到他们的梯度，之后才将新的参数广播给每一个tranier。同样每一个trainer需要等待新的参数到达之后才能开始n+1-th mini-batch

- Asynchronous Stochastic Gradient Descent (async-SGD 异步随机梯度下降)

  ​

  ​

