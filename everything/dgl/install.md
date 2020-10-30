# 安装 DGL

这个主题介绍如何安装DGL, 我们建议使用`conda`或`pip`安装DGL

## 系统要求

DGL 可以在下列操作系统下工作:

* Ubuntu 16.04
* macOS X
* Windows 10

DGL要求Python的版本至少为3.6

DGL支持多个张量库作为后端, 如 PyTorch, MXNet. 关于对后端的要求及如何选择后端, 看[使用不同的后端](backends.md)

从版本0.3开始，DGL被分为CPU和CUDA版本。这些版本共享相同的Python软件包名称。如果在安装CPU版本后使用CUDA 9版本安装DGL，则CPU版本将被覆盖。

## 用conda安装

如果还没有安装`conda`, 安装[miniconda](https://conda.io/miniconda.html)或完整[anaconda](https://www.anaconda.com/download/).

安装conda后, 您将需要将DGL安装到Python 3.6 `conda`环境中.运行`conda create -n dgl python = 3.6`创建环境. 通过运行`source activate dgl`来激活环境. 激活conda环境后，运行以下命令之一.

```bash
conda install -c dglteam dgl              # For CPU Build
conda install -c dglteam dgl-cuda9.0      # For CUDA 9.0 Build
conda install -c dglteam dgl-cuda10.0     # For CUDA 10.0 Build
conda install -c dglteam dgl-cuda10.1     # For CUDA 10.1 Build
conda install -c dglteam dgl-cuda10.2     # For CUDA 10.2 Build
```

## Install from pip

对于CPU版本, 使用下面的命令使用`pip`来安装.

```bash
pip install dgl
```

对于CUDA版本, 使用下面命令中的一条来指明CUDA版本.

```bash
pip install dgl           # For CPU Build
pip install dgl-cu90      # For CUDA 9.0 Build
pip install dgl-cu92      # For CUDA 9.2 Build
pip install dgl-cu100     # For CUDA 10.0 Build
pip install dgl-cu101     # For CUDA 10.1 Build
pip install dgl-cu102     # For CUDA 10.2 Build
```

要安装当前最新的master分支版本, 使用下面命令中的一条.

```bash
pip install --pre dgl           # For CPU Build
pip install --pre dgl-cu90      # For CUDA 9.0 Build
pip install --pre dgl-cu92      # For CUDA 9.2 Build
pip install --pre dgl-cu100     # For CUDA 10.0 Build
pip install --pre dgl-cu101     # For CUDA 10.1 Build
pip install --pre dgl-cu102     # For CUDA 10.2 Build
```

## 从源码安装

从源码安装比较复杂, 有兴趣的同学自行查看[文档](https://docs.dgl.ai/install/index.html#install-from-source).