# Parameter Server

## Background

实际的训练数据在1TB到1PB之间。这些数据可以训练出拥有10^9~10^12个参数的复杂模型。模型需要经常将参数全局分享到所有的worker上。worker需要频繁的去访问共享参数，然后通过计算来完善它。整个过程中将会面临以下3个挑战：

- 访问参数需要巨大的带宽。
- 许多机器学习算法是顺序的，这将会因为cost同步以及机器延迟较高时，损失性能。
- 在大规模中，容错是至关重要的。训练的任务经常会在云端运行，而云端的机器是不可以依赖的并且作业可以被强占。

## Architecture
![Alt text](./pic/pserver_and_trainer.png?raw=false "Title")

## classify

### Data Parallel Training

通过数据并行的方式来提升SGD的效率，比如，假如每次SGD的mini-batch是1000个样本，那么，切成10份，每份100个，然后将模型复制10份，每份都将梯度传到参数服务器上。

数据切分也分为同步和异步两种方式:

![Alt text](./pic/data_parallel_training.png?raw=false "Title")

- Synchronous

同步是等待每个独立的model传上来的梯度都到齐后，将所有的梯度累加计算平均之后，再更新参数。优点是loss的下降比较稳定，但是缺点比较明显，处理的速度取决于最慢的那个分片的计算时间。

![Alt text](./pic/blog-synchronous.gif?raw=false "Title")

- Asynchronous

异步是不用等待，每个独立的模型的参数直接更新。优点是计算速度快，计算资源能够得到充分的利用，缺点是loss的下降不稳定，抖动大。

![Alt text](./pic/blog-asynchronous.gif?raw=false "Title")



**在数据量小，各个节点的计算能力比较均衡的情况下，推荐使用同步模式；数据量很大，各个机器的计算性能参差不齐的情况下，推荐使用异步方式。**



### Model Parallel Training

模型并行是指让模型的不同部分执行在不同的设备上，worker之间共享同一份数据。

![Alt text](./pic/model_parallel_training.png?raw=false "Title")


