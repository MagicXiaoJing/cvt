# 基本操作

## pulsar build 的 --config 和 --output_config 有什么区别？
* `--config` 是用户友好的 prototxt ，提供了多种简化配置的语法糖
* `--output_config` 会展开所有语法糖，并保存成工具链友好的配置文件，**不是必须的**
* 现阶段 pulsar run 仿真功能需要使用 `--output_config` 生成的配置文件

## pulsar build 能把什么类型的模型编译至什么类型？
* 详见[此处](/super_pulsar/pulsar_build.md)。

## pulsar build 中输出的仿真 fps 与上板实测 fps 差距较大
* 需要针对模型具体分析，一般差异原因有以下两点
    * neu fps 较大是由于板上 DDR 不受限，而 pulsar 仿真严格卡了 ddr 带宽
    * neu fps 较小是由于 Pulsar 仿真的 Cycle Model 中，之前省去的小量（比如 LUT 配置时间），在某些 case 下变得不可忽略

## 如何在 Prototxt 中配置多 Batch?
* 示例

```prototxt
# 无 tasks 配置文件参数路径：pulsar_conf
# 有 tasks 配置文件参数路径：tasks.pulsar_conf

pulsar_conf {
  batch_size: 2  # 使编译的模型推理时的 batch size 为 2
}
```

* [详情](/super_pulsar/config/config_pulsar_conf.md#batch_size)

## 如何在 Prototxt 中配置动态 Batch?
* 示例

```prototxt
# 无 tasks 配置文件参数路径：pulsar_conf
# 有 tasks 配置文件参数路径：tasks.pulsar_conf

pulsar_conf {
    batch_size_option: BSO_DYNAMIC # 编译后的模型支持动态 batch
    batch_size: 1                  # 实际推理时常用 batch_size
    batch_size: 2                  # 实际推理时常用 batch_size
    batch_size: 4                  # 最大 batch_size 为 4
}
```

* [详情](/super_pulsar/config/config_pulsar_conf.md#batch_size)

## 怎么设 VNPU
* 方式一：通过 `pulsar build` 命令行参数设置
  * 使用 `--target_hardware` 参数指定芯片类型，如 `AX630`。[详情](/super_pulsar/pulsar_build.md#`--target_hardware`)
  * 使用 `--virtual_npu` 参数指定虚拟 NPU 核，如 `221`。[详情](/super_pulsar/pulsar_build.md#`--virtual_npu`)

* 方式二：在配置文件里面设置
  * 使用 [target_hardware](/super_pulsar/config/config_without_tasks.md#target_hardware) 指定芯片类型，如：`target_hardware: TARGET_HARDWARE_AX620`
  * 使用 [pulsar_conf.virtual_npu](/super_pulsar/config/config_pulsar_conf.md#virtual_npu) 指定 AX630 的虚拟 NPU 核，如 `virtual_npu: VIRTUAL_NPU_MODE_311`
  * 使用 [pulsar_conf.ax620_virtual_npu](/super_pulsar/config/config_pulsar_conf.md#ax620_virtual_npu) 指定 AX620 的虚拟 NPU 核，如 `ax620_virtual_npu: AX620_VIRTUAL_NPU_MODE_111`

## onnx 模型输入是 RGB，期望转出来的 joint 模型也是 RGB 输入，应该如何操作
* 在配置文件中设置。示例：

```prototxt
dst_input_tensors {
  color_space: TENSOR_COLOR_SPACE_RGB
}
```

# 常识
## 转出来的 .joint 模型可以像之前 .neu 模型上板跑么？
* 可以
* 事实上 joint 模型是当前主流上板模型格式。neu 模型是旧版格式，Super Pulsar 可以[将 neu 模型转换成 joint 模型](/use_case/neu_to_joint.md)

## PTQ ONNX -> Joint 校正和转换有没办法提速？
可以按如下方法设置配置文件中相应选项来提高转模型的速度：
* 可以将 [dataset_conf_calibration](/super_pulsar/config/config_neuwizard_conf.md#dataset_conf_calibration) 的 [size](/super_pulsar/config/neuwizard_conf/dataset.md#size) 设置小一些（注意：精度会有损失）
* 可以关掉一些 debug 选项：
  * [global_conf](/super_pulsar/config/config_neuwizard_conf.md#global_conf) 里 dump 开头的全部置成 false
  * [pulsar_conf](/super_pulsar/config/config_pulsar_conf.md) 里 [debug](/super_pulsar/config/config_pulsar_conf.md#debug) 置为 false

## ptq 能跑 GPU 吗？
* 工具链本身是支持的，但 docker 本身出于大小考虑没用 nvidia 基础镜像

## dataset_output_type 默认是 BGR，是否指使用数据集里的图片校正时使用的是 BGR 格式输入到模型。如果是的话 config.prototxt 里的 mean 和 std 是不是也要按照 BGR 顺序设置？
* 是的
* dataset_output_type 值是 BGR 代表编译时是按照 BGR 格式来读取的校正图片数据，从而 mean/std 也要按 BGR 顺序设置

## override shape 中的常见值为 [0, 0, rggb height, rggb width]， 为什么前面两维是 0 呢？当前编译工具支持变 channel 吗，例如配置一个 16-channel 的输入？
* override_input_shape 设置的初衷是修改输入分辨率(height & width), 0 代表该位置的 shape 值不做修改, 现在不支持变 channel
* 详细信息请查阅 [override_input_shape](/super_pulsar/config/neuwizard_conf/operator_conf.md#override_input_shape) 的介绍

## 在 TASK API 中，每一个 task 里都要配置 target_hardware: TARGET_HARDWARE_AX620， 这导致每一个 task 都要配置一下目标芯片。但想象中应该是同一个 target hardware?
* 编译任务是以一个 task 为基本单位执行的。每个 task 之间的设置是独立的, 所以目前是需要为每一个 task 设定一次 target_hardware


# QAT

## magma 是怎么从 model.py 出来的？会集成到 Super Pulsar 吗？
* 导出 magma 是 Axion 的功能，Super Pulsar 包含这个组件，但实际上是不同的工作流程
* 用户训练时使用 Axion 应当参照 QAT 白皮书和 axnet-examples

## 客户拿到的完整工具包是怎样的？
* 2020 年版是 白皮书、`neutron_toolchain`（包含 axnet-examples 等）
* 2021 下半年会是 白皮书、axnet-examples、SuperPulsar

## 在 config.prototxt 中的 input_modifications 类中包含了 blob_pack / affine_kb / space_to_depth / shape 等操作， 他们有先后顺序之分么？
* 有先后顺序之分。先后顺序是依照 input_modifications 中的顺序依次执行，但 [override_input_shape](/super_pulsar/config/neuwizard_conf/operator_conf.md#override_input_shape) 除外
  * 不论 [override_input_shape](/super_pulsar/config/neuwizard_conf/operator_conf.md#override_input_shape) 放在哪个位置，都是第一个起作用
* 详细信息请查阅[前处理算子数组](/super_pulsar/config/neuwizard_conf/operator_conf.md#前处理算子数组)

## config.prototxt 中的 dataset_conf_calibration 是否用来量化校正，和 dataset_conf_error_measurement 是否用同一组图片？
* dataset_conf_error_measurement 这里的图片用来评估量化前后网络输出的差异，提供一个量化效果的参考。一般就会直用同一组，查看用来量化校正数据集在量化前后差异如何

## magma 编译完， 上板输出顺序发生了改变，可以修改上板输出顺序吗
* magma 的定义中有 outputs 域，通过修改此处可以改变输出顺序
* super_pulsar/tools 下 modify_magma_ioinfo.py 脚本提供修改输入输出的功能
   * 假设 xxx.magma 输出为 o3, o1, o2, 通过下面的命令可以修改为 o1, o2, o3 输出，并保存为 yyy.magma
   ```bash
   python3 modify_magma_ioinfo.py --magma xxx.magma --outputs o1 o2 o3 --output_magma yyy.magma
   ```

# BSP 速度

## 跟 19A 的速度比较，怎么知道现状是否合理？
* 需要 case-by-case 分析，客户模型一般都针对 Hisi 高度优化，有一定油水

# Q 值

## Q 值是 int16 吗？
* 不是。Q 值类型可配，详见[说明](/super_pulsar/config/config_tensor_config.md#quantization_value)

##  如何在 config.prototxt 中配置 Q 值？
* 示例：

```prototxt
dst_output_tensors {
  data_type: INT16
}
```

## Q 值的 CPU 子图时间怎么算？
* Q 值没有 CPU 子图，但遗留了 `/Q` 的算术操作到客户的后处理代码

## Q 值不还是要接 CPU 做除法，没有省时间啊？
* 是要接 CPU ，但 `/Q` 操作可以跟其他操作耦合起来，大部分情况都是几乎免费的
    * 比如检测后处理步骤 NMS 后需要做除法，则 `分母*Q` 即可
    * 检测网络单独做大 tensor 乘法，可能需要 NPU 数倍时间， NMS 后计算量小

## 转出模型的 Q 值接口是什么
* 直接**上板执行** `run_joint model.joint`，在日志会有打印
* joint sdk 中 C++ 接口也有 nQuantizationValue

# Joint 输出 NHWC Layout （现状：科学化支持前）
现状是靠一段脚本来强行将 Joint 中 onnx 子图中的所有算子强行转换成在 NHWC 域上进行操作。之后会科学化修改到在 Neuwizard 中支持。

## 现在有哪些 onnx 算子能够支持 NHWC Layout？
* 现状是 onnx 子图中只有如下这些算子才能支持 Joint NHWC Layout 变换。
  * `Cast`, `Constant`, `Slice`, `Mul`, `Add`, `Flatten`, `Softmax`, `Sigmoid`
* 特别地，对于任意一个 onnx 算子，只要它的行为与 Layout 无关，则可以直接快速地在 Super Pulsar 中予以支持。
  * 比如 `Abs`, `Ceil`, `Cos`, `Exp` 等
