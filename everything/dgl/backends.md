# DGL 后端

DGL支持PyTorch，MXNet和Tensorflow后端。 DGL将在以下选项上选择后端（从高优先级到低优先级）-DGLBACKEND环境

* `DGLBACKEND` 环境变量
    你可以使用 `DGLBACKEND=[BACKEND] python gcn.py … `来指定后端, 或者使用 ` export DGLBACKEND=[BACKEND]`来设置公共环境变量
* "~/.dgl" 文件夹下的 config.json 文件
    你可以使用 `python -m dgl.backend.set_default_backend [BACKEND]`来设置默认后端

当前后端可以从 mxnet, pytorch, tensorflow 中选择.

## PyTorch

将DGLBACKEND设置为pytorch以指定PyTorch后端。要求PyTorch是1.1.0或更高版本. 有关安装说明, 请参见 pytorch.org.

## MXNet

将DGLBACKEND设置为mxnet以指定MXNet后端. 要求MXNet是1.5或更高版本. 有关安装说明, 请参见 mxnet.apache.org.

MXNet使用uint32作为整数张量的默认数据类型, 该类型仅支持大小小于2^32的图形. 要启用大型图形训练, 请使用`USE_INT64_TENSOR_SIZE = 1` 标志构建MXNet. 请参见[FAQ](https://mxnet.apache.org/api/faq/large_tensor_support)来了解更多信息.

MXNet 1.5及更高版本具有为`NDArray`对象启用Numpy形状模式的选项, 某些DGL模型需要启用此模式才能正确运行. 但是, 此模式可能与禁用此模式的预训练模型参数不兼容, 例如来自GluonCV和GluonNLP的预训练模型. 通过设置`DGL_MXNET_SET_NP_SHAPE`, 用户可以打开或关闭此模式.

## Tensorflow

将`DGLBACKEND`设置为`tensorflow`以指定Tensorflow后端. 要求Tensorflow是2.2.0或更高版本. 有关安装说明, 请参见 tensorflow.org. 此外, DGL会将`TF_FORCE_GPU_ALLOW_GROWTH`设置为`true`以防止Tensorflow接管整个GPU内存.

```bash
pip install "tensorflow>=2.2.0rc1"  # when using tensorflow cpu version
```

