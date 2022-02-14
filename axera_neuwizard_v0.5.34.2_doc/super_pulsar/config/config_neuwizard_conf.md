## neuwizard 配置
* 参数路径：
  * [无 tasks 配置文件](/super_pulsar/config/config_without_tasks.md): neuwizard_conf
  * [有 tasks 配置文件](/super_pulsar/config/config_with_tasks.md): tasks.neuwizard_conf
* 快速跳转：
  * [附加算子](neuwizard_conf/operator_conf.md)
* 参数类型：结构体
* 参数作用：用于指导 neuwizard 工具将 onnx 或 magma 格式型编译成 lava_joint 格式模型
* 配置参数示例：

```prototxt
# 注意按照参数路径将以下内容放入到配置文件中的正确位置

neuwizard_conf {
    global_conf {
        dump_bitmacs: true              # 将 bitmacs 输出到文件
        dump_name_history_mermaid: true # 将 名字继承关系图 输出到文件
        dump_planner_mermaid: true      # 将 schedule 过程 输出到文件
        dump_intermediate_mermaid: true # 将 中间阶段 IR 输出到文件
    }
    operator_conf {
        input_conf_items {
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
            }
        }
    }
    dataset_conf_calibration {
        path: "imagenet-1k-images.tar" # 一个具有 1000 张 jpeg 格式图片的 tar 包，用于在编译过程中做校准
        type: DATASET_TYPE_TAR         # 数据集格式为 tar 包
        size: 16
    }
    dataset_conf_error_measurement {
        path: "imagenet-1k-images.tar" # 在编译过程中用于对分
        type: DATASET_TYPE_TAR
        size: 4
        batch_size: 4
    }
    evaluation_conf {
        path: "neuwizard.evaluator.error_measure_evaluator" # 指定评估模块路径
        type: EVALUATION_TYPE_ERROR_MEASURE                 # 评估的方式是对分
        source_ir_types: IR_TYPE_NATIVE
        ir_types: IR_TYPE_LAVA
    }
}
```

* 字段说明：
### global_conf
* 参数路径：
  * neuwizard_conf.global_conf
* 参数作用：neuwizard 的通用配置信息
* 参数类型：一个结构体。字段说明：
  * `dump_planning_history`: 一个布尔值。值为 `true` 时将**变换历史**输出到文件。默认为 `false`，不输出变换历史
  * `dump_name_history_mermaid`: 一个布尔值。值为 `true` 时将**名字继承关系图**输出到文件。默认为 `false`，不输出名字继承关系图
    * mermaid 文件可使用 [pulsar view](/super_pulsar/pulsar_view.md) 命令查看
  * `dump_planner_mermaid`: 一个布尔值。值为 `true` 时将 **schedule 过程**输出到文件。默认为 `false`，不输出 schedule 过程
    * mermaid 文件可使用 [pulsar view](/super_pulsar/pulsar_view.md) 命令查看
  * `dump_intermediate_mermaid`: 一个布尔值。值为 `true` 时将**中间阶段 IR** 输出到文件。默认为 `false`，不输出中间阶段 IR
    * mermaid 文件可使用 [pulsar view](/super_pulsar/pulsar_view.md) 命令查看
  * `operator_strict_mode`: 一个布尔值。值为 `true` 时开启特性。默认为 `false`，不开启
  * `operator_shape_inference`: 一个布尔值。值为 `true` 时开启特性。默认为 `false`，不开启
  * `lava_with_data`: 一个布尔值。值为 `true` 时开启特性。默认为 `false`，不开启
  * `target_hardware`: 指示硬件平台。可选项
    * `TARGET_HARDWARE_NONE`: 默认值。默认为 `TARGET_HARDWARE_AX630`
    * `TARGET_HARDWARE_AX630`
    * `TARGET_HARDWARE_AX620`
  * `error_measure_with_onnx_model`: 一个布尔值。值为 `true` 时打开调试选项与 onnx 模型对分开关。默认为 `false`，不开启
  * `enable_add2blend`: 一个布尔值。值为 `true` 时打开一个 Hack Flag for add2blend。默认为 `false`，不开启
  * `enable_progress_bar`: 一个布尔值。值为 `true` 时编译过程中显示进度条。默认为 `false`，不显示进度条
  * `dump_bitmacs`: 一个布尔值。值为 `true` 时将 **bitmacs** 输出到文件。默认为 `false`，不输出 bitmacs
  * `validation_mode`: 可选项
    * `VALIDATION_AUTO`: 默认值
    * `VALIDATION_DISABLED`
    * `VALIDATION_ENABLED`

### task_conf
* 参数路径：
  * neuwizard_conf.task_conf
* 参数类型：一个结构体。[字段说明](neuwizard_conf/task_conf.md)

### operator_conf
* 参数路径：
  * neuwizard_conf.operator_conf
* 参数类型：一个结构体。[字段说明](neuwizard_conf/operator_conf.md)

### dataset_conf_train
* 参数路径：
  * neuwizard_conf.dataset_conf_train
* 作用：
  * 用于描述训练数据集
* 参数类型：一个结构体。[字段说明](/super_pulsar/config/neuwizard_conf/dataset.md#DatasetConfiguration)

## dataset_conf_calibration
* 参数路径：
  * neuwizard_conf.dataset_conf_calibration
* 作用：
  * 用于描述编译过程中的校准数据集
* 参数类型：一个结构体。[字段说明](/super_pulsar/config/neuwizard_conf/dataset.md#DatasetConfiguration)

## dataset_conf_error_measurement
* 参数路径：
  * neuwizard_conf.dataset_conf_error_measurement
* 作用：
  * 用于描述编译过程中用于误差测试的数据集
* 参数类型：一个结构体。[字段说明](/super_pulsar/config/neuwizard_conf/dataset.md#DatasetConfiguration)

## evaluation_conf
* 参数路径：
  * neuwizard_conf.evaluation_conf
* 数据类型：一个结构体。字段说明：
  * `path`：一个字符串。指定模块路径
  * `type`：一个枚举值。可选项：
    * `EVALUATION_TYPE_NONE`
    * `EVALUATION_TYPE_ERROR_MEASURE`
    * `EVALUATION_TYPE_PYMODULE`
  * `source_ir_types`: 指定编译过程中对分时需要对比的中间表达名称。可选项：
    * `IR_TYPE_CAFFE`
    * `IR_TYPE_ONNX`
    * `IR_TYPE_NATIVE`
    * `IR_TYPE_AXION`
    * `IR_TYPE_MAGMA`
    * `IR_TYPE_LAVA`
  * `ir_types`: 指定编译过程中对分时需要对比的中间表达名称，或者编译过程中计算精度时需要使用的中间表达名称
    * 可选项同 `source_ir_types`
  * `score_compare_per_layer`: Debug 选项，目前专用于 QAT 模型逐层对分
* 示例：

```prototxt
    path: "neuwizard.evaluator.error_measure_evaluator"
    type: EVALUATION_TYPE_ERROR_MEASURE
    source_ir_types: IR_TYPE_MAGMA
    ir_types: IR_TYPE_LAVA
```
