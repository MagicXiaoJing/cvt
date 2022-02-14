# 将 neu 模型转换成 joint 模型
使用如下 [pulsar build](/super_pulsar/pulsar_build.md) 命令

```bash
pulsar build --input neu_model.neu --output joint_model.joint
```

* 说明：
  * [`--input`](/super_pulsar/pulsar_build.md#-input) 用于指定输入的 neu 模型文件
  * [`--output`](/super_pulsar/pulsar_build.md#-output) 用于指定输出的 joint 模型文件名称
