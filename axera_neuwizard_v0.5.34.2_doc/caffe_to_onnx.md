Caffe2ONNX
=========
# Quick Start
```bash
dump_onnx.sh xxx.prototxt xxx.caffemodel xxx.onnx
```

# 批量转化
```bash
batch_onnx.sh config_file
```
## config_file 格式
文件每一行为代表需要转化的一个模型，第一列为prototxt,第二列为caffemodel，第三列为输出的onnx文件名，中间以一个空格分隔
```
a.prototxt a.caffemodel a.onnx
11.prototxt 11.caffemodel 11.onnx
2.prototxt 3.caffemodel 3.onnx
```
