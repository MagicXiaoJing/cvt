# 有 task 的配置文件
***当前作为实验版本***

* [配置文件基础](/super_pulsar/config.md)
* [多 tasks 级联](config_multi_tasks.md)

## 概述
* 有 tasks 的配置文件是当前最新的配置文件形式。***当前作为实验版本***
  * [旧版配置文件](/super_pulsar/config/config_without_tasks.md)
* 注意：
  * 如果一个配置文件中有 tasks 结构体参数，那么定义在 tasks 外面的参数将会被忽略， `input_task_names` 和 `output_task_names` 除外

```prototxt
# my_config.prototxt

target_hardware: TARGET_HARDWARE_AX620 # 无效的参数

tasks {
    name: "task1"
    target_hardware: TARGET_HARDWARE_AX630 # 有效的参数
}
input_task_names: "task1"  # 有效的参数
output_task_names: "task1" # 有效的参数
```

## 配置文件示例

```prototxt
tasks {
    name: "compile_onnx"
    input_model_items {
        model {
            type: MODEL_TYPE_ONNX
            path: "LeNet5.onnx"
        }
    }
    output_model_items {
        model {
            type: MODEL_TYPE_JOINT
            path: "./LeNet5.joint"
        }
    }
    target_hardware: TARGET_HARDWARE_AX630
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
    pulsar_conf {
        batch_size: 1
        debug: true
    }
}
input_task_names: "compile_onnx"
output_task_names: "compile_onnx"
```

## 配置文件字段说明
### tasks
* 参数路径：
  * tasks
* 参数类型：结构体数组
* 参数作用：
  * 一个 tasks 用于描述一个编译过程或一个编译步骤。因此，有 tasks 的配置文件一次可以描述多个编译过程或编译步骤
  * 编译步骤之间有先后关系和依赖关系。其中：
    * 编译步骤的先后关系，由对应的 `tasks` 结构体在配置文件中出现的先后顺序决定。即由 `tasks` 结构体在数组中的顺序决定
    * 编译步骤的依赖关系，由对应的 `tasks` 结构体中的 `input_model_items` 域来指定
      * `input_model_items` 描述了当前 `tasks` 所对应的编译步骤的输入模型来自于**前面**的哪一个或哪几个 `tasks` 所对应的编译步骤
    * 排在第一位或前几位的 `tasks` 所对应的编译步骤的输入模型来自于外部输入
      * `input_model_items` 是一个 union 类型，它既可以描述其它 `tasks` 的输出模型，也可以描述外部输入模型
  * 每一个编译步骤的编译参数按不同的编译步骤而定。`tasks` 结构体中有很多参数，这些参数用于不同的编译过程

tasks 结构体的字段说明如下

### tasks.name
* 参数路径：
  * tasks.name
* 参数类型：一个字符串
* 参数作用：
  * 给 tasks 命名。注意多个 tasks 之间不能重名
* 参数示例：

```prototxt
# my_config.prototxt

# 以下两个 tasks 组成一个数组
tasks {
    name: "task0"
}

tasks {
    name: "task1"
}
```

### tasks.input_model_items
* 参数路径：
  * tasks.input_model_items
* 参数作用：
  * 用于指定当前 tasks 的输入模型。可以指定多个。当前 tasks 的输入模型来源可以有两种：
    * 输入模型来源一：[来自已有模型](#model)
      * 参数路径：tasks.input_model_items.model
    * 输入模型来源二：[来自其它 tasks 输出的模型](#task_output)
      * 参数路径：tasks.input_model_items.task_output
* 参数类型：结构体数组。[结构体字段说明](#ModelItem)

### tasks.output_model_items
* 参数路径：
  * tasks.output_model_items
* 参数作用：
  * 用于指定当前 tasks 的输出模型。可以指定多个。
* 参数类型：结构体数组。[结构体字段说明](#task_output)
* 示例：

```prototxt
# 请按参数路径，将如下内容放入配置文件的正确位置

output_model_items {
	model {
		path_v2 {
			path: "compiled.joint"   # 设置输出模型的相对路径
			mode: PATH_MODE_REL_EXEC # 设置输出模型的相对路径的参考点为执行命令时所在的目录
		}
		type: MODEL_TYPE_JOINT
	}
}
```

### tasks.target_hardware
* 参数路径：
  * tasks.target_hardware
* 参数作用：
  * 指定编译输出模型所适用的硬件平台
* 参数类型：一个枚举类型值
* 可选项：
  * `TARGET_HARDWARE_NONE`
  * `TARGET_HARDWARE_AX630`
  * `TARGET_HARDWARE_AX620`
  * `TARGET_HARDWARE_AX650`
* 示例：

```prototxt
# my_config.prototxt

target_hardware: TARGET_HARDWARE_AX630
```

### tasks.qat_convert_mode
* 参数路径：
  * tasks.qat_convert_mode
* 参数作用：
  * 指定 QAT 模型转换模式
* 参数类型：一个枚举类型值
* 可选项：
  * `QAT_CONVERT_MODE_POST_NW`：默认值。使用 NeuWizard 转 qat 模型
  * `QAT_CONVERT_MODE_PRE_NW`：使用 QAT-Eruptor 转 qat 模型
* 示例：

```prototxt
# my_config.prototxt

qat_convert_mode: QAT_CONVERT_MODE_PRE_NW
```

### tasks.enable_progress_bar
* 参数路径：
  * tasks.enable_progress_bar
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

### tasks.cpu_backend_settings
* 参数路径：
  * tasks.cpu_backend_settings
* 参数作用：
  * 控制编译后模型采用的 CPU 后端模式，目前有 ONNX 与 AXEngine 可选
* 参数类型：一个结构体。结构体字段：
  * [结构体字段](/super_pulsar/config/config_cpu_backend_settings.md)

### tasks.neuwizard_conf
* 参数路径：
  * tasks.neuwizard_conf
* 参数作用：
  * 编译器子工具 NeuWizard 的配置参数
  * neuwizard_conf 用于指导 NeuWizard 将 onnx 或 magma 格式的模型编译成 lava_joint 格式
* 参数类型：一个结构体。结构体字段：
  * [结构体字段](/super_pulsar/config/config_neuwizard_conf.md)

### tasks.pulsar_conf
* 参数路径:
  * tasks.pulsar_conf
* 参数作用：
  * 编译器子工具 pulsar_compiler 的配置参数
  * 用于指导 pulsar_compiler 将 lava_joint 或 lava 格式的模型编译成 joint 或 neu 格式的模型
* 参数类型：一个结构体
  * [结构体字段](/super_pulsar/config/config_pulsar_conf.md)

### tasks.joint_conf
* 参数路径：
  * tasks.joint_conf
* 参数作用：
  * 用于 lava_joint [模型焊接](joint_conf/config_joint_conf_fuse_model.md)
    * [详情](joint_conf/config_joint_conf_fuse_model.md)
  * 用于[制作具有多份 weights 的 joint 模型](/use_case/mcode_wbt.md)等编译过程的参数配置
    * [详情](/use_case/mcode_wbt.md)

### tasks.deploy_modifications
* 参数路径：
  * tasks.deploy_modifications
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

## 高级用法
* [多 tasks 级联](config_multi_tasks.md)

## ModelItem
### task_output
此参数与 [model](#model) 二选一使用。用于表示当前 tasks 以哪个 tasks 的**输出**作为**输入**
* 注意：
  * 虽然此参数名称是 **task_output**，但是它并**不是用来描述一个 tasks 的输出**模型，而是用来描述 tasks 的**输入模型**
  * 要描述一个 tasks 的输出模型，请使用 tasks.[output_model_items](#tasks.output_model_items).model
* 参数类型：一个结构体。结构体字段说明：
  * `name`: 一个字符串。指示**提供模型的 tasks** 的名称
  * `output_idx`: 一个整数。指示**提供模型的 tasks** 的**输出模型**的索引
* 返回：
  * [tasks.input_model_items](#tasks.input_model_items)

### model
此参数与 [task_output](#task_output) 二选一使用。用于表达当前 tasks 的输入模型来源于原始输入模型
* 参数类型：一个结构体。结构体字段说明：
  * `path`: 一个字符串。指示模型文件的路径。如果是相对路径，则它是默认相对配置文件的路径。此参数和 `path_v2` 二选一使用
    * 可以是 oss 路径
  * `path_v2`: 一个结构体。指示模型文件的相对路径，可设置相对路径的参考点。此参数和 `path` 二选一使用。字段说明：
    * `path`: 一个字符串。指示模型的路径。它是一个相对路径，相对路径的参考点由当前结构体的 `mode` 字段决定
    * `mode`: 一个枚举值。用于指示当前结构体中 `path` 字段所描述的相对路径的参考点。可选项：
      * `PATH_MODE_REL_CONF`: 默认选项。告诉编译器，`path` 所描述的相对路径的起点是配置文件所在目录
      * `PATH_MODE_REL_EXEC`: 告诉编译器，`path` 所描述的相对路径的起点是执行 `Super Pulsar` 时所在的目录
  * `type`: 一个枚举值。指示模型的类型。可选项：
    * `MODEL_TYPE_AUTO`: 默认值。按模型文件的后缀名自动推断模型类型
    * `MODEL_TYPE_ONNX`
    * `MODEL_TYPE_MAGMA`
    * `MODEL_TYPE_MAGMA_JOINT`
    * `MODEL_TYPE_LAVA`
    * `MODEL_TYPE_LAVA_JOINT`
    * `MODEL_TYPE_NEU`
    * `MODEL_TYPE_JOINT`
    * `MODEL_TYPE_ONNX_JOINT`
  * `input_tensors`: 结构体数组。用于输入模型的输入张量配置。[结构体字段说明](/super_pulsar/config/config_tensor_config.md)
  * `output_tensors`: 结构体数组。用于输入模型的输出张量配置。[结构体字段说明](/super_pulsar/config/config_tensor_config.md)
* 返回：
  * [tasks.input_model_items](#tasks.input_model_items)
  * [tasks.output_model_items](#tasks.output_model_items)
