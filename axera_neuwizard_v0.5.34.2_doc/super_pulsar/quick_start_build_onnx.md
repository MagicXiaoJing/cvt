## 编译 onnx 模型
### 准备工作
#### Super Pulsar 工具 docker 镜像
请确保本地安装了 Super Pulsar 工具的 docker 镜像。可通过以下命令查看：
```bash
tools@host:~$ docker image list
REPOSITORY        TAG        IMAGE ID       CREATED         SIZE
axera/neuwizard   0.5.32.5   9f75980741eb   8 weeks ago     3.65GB
axera/neuwizard   0.4.0      943dfd5eccec   8 months ago    3.31GB
```
示例中显示的 docker 镜像版本为 `axera/neuwizard-v0.5.32.5`

#### 目标模型
准备一个 onnx 模型，如 LeNet5.onnx

#### 数据集
准备一个图片数据集，数据集大小可视需求而定，也可以准备一个比实际需求大的数据集

以下示例是从 imagenet 数据集中选取了 1000 张图片的数据集

```
.
└── imagenet-1k-images
    ├── img-0.jpg
    ├── img-1.jpg
    ├── img-2.jpg
...
    ├── img-998.jpg
    ├── img-999.jpg
```

将数据集打成 tar 包
```bash
tar cf imagenet-1k-images.tar imagenet-1k-images
```

### 配置文件
Super Pulsar 工具工作中需要[配置参数](/super_pulsar/config.md)，它们被放置在配置文件

将如下内容保存到文件 my_config.prototxt

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
        path: "imagenet-1k-images.tar" # 数据集图片的 tar 包，用于编译过程中对模型校准
        type: DATASET_TYPE_TAR         # 数据集类型：tar 包
        size: 256                      # 编译过程中校准所需要的实际数据个数为 256
    }
    dataset_conf_error_measurement {
        path: "imagenet-1k-images.tar" # 用于编译过程中对分
        type: DATASET_TYPE_TAR
        size: 4                        # 对分过程所需实际数据个数为 4
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

### 编译
在 docker 中执行如下命令。[了解更多](/super_pulsar/pulsar_build.md)
```bash
pulsar build --config my_config.prototxt

# 得到编译后模型：LeNet5.joint
```

