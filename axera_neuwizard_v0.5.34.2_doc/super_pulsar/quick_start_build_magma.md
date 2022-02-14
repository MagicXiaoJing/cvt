## 编译 magma 模型
### 准备工作
#### Super Pulsar 工具 docker 镜像
请确保本地安装了 Super Pulsar 工具的 docker 镜像。可通过以下命令查看：
```bash
tools@host:~$ docker image list
REPOSITORY        TAG        IMAGE ID       CREATED         SIZE
axera/neuwizard   0.5.32.5   9f75980741eb   8 weeks ago     3.65GB
axera/neuwizard   0.4.0      943dfd5eccec   8 months ago    3.31GB
```
示例中显示的 docker 镜像版本为 `axera/neuwizard:0.5.32.5`

#### 目标模型
准备一个 magma 模型，如 imagenet.magma

### 配置文件
Super Pulsar 工具工作中需要[配置参数](/super_pulsar/config.md)，它们被放置在配置文件

将如下内容保存到文件 my_config.prototxt

```prototxt
# my_config.prototxt

# 基本配置参数：输入输出
input_type: INPUT_TYPE_MAGMA
output_type: OUTPUT_TYPE_JOINT

# 选择硬件平台
target_hardware: TARGET_HARDWARE_AX630

# neuwizard 工具的配置参数
neuwizard_conf {
    operator_conf {
        # magma 输入数据域为 [0, 1]，但希望编译后的 joint 模型输入数据域为 [0, 255]，在这里补上 scale
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
    dataset_conf_error_measurement {
        type: DATASET_TYPE_DUMMY
        size: 4                          # 对分过程所需实际数据个数为 4
    }
}
# pulsar compiler 的配置参数
pulsar_conf {
    batch_size: 1
    debug: true
}
```

### 编译
在 docker 中执行如下命令。[了解更多](/super_pulsar/pulsar_build.md)
```bash
pulsar build --config my_config.prototxt --input imagenet.magma --output imagenet.joint

# 得到编译后模型：imagenet.joint
```
