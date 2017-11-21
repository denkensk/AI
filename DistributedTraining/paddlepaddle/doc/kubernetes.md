##kubernetes分布式训练
###作业训练
1. master server process：任务分配
2. 1个或多个trainer process：分布式训练及同步梯度/模型
3. 1个或多个parameter server process：共享全局的模型，接受来自每个trainer上传的梯度，然后运行优化函数更新参数

####Master Server Process

####Trainer Process

####Parameter Server Process

####优化算法

