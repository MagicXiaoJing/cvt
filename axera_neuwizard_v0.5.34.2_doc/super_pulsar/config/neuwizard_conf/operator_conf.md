## 输入输出盖帽
* 参数路径：
  * [无 tasks 配置文件](/super_pulsar/config/config_without_tasks.md): [neuwizard_conf](/super_pulsar/config/config_neuwizard_conf.md).[operator_conf](/super_pulsar/config/config_neuwizard_conf.md#operator_conf)
  * [有 tasks 配置文件](/super_pulsar/config/config_with_tasks.md): [tasks](/super_pulsar/config/config_with_tasks.md).[neuwizard_conf](/super_pulsar/config/config_neuwizard_conf.md).[operator_conf](/super_pulsar/config/config_neuwizard_conf.md#operator_conf)

* 参数作用：
  * 附加的输入输出盖帽算子对现有算子的输入或输出的 tensor 附加一次运算；在配置文件中，添加盖帽算子的过程是通过给现有算子的输入或输出 tensor 扩充或修改**属性**的过程来实现的
* 字段说明：
  * `input_conf_items`: 结构体数组。用于为模型的输入数据做前处理。详情见：[前处理](#前处理)
  * `output_conf_items`: 结构体数组。用于对输出数据做后处理。详情见：[后处理](#后处理)
<!--SLOT0-->
  * `additional_output_tensor_names`: 字符串数组

### 前处理
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items
* 示例：

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

input_conf_items {
    # selector 用于指示附加的前处理算子将要作用的输入 tensor
    selector {
        op_name: "normal0" # 输入 tensor 的名称
    }
    # attributes 用于包裹作用于 "normal0" 的盖帽算子
    attributes {
        input_modifications {
            # 对输入数据做一个 affine 操作，用于改变编译后模型的输入数据类型
            #     既，将输入数据类型由浮点数 [0, 1) 类型改为 uint8
            affine_preprocess {
                slope: 1
                slope_divisor: 255
                bias: 0
            }
        }
        input_modifications {
            # 设置编译后的模型的输入尺寸为 1920x1080
            override_input_shape {
                shape: 0, # 维持和输入模型一样
                shape: 0, # 维持和输入模型一样
                shape: 1080,
                shape: 1920,
            }
        }
    }
}
```

#### input_conf_items.selector
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.selector
* 字段说明：
  * `op_name`: 指定输入 tensor 的完整名称。如 "normal0"
  * `op_name_regex`: 指定一个正则表达式，用于适配多个 tensor。相应的 `attributes` 结构体中的盖帽算子将作用于所有被适配的 tensor
* 作用：
  * 用于指示附加的前处理算子将要作用的输入 tensor 的名称
* 示例：

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

selector {
    op_name: "normal0"
}
```

#### input_conf_items.attributes
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes
* 作用：
  * 用于描述对输入 tensor 的属性的更改，目标输入 tensor 由 [input_conf_items.selector](#input_conf_items.selector) 所指定
* 参数类型：一个结构体。字段说明：
  * `type`: 明示或修改输入 tensor 的数据类型。[枚举类型](#DataType)，默认值 `DATA_TYPE_UNKNOWN`
  * `affine_mode`: 指示输入 tensor 的 affine 算子的模式。[字段说明](#IOAffineMode)
  * `input_modifications`: [前处理算子数组](#前处理算子数组)。对输入 tensor 添加的盖帽算子。有多种，可以同时指定多个

##### 前处理算子数组
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications
* 作用：
  * 作用于某一个输入 tensor 的[前处理算子](#前处理算子)所组成的数组
  * 在**前处理算子数组**中的所有算子依次执行，排在数组中第二位的算子以前一个算子的输出为输入，依次类推。
    * 但是 **[override_input_shape](#override_input_shape) 除外** ，不论 **[override_input_shape](#override_input_shape)** 处于第几位，都永远是第一个起作用

#### 前处理算子
##### input_normalization
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.input_normalization
* 字段说明：
  * `mean`: 浮点数数组
  * `std`: 浮点数数组
* 实现效果：y=(x-mean)/std
* 注意：
  * 这里 mean/std 的顺序与输入 tensor 的[颜色空间](/super_pulsar/config/config_tensor_config.md#color_space)有关
  * 如果上述变量等于 `TENSOR_COLOR_SPACE_AUTO` / `TENSOR_COLOR_SPACE_BGR` 则 mean/std 的顺序为 BGR
  * 如果上述变量等于 `TENSOR_COLOR_SPACE_RGB` 则 mean/std 的顺序就是 RGB

##### input_to_constant
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.input_to_constant
* 参数类型：一个结构体。字段是说明：
  * `value`: 一个浮点数

##### input_3rvschn
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.input_3rvschn
* 参数作用：用于翻转图像的[像素 channel 的顺序](/super_pulsar/config/config_tensor_config.md#color_space)
  * 如果当前图像的像素 channel 的顺序为 RGB，则被翻转为 BGR。反之亦然
* 字段说明：
  * 此结构体参数没有字段。在配置文件中存在，即代表需要在编译过程中对数据的 channel 顺序进行翻转。否则代表不翻转

##### affine_preprocess
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.affine_preprocess
* 作用：
  * 对输入 tensor 做 affine 操作
* 字段说明：
  * `slope`: 浮点数数组。数组长度等于 1 或者数据的 channel 数。当长度为 1 时，编译工具会自动复制 channel 次
  * `slope_divisor`: 浮点数数组。数组长度同 `slope`
  * `bias`: 浮点数数组。数组长度同 `slope`
  * `bias_divisor`: 浮点数数组。数组长度同 `slope`
* 实现效果：y=x*(slope/slope_divisor)+(bias/bias_divisor)
* 示例：将输入数据类型由数域 {k/255}(k=0, 1, ..., 255) 改为整数域 [0, 255]
  * 希望编译后的模型输入数据类型为 uint8

```prototoxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

affine_preprocess {
    slope: 1
    slope_divisor: 255
    bias: 0
}
```

##### input_rvschn
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.input_rvschn
* 作用同类似于 [input_3rvschn](#input_3rvschn)，区别是不限定 channel 数为 3
* 字段说明：
  * 此结构体参数没有字段。在配置文件中存在，即代表需要在编译过程中对数据的 channel 顺序进行翻转。否则代表不翻转

##### space_to_depth
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.space_to_depth
* 作用：
  * 此算子用于实现 **bayer** 转 **rggb** 功能
* 字段说明：
  * `block_size`: 一个整数。缺省值为 0。当值为 0 时，编译器会自动修改为 1。此参数用于表示 **bayer** 格式的 block size
  * `op_name`: 一个字符串。用于给当前算子指定名字。一般情况下可以省略，缺省时由编译器自动分配名字
    * 当需要添加其它算子对当前算子的结果做进行进一步的转换时，需要给 `op_name` 指定一个明确的值，且不能与模型内部的算子和已定义的盖帽算子的名字冲突

##### blob_pack
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.blob_pack
* 作用：
  * 此算子用于实现将 bit 数为 8、10、12、14bits 等数据 align 为 128bit 的数据流转换成 uint16 的数组
* 字段说明：
  * `in_bit`: sensor channel 的 bit 数，一般为 8、10、12 或 14 bits
  * `mode`: 枚举类型。可选项：
    * `LSB`: 将 8、10、12 或 14bits 的数据转成 uint16 时，**高位对齐，低位补 0**，即 `x_u16 = x_10bit << 6`
    * `MSB`: 将 8、10、12 或 14bits 的数据转成 uint16 时，**低位对齐，高位补 0**，即 `x_u16 = x_10bit`

<!--SLOT1-->

##### input_modifications.csc
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.csc
* 作用：
  * 此算子用于对输入 tensor 的颜色空间进行转换
* 字段说明：
  * `src_cs`: 原始[颜色空间](#ColorSpace)
  * `dst_cs`: 目标[颜色空间](#ColorSpace)
* 示例：将模型的输入图像的颜色空间由 RGB 改为 BGR
  * 原始输入模型的输入图像的颜色空间为 RGB，希望编译后模型输入图像的颜色空间为 BGR

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

csc {
  src_cs {
    type: CST_BGR0
  }
  dst_cs {
    type: CST_BGR
  }
}
```

##### override_input_shape
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.override_input_shape
* 参数作用：对输入 tensor 的 shape 进行转换
  * 注意：
    * override_input_shape **不是一个真正意义上的算子**，它只是对某一个 tensor 的 shape 属性做修改
    * override_input_shape 的**作用不受位置的影响**，不论 `override_input_shape` 被放在输入 tensor 的前处理算子数组 [input_modifications](#input_modifications) 的第几位，都可以认为它是第一个发挥作用的。即，tensor 的 shape 或输入图像的尺寸的总是在一开始就完成了，而不是在盖帽算子序列执行到某一步才开始改变
* 字段说明：
  * `shape`: 浮点数数组。
    * 注意：当某一个维度的数值被设置为 0 时，不代表真的将这一维的大小设置为 0，也不代表去掉这一维。而是保留原来的大小
* 示例：
  * 假设当前 tensor 的 shape 为 (1, 3, 380, 672)，希望改成 (1, 3, 760, 1344)

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

override_input_shape {
    shape: 0, # or 1
    shape: 0, # or 3
    shape: 760,
    shape: 1344,
}
```

##### fbdc_preprocess
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.fbdc_preprocess
* 参数类型：一个[结构体](#FBCDCProcess)。

##### scale_to_integers
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.scale_to_integers
* 参数类型：一个结构体。此结构体无内部字段，存在即代表执行相应操作
* 实现效果：强制使得编译后模型的输入数据类型变为整数，并且在模型头部添加一个除以 scale 的操作
  * y=x/((2**bit) - 1)
* 示例：

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

scale_to_integers {
}
```

##### enforce_integers
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.enforce_integers
* 参数类型：一个结构体。此结构体无内部字段，存在即代表执行相应操作
* 实现效果：强制使得编译后模型的输入数据类型变为整数
* 示例：

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

enforce_integers {
}
```

##### input_resize
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.input_modifications.input_resize
* 参数作用：用于对图像的尺寸进行缩放
* 特别说明：注意和 [override_input_shape](#override_input_shape) 的区别，两者都可以起到修改图像的尺寸作用，但是有如下区别：
  * [override_input_shape](#override_input_shape) 总是第一个起作用，而 input_resize 起作用的时机由其在 [input_modifications](#input_modifications) 数组中的位置决定
  * [override_input_shape](#override_input_shape) 改变的是图像尺寸的绝对值，而 input_resize 总是在图像尺寸的现有值的基础上乘以一个系数
* 参数类型：一个结构体。字段说明：
  * `scale_w`：一个浮点数。用于设置对图像 width 的缩放系数
  * `scale_h`: 一个浮点数。用于设置对图像 height 的缩放系数
* **注意**：scale_w 和 scale_h 在小于 0.5 的范围内，**只支持** 0.25 0.125 或 0.0625
* 示例：

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

input_resize {
    scale_w: 2.0 # 将原始输入图像或某前处理算子输出图像的 width x 2.0
    scale_h: 2.0 # 将原始输入图像或某前处理算子输出图像的 height x 2.0
}
```


### 后处理
* 参数路径：
  * neuwizard_conf.operator_conf.output_conf_items
* 示例：

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

output_conf_items {
    # selector 用于指示输入 tensor
    selector {
        op_name: "normal0" # 输入 tensor 的名称
    }
    # attributes 用于包裹作用于 "normal0" 的盖帽算子
    attributes {
        # 后处理算子数组
    }
}
```

#### output_conf_items.selector
* 参数路径：
  * neuwizard_conf.operator_conf.output_conf_items.selector
* 字段说明：
  * `op_name`: 指定输入 tensor 的完整名称。如 "normal0"
  * `op_name_regex`: 指定一个正则表达式，用于适配多个 tensor。相应的 `attributes` 结构体中的输出盖帽算子将作用于所有被适配的 tensor
* 示例：

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

selector {
    op_name: "normal0"
}
```

#### output_conf_items.attributes
* 参数路径：
  * neuwizard_conf.operator_conf.output_conf_items.attributes
* 字段说明：
  * `type`: 明示或修改输入 tensor 的数据类型。枚举类型，[可选项](#DataType)，默认值`DATA_TYPE_UNKNOWN`
  * `affine_mode`: 指示输入 tensor 的 affine 算子的模式。[字段说明](#IOAffineMode)
  * `output_modifications`: [后处理算子数组](#后处理算子数组)。对输出 tensor 添加的盖帽算子。有多种，可以同时指定多个

##### 后处理算子数组
* 参数路径：
  * neuwizard_conf.operator_conf.output_conf_items.attributes.output_modifications
* 作用：
  * 作用于某一个输入 tensor 的[后处理算子](#后处理算子)所组成的数组
  * 在**后处理算子数组**中的所有算子依次执行，排在数组中第二位的算子以前一个算子的输出为输入，依次类推。

#### 后处理算子
##### affine_postprocess
* 参数路径：
  * neuwizard_conf.operator_conf.output_conf_items.attributes.output_modifications.affine_postprocess
* 作用：
  * 对输出 tensor 做 affine 操作
* 字段说明：
  * `slope`: 浮点数数组。数组长度等于 1 或者数据的 channel 数。当长度为 1 时，编译工具会自动复制 channel 次
  * `slope_divisor`: 浮点数数组。数组长度同 `slope`
  * `bias`: 浮点数数组。数组长度同 `slope`
  * `bias_divisor`: 浮点数数组。数组长度同 `slope`

<!--SLOT2-->

##### depth_to_space
* 参数路径：
  * neuwizard_conf.operator_conf.output_conf_items.attributes.output_modifications.depth_to_space
* 作用：
  * 此算子用于实现 **rggb** 转 **bayer** 功能
* 字段说明：
  * `block_size`: 一个整数。缺省值为 0。当值为 0 时，编译器会自动修改为 1。此参数用于表示 **bayer** 格式的 blaock size
  * `op_name`: 一个字符串。用于给当前算子指定名字。一般情况下可以省略。缺省时由编译器自动分配名字
    * 当需要添加其它算子对当前算子的结果做进行进一步的转换时，需要给 `op_name` 指定一个明确的值，且不能与模型内部的算子和已定义的盖帽算子的名字冲突

##### output_modifications.csc
* 参数路径：
  * neuwizard_conf.operator_conf.output_conf_items.attributes.output_modifications.csc
* 作用：
  * 此算子用于对输出 tensor 的颜色空间进行转换
* 字段说明：
  * `src_cs`: 原始[颜色空间](#ColorSpace)
  * `dst_cs`: 目标[颜色空间](#ColorSpace)

##### unpack_postprocess
* 参数路径：
  * neuwizard_conf.operator_conf.output_conf_items.attributes.output_modifications.unpack_postprocess
* 作用：
  * 此算子用于将 uint16 的数组转换成 bit 数为 8、10、12 或 14bits align 为 128bit 的数据流
* 字段说明：
  * `block_size`: 一个整数。缺省值为 0。当值为 0 时，编译器会自动修改为 1。此参数用于表示 **bayer** 格式的 block size
  * `unpack_mode`: 枚举类型。可选项：
    * `LSB`: 将 uint16 数据转成 8、10、12 或 14bits 的数据时，**从高位开始取相应位数**，即 `x_10bit = (x_u16 >> 6) & 0x03FF`
    * `MSB`: 将 uint16 数据转成 8、10、12 或 14bits 的数据时，**从低位开始取相应位数**，即 `x_10bit = x_u16 & 0x03FF`

<!--SLOT3-->

###### scale_to_integers
* 参数路径：
  * neuwizard_conf.operator_conf.output_conf_items.attributes.output_modifications.scale_to_integers
* 字段说明：
  * 此结构体没有字段。在配置文件中存在，即代表需要在编译过程中对数据执行操作。否则代表不执行

##### fbc_postprocess
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.output_modifications.fbc_postprocess
* 参数类型：一个[结构体](#FBCDCProcess)。

##### split_outputs
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.output_modifications.split_outputs
* 参数作用：从一个输出 tensor fork 出若干个输出
* 参数类型：一个结构体
* 字段说明：
  * `output_names`：字符串数组
* 示例：

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

output_conf_items {
    selector {
        op_name: "blend" # 原始输出 tensor 的名称
    }
    attributes {
        output_modifications {
            split_outputs {
                output_names: ["orig", "resize"] # 原始输出 tensor fork 出两个，名称分别为 orig 和 resize
            }
        }
    }
}
```

##### output_resize
* 参数路径：
  * neuwizard_conf.operator_conf.input_conf_items.attributes.output_modifications.output_resize
* 参数作用：用于对图像的尺寸进行缩放
* 参数类型：一个结构体。字段说明：
  * `scale_w`：一个浮点数。用于设置对输出图像 width 的缩放系数
  * `scale_h`: 一个浮点数。用于设置对输出图像 height 的缩放系数
* **注意**：scale_w 和 scale_h 在小于 0.5 的范围内，**只支持** 0.25 0.125 或 0.0625
* 示例：

```prototxt
# 注意按参数路径，将以下内容放入配置文件中合适的位置

input_resize {
    scale_w: 2.0 # 将输出图像或某后处理算子输出图像的 width x 2.0
    scale_h: 2.0 # 将输出图像或某后处理算子输出图像的 height x 2.0
}
```


<!--SLOT-->

## 通用数据类型
### DataType
数据类型
* 可选项：
  * `DATA_TYPE_UNKNOWN`: 
  * `UINT2`: 
  * `INT2`: 
  * `MINT2`: 
  * `UINT4`: 
  * `INT4`: 
  * `MINT4`: 
  * `UINT8`: 
  * `INT8`: 
  * `MINT8`: 
  * `UINT16`: 
  * `INT16`: 
  * `FLOAT32`: 
* 返回：
  * [input_conf_items.attributes](#input_conf_items.attributes)
  * [output_conf_items.attributes](#output_conf_items.attributes)

### ColorSpace
颜色空间
* 字段定义：
  * `type`: 枚举类型。可选项
    * `CST_AUTO`
    * `CST_BGR`
    * `CST_RGB`
    * `CST_GRAY`
    * `CST_FEATUREMAP`
    * `CST_RAW`
    * `CST_NV12`
    * `CST_NV21`
    * `CST_BGR0`
  * `standard`: 枚举类型。可选项
    * `CSS_LEGACY`: 默认值
    * `CSS_ITU_BT601_STUDIO_SWING`
    * `CSS_ITU_BT601_FULL_SWING`
* 返回：
  * [input_modifications.csc](#input_modifications.csc)
  * [output_modifications.csc](#input_modifications.csc)

### IOAffineMode
IOAffineMode 用于指示作用于模型输入输出 tensor 的 affine 操作的模式
* IOAffineMode 结构体的字段定义：
  * `slope_mode`: 枚举类型。可选项：
    * `IO_SLOPE_MODE_NONE`: 
    * `IO_SLOPE_MODE_CONSTANT`: 
    * `IO_SLOPE_MODE_LAYERWISE`: 
    * `IO_SLOPE_MODE_LAYERWISE_INTEGER`: 
    * `IO_SLOPE_MODE_LAYERWISE_POWER_OF_TWO`: 
    * `IO_SLOPE_MODE_CHANNELWISE`: 
    * `IO_SLOPE_MODE_CHANNELWISE_POWER_OF_TWO`: 
  * `bias_mode`: 可选项：
    * `IO_BIAS_MODE_NONE`: 
    * `IO_BIAS_MODE_CONSTANT`: 
    * `IO_BIAS_MODE_LAYERWISE`: 
    * `IO_BIAS_MODE_CHANNELWISE`: 
  * `slope`: 浮点数数组
  * `slope_divisor`: 浮点数数组
  * `bias`: 浮点数数组
  * `bias_divisor`: 浮点数数组
* 返回：
  * [input_conf_items.attributes](#input_conf_items.attributes)
  * [output_conf_items.attributes](#output_conf_items.attributes)

### FBCDCProcess
* FBCDCProcess 结构体字段说明：
  * `tile_shape`: 一个枚举值。可选项：
    * `TILE_SHAPE_MODE_W64_H4`
    * `TILE_SHAPE_MODE_W32_H4`
  * `mode`: 一个枚举值。可选项：
    * `RAW8`
    * `RAW10`
    * `RAW12`
    * `RAW14`
    * `RAW16`
    * `YUV_Y`
    * `YUV_UV`
    * `RGBA`
* 返回：
  * [input_conf_items.fbdc_preprocess](#fbdc_preprocess)
  * [output_conf_items.fbc_postprocess](#fbc_postprocess)
