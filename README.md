# 无头V：华为手机部署包

用于支持 Android APK 的华为鸿蒙 4.2 手机，通过 Termux 在手机本机运行。要求 ARM64、Android 兼容环境 API 28 或更高。**尚未进行真机测试。**

这里提供 Android 编译包、安装脚本和说明，不上传原项目的 Go 源码。它不是可直接点击安装的 APK，也不是鸿蒙 NEXT 原生应用。

## 下载

当前版本：`huawei-arm64-20261014`。

- [下载 wutouv-huawei-arm64.zip](https://github.com/guhfujb/wutouv-huawei-downloads/releases/download/huawei-arm64-20261014/wutouv-huawei-arm64.zip)
- [版本页面与 SHA-256 校验文件](https://github.com/guhfujb/wutouv-huawei-downloads/releases/tag/huawei-arm64-20261014)

将 ZIP 放进手机的 **Download／下载** 文件夹，保留原文件名。

也可以直接在 Termux 下载，无需传文件或申请共享存储权限：

```sh
pkg update -y
pkg install -y curl unzip ca-certificates openssl-tool
mkdir -p "$HOME/wutouv-package"
cd "$HOME/wutouv-package"
curl -fL --retry 3 -o wutouv-huawei-arm64.zip 'https://github.com/guhfujb/wutouv-huawei-downloads/releases/download/huawei-arm64-20261014/wutouv-huawei-arm64.zip'
curl -fL --retry 3 -o wutouv-huawei-arm64.zip.sha256 'https://github.com/guhfujb/wutouv-huawei-downloads/releases/download/huawei-arm64-20261014/wutouv-huawei-arm64.zip.sha256'
sha256sum -c wutouv-huawei-arm64.zip.sha256 && unzip wutouv-huawei-arm64.zip && sh wutouv/install.sh
```

安装成功后输入 `wv-start`。下面的手动传文件步骤可跳过。

## 第一次安装

已有 Termux 时先保留。旧版低于 `0.118.0`，请先备份数据，再从原来源更新；不要直接卸载，也不要混用 F-Droid、GitHub 的不同签名版本或插件。[Termux 官方安装说明](https://github.com/termux/termux-app#installation)

打开 Termux，输入以下命令，**每行执行一次**。运行第一行后，按手机提示决定是否授予下载文件访问权限。

```sh
termux-setup-storage
pkg update -y
pkg install -y unzip ca-certificates openssl-tool
mkdir -p "$HOME/wutouv-package"
unzip "$HOME/storage/downloads/wutouv-huawei-arm64.zip" -d "$HOME/wutouv-package"
sh "$HOME/wutouv-package/wutouv/install.sh"
```

程序安装到 Termux 私有目录 `$HOME/wutouv`。不要从 `/storage`、`/sdcard` 或下载目录直接运行二进制。如果解压时提示覆盖文件，先确认是否已经安装过，不要随意全部覆盖。

## 启动与连接

在 Termux 输入：

```sh
wv-start
```

首次启动会显示项目规则。请自己阅读；**只有同意时才输入 `yes` 并回车**。安装脚本不会替你同意，也不会设置开机自启。运行期间保留 Termux 会话。

在另一个 Termux 会话查看本机生成的密码和 API Key：

```sh
wv-password
```

- 手机浏览器打开 `http://127.0.0.1:2156/admin/`，用**管理员密码**登录，不需要用户名。
- 同一手机的聊天软件选择 **OpenAI 兼容接口**，API 地址填 `http://127.0.0.1:2156/v1`，密钥填完整的本地 `sk-` API Key。
- 管理员密码用于登录后台；API Key 用于连接聊天软件。不要把它们发到公开页面。
- 本地检查地址：`http://127.0.0.1:2156/health`。出现 `healthy` 仅证明本地程序已启动，不保证上游模型可用。

## 停止与排错

在运行会话按 **CTRL，再按 C**；或者在新会话输入 `wv-stop`。等待正常退出，最长约 25 秒。

锁屏后服务退出时，检查华为的 Termux 后台活动设置，并保留 Termux 会话。唤醒锁不能保证系统永不杀后台。

报错时记录执行的命令和最后几行错误，先遮住密码与 API Key。更详细的步骤见 ZIP 内的 `README-手机启动.txt`。
