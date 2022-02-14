## neuwizard TaskConfiguration
* 参数路径：
  * [无 tasks 配置文件](/super_pulsar/config/config_without_tasks.md): [neuwizard_conf](/super_pulsar/config/config_neuwizard_conf.md).[task_conf](/super_pulsar/config/config_neuwizard_conf.md#task_conf)
  * [有 tasks 配置文件](/super_pulsar/config/config_with_tasks.md): [tasks](/super_pulsar/config/config_with_tasks.md).[neuwizard_conf](/super_pulsar/config/config_neuwizard_conf.md).[task_conf](/super_pulsar/config/config_neuwizard_conf.md#task_conf)

### 示例
```prototxt
# 请按参数路径将以下内容放入到配置文件中正确的位置

task_conf {
    task_strategy: TASK_STRATEGY_FINETUNE_LSQ
    finetune_options {
        total_samples: 204800  # 200 epochs
        weight_decay: 1e-5
        learning_rate: 1e-4
        optimizer: OPTIMIZER_TYPE_DEFAULT
        lr_scheduler: LR_SCHEDULER_DEFAULT
        find_feature_strategy: FIND_FEATURE_STRATEGY_DEFAULT
        output_loss_options {
            ratio: 100
            decay: 1
            losstype: LOSS_TYPE_COSINE
        }
        feature_loss_options {
            ratio: 100
            decay: 0.8
            losstype: LOSS_TYPE_DEFAULT
        }
    }
}
```

### 字段说明：
#### input_path
此参数和 [caffe_input_path](#caffe_input_path) 二选一使用。用于指定输入模型（非 caffe 模型）路径
* 参数路径：
  * neuwizard_conf.task_conf.input_path
* 参数类型：一个字符串

#### caffe_input_path
此参数和 [input_path](#input_path) 二选一使用。用于指定输入模型（caffe 模型）路径
* 参数路径：
  * neuwizard_conf.task_conf.caffe_input_path
* 参数类型：一个结构体。字段说明：
  * `caffeproto`: 一个字符串。指定 prototxt 文件路径
  * `caffemodel`: 一个字符串。指定 caffemodel 文件路径

#### output_path
* 参数路径：
  * neuwizard_conf.task_conf.output_path
* 参数作用：
  * 用于设置输出模型的路径
* 参数类型：一个字符串

#### task_strategy
* 参数路径：
  * neuwizard_conf.task_conf.task_strategy
* 参数类型：一个枚举值。可选项：
  * `TASK_STRATEGY_DEFAULT`: 默认值
  * `TASK_STRATEGY_LAYERWISE_FINETUNE`
  * `TASK_STRATEGY_TWO_STAGE_FINETUNE`
  * `TASK_STRATEGY_FINETUNE_LSQ`
  * `TASK_STRATEGY_NO_CALIBRATION`: Use this when the input model is already legal on hardware. i.e. QAT.

#### input_type
* 参数路径：
  * neuwizard_conf.task_conf.input_type
* 参数作用：
  * 明示输入模型的类型
* 参数类型：一个枚举值。可选项：
  * `INPUT_TYPE_ONNX`
  * `INPUT_TYPE_MAGMA`
  * `INPUT_TYPE_ONNX_IR_OBJECT`
  * `INPUT_TYPE_MAGMA_IR_OBJECT`
  * `INPUT_TYPE_CAFFE`
  * `INPUT_TYPE_CAFFE_IR_OBJECT`
  * `INPUT_TYPE_JOINT_LAVA_ONNX`


#### output_type
* 参数路径：
  * neuwizard_conf.task_conf.output_type
* 参数作用：
  * 设置输出模型的类型
* 参数类型：一个枚举值。可选项：
  * `OUTPUT_TYPE_NULL`
  * `OUTPUT_TYPE_JOINT_MAGMA_ONNX`
  * `OUTPUT_TYPE_LAVA`
  * `OUTPUT_TYPE_MAGMA_IR_OBJECT`
  * `OUTPUT_TYPE_LAVA_IR_OBJECT`
  * `OUTPUT_TYPE_NULL_IR_OBJECT`
  * `OUTPUT_TYPE_JOINT_LAVA_ONNX`
  * `OUTPUT_TYPE_JOINT_LAVA_ONNX_IR_OBJECT`

#### finetune_options
**此参数一个临时的用于测试的参数**
* 参数路径：
  * neuwizard_conf.task_conf.finetune_options
* 参数类型：一个结构体。字段说明：
  * `total_samples`: 一个整数
  * `learning_rate`: 一个浮点数。默认值 1e-4
  * `weight_decay`：一个浮点数。默认值 1e-4
  * `optimizer`：一个枚举值。可选项：
    * `OPTIMIZER_TYPE_DEFAULT`: 默认值。默认为 `OPTIMIZER_TYPE_ADAMW`
    * `OPTIMIZER_TYPE_SGD`
    * `OPTIMIZER_TYPE_ADAMW`
  * `lr_scheduler`一个枚举值。调整学习率相关。可选项：
    * `LR_SCHEDULER_DEFAULT`: 默认值。默认为 `LR_SCHEDULER_COSINEANNEALINGWARMRESTARTS`
    * `LR_SCHEDULER_COSINEANNEALINGWARMRESTARTS`
  * `find_feature_strategy`：一个枚举值。可选项：
    * `FIND_FEATURE_STRATEGY_DEFAULT`
    * `FIND_FEATURE_STRATEGY_DOWNSAMPLES`
    * `FIND_FEATURE_STRATEGY_RECEPTIVE_FIELD_CHANGES`
  * `output_loss_options`：一个结构体。[LossOptions 字段说明](#LossOptions)
  * `feature_loss_options`：一个结构体。[LossOptions 字段说明](#LossOptions)
  * `evaluate_frequency`: 一个枚举值。可选项：
    * `EVALUATE_DEFAULT`
    * `EVALUATE_ALL`


###### LossOptions
* 字段说明：
  * `ratio`: 一个浮点数
  * `decay`: 一个浮点数
  * `losstype`: 一个枚举值。可选项：
    * `LOSS_TYPE_DEFAULT`: 默认值
    * `LOSS_TYPE_COSINE`
    * `LOSS_TYPE_KD`


#### npu_partition_none
此参数和 [npu_partition_by_max_operators](#npu_partition_by_max_operators)、[npu_partition_by_operator_names](#npu_partition_by_operator_names) 三选一使用。用于设置对算子的分割策略
* 参数路径：
  * neuwizard_conf.task_conf.npu_partition_none
* 参数类型：一个结构体。字段说明：无字段。此参数存在即代表不使用分割策略
* 示例：

```prototxt
# 请按参数路径将以下内容放入到配置文件中正确的位置

task_conf {
    # 告诉编译器，不使用任何分割策略
    npu_partition_none {
    }
}
```

#### npu_partition_by_max_operators
此参数和 [npu_partition_none](#npu_partition_none)、[npu_partition_by_operator_names](#npu_partition_by_operator_names) 三选一使用。用于设置对算子的分割策略
* 参数路径：
  * neuwizard_conf.task_conf.npu_partition_by_max_operators
* 参数类型：一个结构体。字段说明：
  * `max_number_of_operators`: 一个整数

#### npu_partition_by_operator_names
此参数和 [npu_partition_none](#npu_partition_none)、[npu_partition_by_max_operators](#npu_partition_by_max_operators) 三选一使用。用于设置对算子的分割策略
* 参数路径：
  * neuwizard_conf.task_conf.npu_partition_by_operator_names
* 参数类型：一个结构体。字段说明：
  * `operator_names`: 结构体数组。可以指定多个具名算子。结构体字段说明
    * `operator_name`: 字符串数组
