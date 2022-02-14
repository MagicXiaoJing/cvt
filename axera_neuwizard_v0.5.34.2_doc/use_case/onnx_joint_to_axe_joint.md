# 将 Joint 模型中的 ONNX 子图转为 AXEngine 子图

## Quick Start

如下一条指令即可将名为 `input.joint` 的 Joint 模型（以 ONNX 作为 CPU 后端实现）转为 Joint 模型（以 AXEngine 作为 CPU 后端实现），并且开启优化模式。

```bash
python3 /root/python_modules/super_pulsar/super_pulsar/tools/joint_onnx_to_axe.py --input input.joint --output output.joint --optimize_slim_model
```

## 参数详解

### `--input`
转换工具的输入 Joint 模型路径。

### `--output`
转换工具的输出 Joint 模型路径。

### `--optimize_slim_model`
开启优化模式。当网络输出特征图较小时建议开启，否则不建议。