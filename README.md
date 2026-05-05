# Weibo Local Archiver

本项目是一个本地执行的个人微博归档工具。它的目标是把用户自己的微博文字、图片和时间线保存为可长期打开的离线目录，不上传云端，不保存账号密码。

## 目标使用方式

面向普通用户的目标形态是“双击本地 App”：

```text
下载压缩包 -> 解压 -> 双击 App -> 登录微博 -> 点击开始备份 -> 打开本地归档
```

普通用户不需要安装 Node.js，不需要打开终端，也不需要输入命令。当前仓库里的命令行工具是底层能力和开发验证工具，后续会被桌面 App 调用。

## 当前阶段

第一版已经实现 macOS 桌面 App 外壳、微博网页登录采集和本地归档目录的基础闭环：

- `init`：创建标准归档目录。
- `verify`：校验 `data/posts.json` 和 `media/` 图片是否匹配。
- `render`：生成可离线打开的 `html/archive.html`。
- macOS App：支持选择文件夹、打开微博登录、检测登录、自动采集自己的微博、下载图片、生成网页、打开网页和打开文件夹。

当前采集方式是：用户在 App 打开的微博窗口中自己登录，App 使用登录后的微博网页接口读取自己的时间线；工具不保存微博密码。

后续还需要补暂停/继续、失败图片重试、正式签名公证和 Windows 打包。

## 开发者安装和构建

```bash
npm install
npm run build
```

## macOS 桌面 App 开发和打包

开发预览：

```bash
npm run app:dev
```

生成可双击打开的 macOS `.app`：

```bash
npm run app:pack:mac
```

App 使用流程：

```text
选择归档文件夹 -> 准备工作区 -> 打开微博登录 -> 检测登录 -> 开始自动采集 -> 打开网页
```

## 开发者 CLI 使用

```bash
npm run weibo-archive -- init --out ./scratch/my-weibo
npm run weibo-archive -- verify ./scratch/my-weibo
npm run weibo-archive -- render ./scratch/my-weibo
```

如果已经有一个类似 `weibo-archive` 的旧目录，并且其中包含 `data/posts.json` 和 `media/`，可以直接校验和生成页面：

```bash
npm run weibo-archive -- verify /path/to/weibo-archive
npm run weibo-archive -- render /path/to/weibo-archive
```

## 操作、方案和验证文档

- [普通用户操作手册](docs/OPERATION_MANUAL.md)：按“双击本地 App”的方式描述普通用户流程。
- [双击本地 App 方案](docs/APP_PLAN.md)：说明如何把当前底层工具升级为图形界面 App。
- [开发者命令行手册](docs/DEVELOPER_MANUAL.md)：保留当前 CLI 的安装、构建、校验和生成步骤。
- [验证说明](docs/VALIDATION.md)：说明如何确认工具满足“本地保存、完整校验、离线查询”的需求。

## 目录结构

```text
archive-root/
  archive.config.json
  data/
    posts.json
  media/
  html/
    archive.html
  logs/
  tmp/
```

## 数据原则

- 默认只处理本地目录。
- 不保存微博密码。
- 不上传微博内容和图片。
- 归档输出使用普通 JSON 和静态 HTML，脱离本工具也能读取。
