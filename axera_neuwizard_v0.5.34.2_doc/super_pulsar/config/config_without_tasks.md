# 无 task 的配置文件
[配置文件基础](/super_pulsar/config.md)

## 概述
* 无 tasks 的配置文件属于较早的版本
* 它能够指导大部分常用的编译过程，但表达能力具有一定的局限性，**有些功能无法实现**
* [最新版本配置文件(实验版本)](/super_pulsar/config/config_with_tasks.md)

## 配置文件示例
```prototxt
# my_config.prototxt

# 基本配置参数：输入输出
input_path: "LeNet5.onnx"
input_type: INPUT_TYPE_ONNX
output_path: "LeNet5.joint"
output_type: OUTPUT_TYPE_JOINT

# 选择硬件平台
target_hardware: TARGET_HARDWARE_AX630

# neuwizard 工具的配置参数
neuwizard_conf {
    operator_conf {
        input_conf_items {
            attributes {
                input_modifications {
                    affine_preprocess {
                        slope: 1
                        slope_divisor: 255
                        bias: 0
                    }
                }
            }
        }
    }
    dataset_conf_calibration {
        path: "imagenet-1k-images.tar" # 一个具有 1000 张图片的 tar 包，用于编译过程中对模型校准
        type: DATASET_TYPE_TAR
        size: 256
    }
    dataset_conf_error_measurement {
        path: "imagenet-1k-images.tar" # 用于编译过程中测量偏差
        type: DATASET_TYPE_TAR
        size: 4
        batch_size: 4
    }
    evaluation_conf {
        path: "neuwizard.evaluator.error_measure_evaluator"
        type: EVALUATION_TYPE_ERROR_MEASURE
        source_ir_types: IR_TYPE_NATIVE
        ir_types: IR_TYPE_LAVA
    }
}

# pulsar compiler 的配置参数
pulsar_conf {
    batch_size: 1
}
```

## 配置文件字段说明
### enable_progress_bar
* 参数路径：
  * enable_progress_bar
* 参数作用：
  * 控制编译时是否显示进度条。配置文件中可以省略
* 参数类型：一个布尔
  * `true`：编译时显示进度条
  * `false`：编译时不显示进度条
  * 被省略：等于同 `false`
* 示例：

```prototxt
# my_config.prototxt

enable_progress_bar: true
```
### cpu_backend_settings
* 参数路径：
  * cpu_backend_settings
* 参数作用：
  * 控制编译后模型采用的 CPU 后端模式，目前有 ONNX 与 AXEngine 可选
* 参数类型：一个结构体。结构体字段：
  * [结构体字段](/super_pulsar/config/config_cpu_backend_settings.md)

### neuwizard_conf
* 参数路径：
  * neuwizard_conf
* 参数作用：
  * 编译器子工具 NeuWizard 的配置参数
  * neuwizard_conf 用于指导 NeuWizard 将 onnx 或 magma 格式的模型编译成 lava_joint 格式
* 参数类型：一个结构体。结构体字段：
  * [结构体字段](/super_pulsar/config/config_neuwizard_conf.md)

### pulsar_conf
* 参数路径:
  * pulsar_conf
* 参数作用：
  * 编译器子工具 pulsar_compiler 的配置参数
  * 用于指导 pulsar_compiler 将 lava_joint 或 lava 格式的模型编译成 joint 或 neu 格式的模型
* 参数类型：一个结构体
  * [结构体字段](/super_pulsar/config/config_pulsar_conf.md)

### joint_conf
* 参数路径：
  * joint_conf
* 参数作用：
  * 用于 lava_joint [模型焊接](joint_conf/config_joint_conf_fuse_model.md)
    * [详情](joint_conf/config_joint_conf_fuse_model.md)
  * 用于[制作具有多份 weights 的 joint 模型](/use_case/mcode_wbt.md)等编译过程的参数配置
    * [详情](/use_case/mcode_wbt.md)

### input_path
* 参数路径：
  * input_path
* 参数作用：
  * 指定输入模型的路径
* 参数类型：一个字符串
* 注意：
  * 路径是配置文件所在目录的相对路径
  * 参数值字符串需要用半角双引号 `"` 包裹
  * 可以是 oss 路径
* 示例：

```prototxt
# my_config.prototxt

input_path: "./model.onnx"
```

### output_path
* 参数路径：
  * output_path
* 参数作用：
  * 指定输出模型的路径
* 参数类型：一个字符串
* 注意：
  * 路径是配置文件所在目录的相对路径
  * 参数值字符串需要用半角双引号 `"` 包裹
  * 可以是 oss 路径
* 示例：

```prototxt
# my_config.prototxt

output_path: "./compiled.joint"
```

### input_type
* 参数路径：
  *  input_type
* 参数作用：
  * 明示输入模型的类型
  * 如果此字段被省略，则编译器将按模型文件名称的后缀名自动推断。有的时候推断结果可能不是期望的
* 参数类型：一个枚举类型值。可选项：
  * `INPUT_TYPE_AUTO`：按输入模型文件的后缀名，自动识别输入模型类型
  * `INPUT_TYPE_ONNX`
  * `INPUT_TYPE_MAGMA_JOINT`: deprecated
  * `INPUT_TYPE_LAVA_JOINT`
  * `INPUT_TYPE_MAGMA`
  * `INPUT_TYPE_LAVA`
  * 被省略：等于同 `INPUT_TYPE_AUTO`
* 示例：
  * 注意枚举参数值不要带双引号

```prototxt
# my_config.prototxt

input_type: INPUT_TYPE_ONNX
```

### output_type
* 参数路径：
  * output_type
* 参数作用：
  * 指定输出模型的类型
* 参数类型：一个枚举类型值。可选项：
  * `OUTPUT_TYPE_AUTO`：按输出模型文件的后缀名，自动识别输入模型类型
  * `OUTPUT_TYPE_MAGMA_JOINT`: deprecated
  * `OUTPUT_TYPE_LAVA_JOINT`
  * `OUTPUT_TYPE_JOINT`
  * 被省略：等于同 `OUTPUT_TYPE_AUTO`
* 示例：
  * 注意枚举参数值不要带双引号

```prototxt
# my_config.prototxt

output_type: OUTPUT_TYPE_JOINT
```

### target_hardware
* 参数路径：
  * target_hardware
* 参数作用：
  * 指定编译输出模型所适用的硬件平台
* 参数类型：一个枚举类型值。可选项：
  * `TARGET_HARDWARE_NONE`
  * `TARGET_HARDWARE_AX630`
  * `TARGET_HARDWARE_AX620`
  * `TARGET_HARDWARE_AX650`
* 示例：

```prototxt
# my_config.prototxt

target_hardware: TARGET_HARDWARE_AX630
```

### src_input_tensors
* 参数路径：
  * src_input_tensors
* 参数作用：
  * 用于描述**输入模型**的**输入 tensor** 的属性
* 参数类型：结构体数组
  * [结构体字段](/super_pulsar/config/config_tensor_config.md)

### src_output_tensors
* 参数路径：
  * src_output_tensors
* 参数作用：
  * 用于描述**输入模型**的**输出 tensor** 的属性
* 参数类型：结构体数组
  * [结构体字段](/super_pulsar/config/config_tensor_config.md)

### dst_input_tensors
* 参数路径：
  * dst_input_tensors
* 参数作用：
  * 用于修改**输出模型**的**输入 tensor** 的属性
* 参数类型：结构体数组
  * [结构体字段](/super_pulsar/config/config_tensor_config.md)

### dst_output_tensors
* 参数路径：
  * dst_output_tensors
* 参数作用：
  * 用于修改**输出模型**的**输出 tensor** 的属性
* 参数类型：结构体数组
  * [结构体字段](/super_pulsar/config/config_tensor_config.md)

### deploy_modifications
* 参数路径：
  * deploy_modifications
* 参数作用：
  * 配合 QAT-V1 业务修改模型
* 参数类型：结构体数组。结构体字段：
  * `type`: 一个枚举值。可选项：
    * `MOD_TYPE_LEGACY`: 默认值。默认为 GLOBAL_SOFTMAX
    * `MOD_TYPE_GLOBAL_SOFTMAX`: 对所有输出加 Softmax 层
* 示例：

```prototxt
# my_config.prototxt

deploy_modifications {
    type: MOD_TYPE_LEGACY
}
deploy_modifications {
    type: MOD_TYPE_GLOBAL_SOFTMAX
}
```

### qat_convert_mode
* 参数路径：
  * qat_convert_mode
* 参数作用：
  * 指定 QAT 模型转换模式
* 参数类型：一个枚举类型值。可选项：
  * `QAT_CONVERT_MODE_POST_NW`：默认值。使用 NeuWizard 转 qat 模型
  * `QAT_CONVERT_MODE_PRE_NW`：使用 QAT-Eruptor 转 qat 模型
* 示例：

```prototxt
# my_config.prototxt

qat_convert_mode: QAT_CONVERT_MODE_PRE_NW
```

### input_tensors [deprecated]
* **不推荐使用**。请使用 [src_input_tensors](#src_input_tensors)

### output_tensors [deprecated]
* **不推荐使用**。请使用 [dst_input_tensors](#dst_input_tensors)

### quantization_value [deprecated]
* 已废弃

### qunaitzation_type [deprecated]
* 已废弃
