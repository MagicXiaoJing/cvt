## PulsarConfiguration
* 参数路径：
  * [无 tasks 配置文件](/super_pulsar/config/config_without_tasks.md): pulsar_conf
  * [有 tasks 配置文件](/super_pulsar/config/config_with_tasks.md): tasks.pulsar_conf
* 参数类型：结构体
* 参数作用：用于指导 pulsar_compiler 将 lava 格式或 lava_joint 格式模型编译成 neu 格式或者 joint 格式模型
* 配置参数示例

```prototxt
# 注意按照参数路径放入到配置文件的正确位置

pulsar_conf {
    ax620_virtual_npu: AX620_VIRTUAL_NPU_MODE_111 # 编译后模型使用 ax620 虚拟 NPU 1+1 模式的 1 号虚拟核
    batch_size_option: BSO_DYNAMIC                # 编译后的模型支持动态 batch
    batch_size: 1
    batch_size: 2
    batch_size: 4                                 # 最大 batch_size 为 4；要求 batch_size 为 1 2 或 4 时推理保持较高性能
    disable_param_compress: true
    hyper_param: "run_cf.ocm_rb_layers=all"       # 直接传递给 pulsar_compiler 的超参数
    override_unpin_params {
        unpin_mode: PIN_PARAMS                    # 明确告诉 Compiler 打开 unpin_params
    }
    lossy_slicing: true                           # PD
    debug: true                                   # 开启 debug 模式
}
```

### 结构体字段说明
#### virtual_npu
* 参数路径：
  * pulsar_conf.virtual_npu
* 参数作用：
  * 指定目标模型所使用的 AX630A 虚拟 NPU 核
* 参数类型：枚举类型。可选项：
  * `VIRTUAL_NPU_MODE_AUTO`：默认值
  * `VIRTUAL_NPU_MODE_0`：不使用虚拟 NPU 模式
  * `VIRTUAL_NPU_MODE_311`
  * `VIRTUAL_NPU_MODE_312`
  * `VIRTUAL_NPU_MODE_221`
  * `VIRTUAL_NPU_MODE_222`
* 注意：
  * 此配置项需要在 `SuperPulsarConfiguration.target_hardware` 被指定为 `TARGET_HARDWARE_AX630` 的前提下使用
  * 此配置项跟 `ax620_virtual_npu` 二选一使用

#### ax620_virtual_npu
* 参数路径：
  * pulsar_conf.ax620_virtual_npu
* 参数作用：
  * 指定目标模型所使用的 AX620A 虚拟 NPU 核
* 参数类型：枚举类型。可选项：
  * `AX620_VIRTUAL_NPU_MODE_AUTO`：默认值
  * `AX620_VIRTUAL_NPU_MODE_0`：不使用虚拟 NPU 模式
  * `AX620_VIRTUAL_NPU_MODE_111`
  * `AX620_VIRTUAL_NPU_MODE_112`
* 注意：
  * 此配置项需要在 `SuperPulsarConfiguration.target_hardware` 被指定为 `TARGET_HARDWARE_AX620` 的前提下使用
  * 此配置项跟 `virtual_npu` 二选一使用

#### batch_size_option
* 参数路径：
  * pulsar_conf.batch_size_option
* 参数作用：
  * 设置 joint 格式模型所支持的 batch 类型
* 参数类型：枚举类型。可选项：
  * `BSO_AUTO`：默认选项，默认为静态 batch
  * `BSO_STATIC`：静态 batch，推理时固定 batch_size，性能最优
  * `BSO_DYNAMIC`：动态 batch，推理时支持不超过最大值的任意 batch_size，使用最灵活

#### batch_size
* 参数路径：
  * pulsar_conf.batch_size
* 参数作用：
  * 设置 joint 格式模型所支持的 batch size。默认为 1
* 参数类型：整数数组
* 参数说明：
  * 当指定了 [batch_size_option](#batch_size_option) 为 `BSO_STATIC` 时，batch_size 表示 joint 格式模型推理时能用的唯一 batch size
  * 当指定了 [batch_size_option](#batch_size_option) 为 `BSO_DYNAMIC` 时，batch_size 表示 joint 格式模型推理时所能使用的最大 batch size
  * 当生成支持动态 batch 的 joint 格式模型时，可配置多个值，以提高使用不超过这些值的 batch size 进行推理时的性能
    * 注意：指定多个 batch size 会增加 joint 格式模型文件的大小
    * 当配置多个 batch_size 时，[batch_size_option](#batch_size_option) 将默认为 `BSO_DYNAMIC`

#### batch_layout_option
* 参数路径：
  * pulsar_conf.batch_layout_option
* 参数作用：
  * 多 batch 模型的单个输入张量的存储块个数
* 参数类型：枚举类型。可选项：
  * `GATHER`：代表不同 Batch 输入数据用不同内存来存储
  * `BLOCK`：意味着不同 Batch 使用一块连续内存存储
  * `AUTO`：表示使用默认值（同时支持两种内存排布）

#### no_runtime_var
* *deprecated*
* 参数路径：
  * pulsar_conf.no_runtime_var
* 参数作用：
  * 编译 neu 时不生成运行时变量
* 参数类型：布尔类型
  * `true`: 编译 neu 时不生成运行时变量
  * `false`: 生成运行时变量

#### hyper_param
* 参数路径：
  * pulsar_conf.hyper_param
* 参数作用：
  * 直传 pulsar compiler 的超参
* 参数类型：一个字符串

#### lossy_slicing
* 参数路径：
  * pulsar_conf.lossy_slicing
* 参数作用：
  * 部分 QAT ISP 模型适用
* 参数类型：布尔类型
  * `true`: 启用此特性
  * `false`: 禁用此特性

#### input_height
* 参数路径：
  * pulsar_conf.input_height
* 参数作用：
  * 修改模型输入分辨率高度
* 参数类型：一个整数

#### input_width
* 参数路径：
  * pulsar_conf.input_width
* 参数作用：
  * 修改模型输入分辨率宽度
* 参数类型：一个整数

#### override_unpin_params
* 参数路径：
  * pulsar_conf.override_unpin_params
* 参数作用：
  * 不设置时交由 Compiler 决定是否打开 unpin_params，设置时 override 决定
* 参数类型：结构体。字段说明：
  * `unpin_mode`: 枚举类型。可选项：
    * `UNPIN_PARAMS`: 明确告诉 Compiler 打开 unpin_params
    * `PIN_PARAMS`: 明确告诉 Compiler 不打开 unpin_params

#### disable_param_compress
* 参数路径：
  * pulsar_conf.disable_param_compress
* 参数作用：
  * 默认值 false 设置 Compiler `--param_compress`, true 时不会设置
* 参数类型：布尔类型
  * `true`: 不给 pulsar compiler 传递命令行参数 `--param_compress`
  * `false`: 给 pulsar compiler 传递命令行参数 `--param_compress`

#### override_wait_mode
* 参数路径：
  * pulsar_conf.override_wait_mode
* 参数作用：
  * 不设置时交由 Compiler 决定是否打开 wait_mode，设置时 override 决定
* 参数类型：结构体。字段说明：
  * `wait_mode`: 布尔类型
    * `true`: 明确告诉 pulsar_compiler 打开 wait_mode
    * `false`: 明确告诉 puslar_compiler **不打开** wait_mode

#### itp_hsk_mode
* 参数路径：
  * pulsar_conf.itp_hsk_mode
* 参数作用：
  * 控制 itp 的硬件握手模式
* 参数类型：枚举类型。可选项：
  * `ITHM_DISABLE`: 默认模式
  * `ITHM_DDR_SYNC_SINGLE_PARTITION`: 单 partition (适用 ax170a)
  * `ITHM_DDR_SYNC_MULTIPLE_PARTITION`: 多 partition (适用 ax620a)
  * `ITHM_OCM_SYNC_RDONLY`: itp 读 ocm 写 ddr (适用 ax620a)
  * `ITHM_OCM_SYNC_RDWR`: itp 读 ocm, 写 ocm

#### ife_hsk_mode
* 参数路径：
  * pulsar_conf.ife_hsk_mode
* 参数作用：
  * 控制 ife 的硬件握手模式
* 参数类型：枚举类型。可选项：
  * `IFHM_IFE_DISABLE`:
  * `IFHM_IFE_ONE_EU`:


#### debug
* 参数路径：
  * pulsar_conf.debug
* 参数作用：
  * pulsar_compiler 调试模式开关
* 参数类型：布尔类型
  * `true`: 打开调试模式
  * `false`: 关闭调试模式

#### output_dir
* 参数路径：
  * pulsar_conf.output_dir
* 参数作用：
  * 编译结果存储路径
* 参数类型：一个字符串
