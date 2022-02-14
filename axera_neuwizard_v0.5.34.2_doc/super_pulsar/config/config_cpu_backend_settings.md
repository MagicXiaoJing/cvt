## CPUBackendSettings
* 参数路径：
  * [无 tasks 配置文件](/super_pulsar/config/config_without_tasks.md): cpu_backend_settings
  * [有 tasks 配置文件](/super_pulsar/config/config_with_tasks.md): tasks.cpu_backend_settings
* 参数类型：结构体
* 参数作用：控制编译后模型采用的 CPU 后端模式，目前有 ONNX 与 AXEngine 可选。注意，如果需要使带 AXEngine 后端的 Joint 模型可以在某一个旧版不支持 AXEngine 后端的 BSP 上运行时，需要同时开启 onnx_setting.mode 与 axe_setting.mode 为 ENABLED。
* 配置参数示例

```prototxt
cpu_backend_settings {
  # cpu 子图同时使用 onnx 与 axe 作为后端，由 joint sdk 选择后端
  onnx_setting {
    mode: ENBALED
  }
  axe_setting {
    mode: ENABLED
    axe_param {
      optimize_slim_model: true
    }
  }
}
```

### 结构体字段说明
#### onnx_setting
* 参数路径：
  * cpu_backend_settings.onnx_setting
* 参数作用：
  * ONNX 后端是否开启
* 参数类型：结构体。
  * mode: DEFAULT/ENABLED/DISABLED，默认为 DEFAULT（ONNX 的 DEFAULT 与 ENABLED 等价）
#### axe_setting
* 参数路径：
  * cpu_backend_settings.axe_setting
* 参数作用：
  * AXEngine 后端是否开启
* 参数类型：结构体。
  * mode: DEFAULT/ENABLED/DISABLED，默认为 DEFAULT（AXEngine 的 DEFAULT 与 DISABLED 等价）
  * axe_param
    * optimize_slim_model: 布尔类型，表示是否开启优化模式。当网络输出特征图较小时建议开启，否则不建议。
