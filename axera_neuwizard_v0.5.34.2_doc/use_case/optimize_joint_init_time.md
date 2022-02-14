## 概述
对于 `neuwizard-0.5.29.9` 及更早版本工具链转换的 Joint 模型文件，可以使用 `optimize_joint_init_time.py` 工具离线刷新，以减少 Joint 模型加载时间，推理结果和时间不变。

## 使用方法
```bash
cd /root/python_modules/super_pulsar/super_pulsar/tools
python3 optimize_joint_init_time.py --input old.joint --output new.joint
```
