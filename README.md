# 用 GitHub 服务器帮你下 Unity 6000.6.1f1（国内不用翻墙）

## 为什么这么干

Unity 的下载服务器按 IP 判定：中国 IP 一律 302 跳到国内镜像，
而国内镜像里**没有 6000.6.1f1**，所以永远返回 457 字节的错误页。
GitHub 的服务器在美国，不会被跳转。让它替你把 3.6GB 下好，
你再从 GitHub 拉回本地，全程不需要 VPN。

## 你要做的（网页操作，5 分钟）

### 1. 建仓库

打开 https://github.com/new
- Repository name：`unity-download`
- 选 **Private**
- 点 **Create repository**

### 2. 上传工作流文件

在新仓库页面点 **uploading an existing file**（或 Add file → Upload files），
把本文件夹里的 `.github` 整个文件夹拖进去（拖文件夹会自动带层级）。
下方点 **Commit changes**。

> 如果拖不进去，就用 Add file → Create new file，
> 文件名直接输入 `.github/workflows/download.yml`，
> 然后把本地的 `download.yml` 内容粘进去。

### 3. 启动下载

点仓库顶部 **Actions** → 左侧 **Download Unity 6000.6.1f1**
→ 右侧 **Run workflow** → 绿按钮 **Run workflow**

等 10~20 分钟（3.6GB 从 Unity CDN 下到 GitHub 服务器）。

### 4. 取货（关键：走 gh-proxy 加速）

跑到绿勾后，点仓库右侧 **Releases**，里面有
`unity-6000.6.1f1` 这个发布，包含 3 个分卷 + SHA256SUMS.txt。

国内直连 GitHub releases 可能慢，在下载链接前面加
`https://gh-proxy.com/` 前缀，例如：

```
https://gh-proxy.com/https://github.com/zhangsiyi0220/unity-download/releases/download/unity-6000.6.1f1/UnitySetup64-6000.6.1f1.exe.part01
```

part02、part03 同理。三个都下完。

### 5. 合并成一个 exe

把这 3 个文件放进同一个文件夹（比如 `D:\unity\download\`），
在该文件夹空白处按住 Shift + 右键 → **在此处打开 PowerShell 窗口**，执行：

```powershell
cmd /c copy /b UnitySetup64-6000.6.1f1.exe.part01+UnitySetup64-6000.6.1f1.exe.part02+UnitySetup64-6000.6.1f1.exe.part03 UnitySetup64-6000.6.1f1.exe
```

然后校验大小，应为 **3858324720** 字节：

```powershell
(Get-Item UnitySetup64-6000.6.1f1.exe).Length
```

数字对上就说明文件完好，双击安装。

## 装完之后

1. 安装路径用纯英文，比如 `D:\Unity\6000.6.1f1`
2. 打开 Unity Hub → Installs → **Add** → 选 `D:\Unity\6000.6.1f1\Editor\Unity.exe`

## 如果 Actions 跑失败了

看日志里 **Runner IP info** 那一段，确认服务器出口不是中国 IP。
再看 **Download Unity installer** 那一步的 `Downloaded bytes`：
小于 3000000000 就是又被重定向了，把那段日志发我。
