## TensorConfig
* SuperPulsar 工具具备调整**输出模型**的[输入](#dst_input_tensors)/[输出](#dst_output_tensors) tensor 的属性的能力，即允许**输出模型**（如 joint 模型）跟**原始输入模型**（如 onnx 模型）的**输入输出**数据属性（比如图像尺寸、颜色空间等）不一致
* 参数路径：
  * [无 tasks 配置文件](/super_pulsar/config/config_without_tasks.md):
    * [input_tensors](/super_pulsar/config/config_without_tasks.md#input_tensors)
    * [output_tensors](/super_pulsar/config/config_without_tasks.md#output_tensors)
    * [src_input_tensors](/super_pulsar/config/config_without_tasks.md#src_input_tensors)
    * [src_output_tensors](/super_pulsar/config/config_without_tasks.md#src_output_tensors)
    * [dst_input_tensors](/super_pulsar/config/config_without_tasks.md#dst_input_tensors)
    * [dst_output_tensors](/super_pulsar/config/config_without_tasks.md#dst_output_tensors)
  * [有 tasks 配置文件](/super_pulsar/config/config_with_tasks.md):
    * [tasks](/super_pulsar/config/config_with_tasks.md).[input_model_items](/super_pulsar/config/config_with_tasks.md#tasks\.input_model_items).[model](/super_pulsar/config/config_with_tasks.md#model).input_tensors
    * [tasks](/super_pulsar/config/config_with_tasks.md).[input_model_items](/super_pulsar/config/config_with_tasks.md#tasks\.input_model_items).[model](/super_pulsar/config/config_with_tasks.md#model).output_tensors
    * [tasks](/super_pulsar/config/config_with_tasks.md).[output_model_items](/super_pulsar/config/config_with_tasks.md#tasks\.input_model_items).[model](/super_pulsar/config/config_with_tasks.md#model).input_tensors
    * [tasks](/super_pulsar/config/config_with_tasks.md).[output_model_items](/super_pulsar/config/config_with_tasks.md#tasks\.input_model_items).[model](/super_pulsar/config/config_with_tasks.md#model).output_tensors

* 示例：

```prototxt
# 请按参数路径将以下内容放入配置文件中的正确位置

input_tensors {
  color_space: TENSOR_COLOR_SPACE_AUTO
  input_height: 1500
  input_width: 2000
}
output_tensors {
  color_space: TENSOR_COLOR_SPACE_AUTO
}
```

### tensor_name
* 参数作用：
  * 指定当前结构体所描述**输入模型**的 tensor 或所作用的**输出模型**的 tensor 的名称
  * 在[无 tasks 配置文件](/super_pulsar/config/config_without_tasks.md) 中，对于 [src_input_tensors](/super_pulsar/config/config_without_tasks.md#src_input_tensors)、[src_output_tensors](/super_pulsar/config/config_without_tasks.md#src_output_tensors)、[dst_input_tensors](/super_pulsar/config/config_without_tasks.md#dst_input_tensors) 和 [dst_output_tensors](/super_pulsar/config/config_without_tasks.md#dst_output_tensors) 等每一个数组，若其中有任何一个 item 结构体中的 `tensor_name` 字段是缺省的，那么该 item 的内容将覆盖所在数组中的其它 item 的内容
* 参数类型：一个字符串

### color_space
一个枚举值。用于描述**输入模型**的 tensor 的颜色空间，或指定**输出模型**的 tensor 的颜色空间。可选项：
* `TENSOR_COLOR_SPACE_AUTO`：默认值。根据模型输入 channel 数自动识别：3-channel: BGR；1-channel: GRAY
* `TENSOR_COLOR_SPACE_BGR`
* `TENSOR_COLOR_SPACE_RGB`
* `TENSOR_COLOR_SPACE_GRAY`
* `TENSOR_COLOR_SPACE_NV12`
* `TENSOR_COLOR_SPACE_NV21`
* `TENSOR_COLOR_SPACE_BGR0`

### data_type
一个枚举值。指定输入输出 tensor 的数据类型。可选项：
* `DATA_TYPE_UNKNOWN`
* `UINT2`
* `INT2`
* `MINT2`
* `UINT4`
* `INT4`
* `MINT4`
* `UINT8`：**输入 tensor** 的默认值
* `INT8`
* `MINT8`
* `UINT16`
* `INT16`
* `FLOAT32`：**输出 tensor** 的默认值

### quantization_value
一个整数。配置正数时生效，或者满足以下条件之一时也会以推荐值生效
1. 源模型输出实型，目标模型输出整型
2. 源模型输入实型，目标模型输入整型

### color_standard
一个枚举值。用于设置色彩空间标准。可选项：
* `CSC_LEGACY`: 默认值
* `CSS_ITU_BT601_STUDIO_SWING`
* `CSS_ITU_BT601_FULL_SWING`

### input_height
一个整数。修改输入张量高度, 若不做修改则设置为 0

### input_width
一个整数。修改输入张量宽度, 若不做修改则设置为0

### tensor_layout
一个枚举值。用于描述 tensor 维度排列布局。可选项：
* `NATIVE`: 默认值
* `NHWC`
* `NCHW`
