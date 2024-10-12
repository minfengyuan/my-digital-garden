---
{"dg-publish":true,"title":"PyTorch分布式计算","created":"2024-04-01 09:37","updated":"2024-04-01 10:12","tags":["deep-learning","python","pytorch"],"dg-path":"Wiki/PyTorch分布式计算.md","permalink":"/Wiki/PyTorch分布式计算/","dgPassFrontmatter":true,"noteIcon":"1"}
---


```ad-attention

本文所说的分布式计算特指DistributedDataParallel (DDP)。
```

# Concepts
首先我们需要先了解一些概念：
- rank：用于表示进程的编号/序号（在一些结构图中 rank 指的是软节点，rank 可以看成一个计算单位），每一个进程对应了一个 rank 的进程，整个分布式由许多 rank 完成。
- node：物理节点，可以是一台机器也可以是一个容器，节点内部可以有多个 GPU。
- rank 与 local_rank： rank 是指在整个分布式任务中进程的序号；local_rank 是指在一个 node 上进程的相对序号，local_rank 在 node 之间相互独立。（注意：在代码中，会使用 local_rank 来指定 GPU，并且 local_rank 和实际的 gpu 编号存在映射关系，比如，指定 gpu 4,5 进行训练，local_rank 仍然是 0,1，但前提是要先设置 os.environ['CUDA_VISIBLE_DEVICES'] = "4,5"）。
- nnodes、node_rank 与 nproc_per_node： nnodes 是指物理节点数量，node_rank 是物理节点的序号；nproc_per_node 是指每个物理节点上面进程的数量。
- word size ： 全局（一个分布式任务）中，rank 的数量。
> 💡 RANK 是全局进程的序号，而 LOCAL_RANK 可以理解为每个进程中线程的序号。

# Examples
我们以 PyTorch 举例，DDP 具体实现方式如下:

1. 获取全局变量

    ```python
    import os
    
    LOCAL_RANK = int(os.getenv('LOCAL_RANK', -1))
    RANK = int(os.getenv('RANK', -1))
    WORLD_SIZE = int(os.getenv('WORLD_SIZE', 1))
    ```

2. 根据 LOCAL_RANK 给每一个计算节点分配 GPU

    ```python
	if LOCAL_RANK != -1:
		assert torch.cuda.device_count() > LOCAL_RANK, 'insufficient CUDA devices for DDP command'
		print("torch.cuda.device_count():", torch.cuda.device_count())
		torch.cuda.set_device(LOCAL_RANK)
		device = torch.device("cuda", LOCAL_RANK)
		dist.init_process_group(backend="nccl" if dist.is_nccl_available() else "gloo")
    ```

3. 仅在主进程中保存日志信息，避免打印混乱

    ```python
    # logger
    if RANK in {-1, 0}:
    	logger.info("Message...")
    
    # pbar
    pbar = enumerate(train_loader)
	if RANK in {-1, 0}:
		pbar = tqdm(pbar, total=steps_per_epoch, desc='Epoch {}/{}'.format(epoch, args.max_epochs))
    ```

4. 在单机单卡以外的模式，需要设置分布式采样器

    ```python
    from torch.utils.data import distributed
    
    dataset = Dataset()
    sampler = None if LOCAL_RANK == -1 else distributed.DistributedSampler(dataset, shuffle=True)
    ```

5. 在主进程以外的进程中封装 DDP

    ```python
    from torch.nn.parallel import DistributedDataParallel as DDP
    
    if RANK != -1:
    	# Sync batch norm
    	model = torch.nn.SyncBatchNorm.convert_sync_batchnorm(model).to(device)
    	# DDP
    	model = DDP(model, device_ids=[LOCAL_RANK], output_device=LOCAL_RANK)
    ```

# Shell Commonds
在命令行运行训练脚本时我们可以通过指定节点或 GPU 设备来控制 DDP 进程，以下为常见的形式：

1. 单机单卡
	```bash
	python main.py --device 0
	```

2. 单机多卡
	```bash
	python -m torch.distributed.launch --nproc_per_node 2 main.py
	# 指定某几张GPU
	python -m torch.distributed.launch --nproc_per_node 2 main.py --device 0,3
	```

3. 多机多卡
	```bash
	# - master_address: master进程的网络地址
	# - master_port: master进程的一个端口，默认29500，使用前需要确认端口是否被其他程序占用
	# 节点0
	python -m torch.distributed.launch --nproc_node=8 
	        --nnodes=2 --node_rank=0 --master_address="192.168.0.1"
	        --master_port=12345 train.py
	# 节点1
	python -m torch.distributed.launch --nproc_node=8 
	        --nnodes=2 --node_rank=1 --master_address="192.168.0.1"
	        --master_port=12345 train.py
	```