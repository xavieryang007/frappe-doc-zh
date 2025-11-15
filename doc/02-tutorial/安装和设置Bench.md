# 安装和设置Bench (Install and Setup Bench)

Bench是用于管理Frappe应用和站点的命令行工具。

## 1. 安装Bench

- 如果您尚未安装Bench，请先参考安装指南完成安装。
- 安装完成后，可以通过以下命令验证安装是否成功：
  ```bash
  bench --version
  ```
  如果安装成功，会显示Bench的版本号（例如：`5.1.0`）。

## 2. 初始化frappe-bench目录

- 创建一个项目目录（例如`frappe-bench`），用于存放应用和站点：
  ```bash
  bench init frappe-bench
  ```
  该命令会执行以下操作：
  - 在`env`目录下创建Python虚拟环境。
  - 拉取并安装Frappe应用（作为Python包）。
  - 安装Frappe的Node.js依赖。
  - 构建静态资源（JS/CSS）。

## 3. 目录结构说明

初始化后的`frappe-bench`目录结构如下：
```
frappe-bench/
├── apps/           # 存放Frappe应用（如frappe核心应用）
├── sites/          # 站点配置和资源
│   ├── apps.txt    # 已安装应用列表
│   ├── assets/     # 静态资源（生产环境由Nginx托管）
│   └── common_site_config.json  # 全局站点配置
├── env/            # Python虚拟环境
├── config/         # Redis和Nginx配置文件
├── logs/           # 进程日志文件
└── Procfile        # 开发环境进程列表
```

## 4. 启动Bench服务器

- 进入项目目录并启动服务：
  ```bash
  cd frappe-bench
  bench start
  ```
- 启动后会看到多个进程运行，包括：
  - **Web服务器**（端口8000）
  - **Redis服务**（缓存、任务队列、SocketIO）
  - **后台工作进程**（处理异步任务）
  - **资源监听服务**（自动编译前端资源）

## 注意事项

- 不要关闭运行`bench start`的终端窗口。
- 如需执行其他Bench命令，请另开终端并进入`frappe-bench`目录。
- 当前尚未创建任何站点，下一步是创建应用和站点。

## 下一步

- 继续教程：创建应用

如果需要更详细的安装说明或遇到问题，请参考官方文档的安装指南。