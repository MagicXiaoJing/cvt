## DatasetConfiguration
DatasetConfiguration 用于描述训练、校准、对分过程中需要的数据集
* 参数路径：
  * 训练：[neuwizard_conf](/super_pulsar/config/config_neuwizard_conf.md).[dataset_conf_train](/super_pulsar/config/config_neuwizard_conf.md#dataset_conf_train)
  * 校准：[neuwizard_conf](/super_pulsar/config/config_neuwizard_conf.md).[dataset_conf_calibration](/super_pulsar/config/config_neuwizard_conf.md#dataset_conf_calibration)
  * 对分：[neuwizard_conf](/super_pulsar/config/config_neuwizard_conf.md).[dataset_conf_error_measurement](/super_pulsar/config/config_neuwizard_conf.md#dataset_conf_error_measurement)

### 示例
```prototxt
dataset_conf_train {
    path: "imagenet-1k-images.tar" # 一个具有 1000 张 jpg 格式图片的 tar 包
    type: DATASET_TYPE_TAR
    size: 1
    batch_size: 1
}
```

### 字段说明
#### type
枚举类型。用于指定数据集类型。可选项：
* `DATASET_TYPE_NONE`: 默认值
* `DATASET_TYPE_DUMMY`: fake data wrt model input
* `DATASET_TYPE_PYMODULE`: import module
* `DATASET_TYPE_PYFILE`: import from independent python file
* `DATASET_TYPE_MEMORY`: torch.Dataset in memory
* `DATASET_TYPE_TAR`: dataset represented by tar
* `DATASET_TYPE_ONE_SAMPLE`: one sample dataset (experimental, test only)

提示：
* QAT 模型一般选用 DATASET_TYPE_DUMMY

#### size
一个整数。用于表示数据集大小，会从全集里随机采样

#### batch_size
一个整数。用于转模型过程中，内部参数训练、校准或误差检测时所使用数据的 batch_size，默认值为 32

#### data_generation_conf
一个结构体。用于设置生成数据的属性。**此参数只有在数据集用于训练时才会使用到**。字段说明：
* `enable_data_generation`: 一个布尔值。默认值为 `false` 不生成数据；当参数值为 `true` 时生成数据
* `initialize_with_provided_data`: 一个布尔值。默认值为 `false` 不使用指定的数据初始化；当参数值为 `true` 时使用指定的数据初始化
* `number_of_generated_samples`: 一个整数。当时 `enable_data_generation` 的值为 `true` 时，指定生成的数据的数量
* `batch_size`: 一个整数
* `learning_rate`: 一个浮点数
* `weight_of_bn_loss`: 一个浮点数
* `weight_of_class_loss`: 一个浮点数
* `weight_of_image_l2_loss`: 一个浮点数
* `weight_of_image_prior_loss`: 一个浮点数
* `decay_of_bn_loss`: 一个浮点数
* `momentum`: 一个浮点数
* `save_interval`: 一个浮整数。保存数据时的迭代间隔

#### dataset_conf_items
结构体数组。用于分别为一个模型的每个输入指定数据集
* 结构体字段说明：
  * `path`: 一个字符串。表示 tar 文件的路径
    * 可以是 oss 路径
  * `resize_conf`: 一个结构体。描述如何缩放图像数据。[结构体字段说明](#ResizeConfiguration)
  * `selector`: 一个结构体。描述此数据集将用于模型的哪一个输入。[input_conf_items.selector](/super_pulsar/config/config_operator_configuration.md#input_conf_items.selector)
* 注意：
  * 此参数需要 [type](#type) 的值是 `DATASET_TYPE_TAR` 才有意义
  * 当有多个 tar 结构的数据集时，tar 包里文件总数不一致，或文件名不一致时，编译过程中可能会输出相关 log

#### calibration_range_expander
【experimental】
* 参数类型: 一个浮点数

#### calibration_strategy
指定校准策略。可选项：
* `CALIB_STRATEGY_DEFAULT`：默认值。`CALIB_STRATEGY_PER_CHANNEL`
* `CALIB_STRATEGY_PER_CHANNEL`
* `CALIB_STRATEGY_PER_TENSOR`

#### observer_method
指定观察方法。可选项：
* `OBSVR_METHOD_DEFAULT`: 默认值。`OBSVR_METHOD_MIN_MAX`
* `OBSVR_METHOD_MIN_MAX`
* `OBSVR_METHOD_MSE_CLIPPING`
* `OBSVR_METHOD_PERCENTILE`

#### path
一个字符串。用于指定 tar 格式的数据集文件

#### target_data_type
已废弃
* 一个枚举值。可选项：
  * `DATASET_OUTPUT_TYPE_AUTO`: 默认值。根据模型输入 channel 数自动识别：
    * 3-channel: BGR
    * 1-channel: GRAY
    * 4-channel: RAW
  * `DATASET_OUTPUT_TYPE_BGR`
  * `DATASET_OUTPUT_TYPE_RGB`
  * `DATASET_OUTPUT_TYPE_GRAY`
  * `DATASET_OUTPUT_TYPE_FEATUREMAP`
  * `DATASET_OUTPUT_TYPE_RAW`
  * `DATASET_OUTPUT_TYPE_NV12`
  * `DATASET_OUTPUT_TYPE_NV21`

#### resize_conf
* 参数类型: 一个结构体
* 作用：描述如何缩放图像数据。[结构体字段说明](#ResizeConfiguration)


### ResizeConfiguration
* 作用：描述如何缩放图像数据
* 参数类型：结构体。字段说明：
  * `keep_aspect_ratio`: 一个布尔值。默认值 `false`，表示缩放时不保持比例。当为 `true` 时，表示缩放时保持比例
  * `pad_value`: 一个浮点数。pad 数值，值域为 [0, 255]
  * `pad_mode_h`: 一个枚举值。表示图像 height 方向上的 pad 模式。可选项：
    * `PAD_TOP_BOTTOM`：上下等距 pad
    * `PAD_ONLY_TOP`: 下对齐，只 pad 上侧
    * `PAD_ONLY_BOTTOM`: 上对齐，只 pad 下侧
  * `pad_mode_w`: 一个枚举值。表示图像 width 方向上的 pad 模式。可选项：
    * `PAD_LEFT_RIGHT`：左右等距 pad
    * `PAD_ONLY_LEFT`：右对齐，只 pad 左侧
    * `PAD_ONLY_RIGHT`：左对齐，只 pad 右侧
