## 上板和仿真运行
编译 [onnx](/super_pulsar/quick_start_build_onnx.md) 或 [magma](/super_pulsar/quick_start_build_magma.md) 模型后得到的 joint 模型，可以在含有 AX 系列 NPU 的嵌入式系统环境中进行推理，也可以使用 Super Pulsar 工具自带的[仿真命令](/super_pulsar/pulsar_run.md)进行模拟运行

### 仿真
仿真运行可以快速验证模型是否有效。同时可以为上板准备测试数据

在 docker 中执行如下命令。[了解更多](/super_pulsar/pulsar_run.md)

```bash
mkdir gt

pulsar run LeNet5.joint --input imagenet-1k-images/img-0.jpg --output_gt gt

# 得到上板运行时的输入数据文件 gt/input/input.bin 和用于对分的文件 gt/output/output.bin
```

### 上板运行
#### 对分
使用仿真过程中生成的输入数据文件上板推理，将上板推理结果与仿真器提供的对分文件进行比对

在板上执行如下命令
```bash
run_joint LeNet5.joint --data gt/input/input.bin --bin-out-dir ./

# 得到 inference 结果文件 output.bin，此文件可与 gt/output/output.bin 进行对分
```

#### 测试模型的推理性能
重复推理多次，用于更准确地计算模型单次推理所需要的时间，从而评估模型的真实推理性能

在板上执行如下命令
```bash
run_joint LeNet5.joint --repeat 100

# 注意查看 log 中的时间相关信息
```
