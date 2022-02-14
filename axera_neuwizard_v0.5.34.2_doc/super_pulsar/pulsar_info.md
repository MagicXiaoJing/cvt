# pulsar info

## 概述
pulsar info 用于查看 `onnx`、`magma`、`joint`、`lava_joint` 等模型的信息。并可以将模型信息保存为 `html`、`grid`、`jira` 等格式，方便查看和检查

## 用法
* 命令
```
pulsar info model_file 
```
* `model_file` 是目标模型，支持 `onnx`、`magma`、`joint`、`lava_joint` 等格式

## 选项
## `--output`
指定模型信息保存目录。默认不保存

## `--tablefmt`
指定模型信息显示和保存格式，可选项：
* `grid`：默认值
* `html`
* `jira`
