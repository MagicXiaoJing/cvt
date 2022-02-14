# 查看文档
推荐以网页形式查看，也可以直接查阅 .md 文件

## 方法一：本地查看
* 终端执行命令 `python3 -m http.server 8080`
* 浏览器访问 `http://localhost:8080`

## 方法二：借助 docker 查看
* 启动 docker 命令 `docker run --rm --entrypoint="" -ti -p 8080:8080 -v /path/to/doc:/data axera/neuwizard:0.5.30.1 /opt/venv/bin/python3  -m http.server 8080`
  * 请将 `/path/to/doc` 修改成文档所在路径
  * 请用实际的 docker 镜像版本号替换命令中的 `0.5.30.1`
* 浏览器访问 `http://localhost:8080`
