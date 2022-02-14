- Super Pulsar
  - 芯片介绍
    - [AX630A](/hardware/AX630A.md)
    - [AX620A](/hardware/AX620A.md)
<!--SLOT_HW-->

  - [Super Pulsar 介绍](/super_pulsar/super_pulsar.md)

- 使用说明
  - [编译 PTQ 模型](/super_pulsar/quick_start_build_onnx.md)
  - [编译 QAT 模型](/super_pulsar/quick_start_build_magma.md)
  - [上板和仿真运行](/super_pulsar/quick_start_run_on_board_and_simulation.md)
<!--SLOT_QS-->

- 进阶功能
  - [生成一份 mcode 多份 wbt 的 Joint 模型](/use_case/mcode_wbt.md)
  - [将 neu 模型转换成 joint 模型](/use_case/neu_to_joint.md)
  - 配套工具
    - [Caffe2ONNX](/caffe_to_onnx.md)
    - [Joint 模型初始化速度补丁工具](/use_case/optimize_joint_init_time.md)
    - [将 Joint 模型中的 ONNX 子图转为 AXEngine 子图](/use_case/onnx_joint_to_axe_joint.md)
<!--SLOT_ADV-->

- 命令汇总
  - [pulsar build](/super_pulsar/pulsar_build.md)
  - [pulsar run](/super_pulsar/pulsar_run.md)
  - [pulsar view](/super_pulsar/pulsar_view.md)
  - [pulsar debug](/super_pulsar/pulsar_debug.md)
  - [pulsar info](/super_pulsar/pulsar_info.md)
  - [pulsar version](/super_pulsar/pulsar_version.md)

- 参数配置
  - [配置文件基础说明](/super_pulsar/config.md)
  - [无 tasks 版本配置文件](/super_pulsar/config/config_without_tasks.md)
  - [有 tasks 版本配置文件](/super_pulsar/config/config_with_tasks.md)
  - [neuwizard_conf 配置](/super_pulsar/config/config_neuwizard_conf.md)
  - [pulsar_conf 配置](/super_pulsar/config/config_pulsar_conf.md)
  - [输入输出盖帽](/super_pulsar/config/neuwizard_conf/operator_conf.md)
  - [多 tasks 级联](/super_pulsar/config/config_multi_tasks.md)

<!--SLOT_DEV-->
<!--SLOT_DEV2-->

- [FAQ](/faq.md)
