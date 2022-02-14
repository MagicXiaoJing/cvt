# pulsar debug
## 概述
pulsar debug 用于在编译模型过程中出现问题时，对中间过程进行 debug 测试

模型转换过程中存在多个细分的阶段。每个阶段中，将模型从一种格式转换到另外一种格式。在格式转换过程中，将神经网络的各个层（layer），从一种形态转换成另一种形态

调试方式分为两种：
* 逐阶段调试：将模型从一种中间格式转换成相邻的下一种中间格式后做一次测试。从而定位到掉点的中间格式
* 逐层调试：将模型从一种中间格式转换成相邻的下一种中间格式的过程中，每转换一个 layer 后做一次测试。从而定位到掉点的层

## 用法
* 命令

```
pulsar debug transform(/layer) --config config.prototxt --input origin_model.onnx --domain native 
```

## 选项
## 子功能名称
可选项：
* `transform`: 逐阶段调试
* `layer`: 逐层调试（目前只支持从 native 开始转换）

## `--config`
指定配置文件。一般使用 `pulsar build` 的 `--output_config` 选项输出的配置文件

## `--input`
指定编译过程的原始输入模型

## `--domain`
指定编译模型过程中的各个阶段。可选项（按转换模型过程的中间阶段排序）：
* `native`
* `pretransformed`
* `transformed`
* `magma`
* `magma_validified`
* `lava`
