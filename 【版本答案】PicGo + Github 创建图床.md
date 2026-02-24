# 【版本答案】PicGo + Github 创建图床

我们写博客时经常面临一个问题，当我们要插入较多图片时，放到本地要一个个填入图片的本地地址，连同图片一起上传到博客仓库还会使博客页面加载过慢，上传到公共图床既麻烦，又面临图床爆炸图片过期的问题。

使用PicGo + Github创建自己的图床或许成了最优雅的解决方案，它既不用担心图片过期，使用时还十分方便，可以联动Typora在插入图片时自动上传到图床，地址自动填入图床的图片地址，在上传到CSDN等博客软件时也不用因为是本地图片而再 一张张地上传图片。

下面我们来看一下具体的安装配置过程。

### 第一步：创建 GitHub 图床仓库

1. 登录 GitHub，创建一个新仓库（例如命名为 `Image-Bed`）。
2. 仓库必须设置为 **Public**（公开），否则外部无法访问图片链接。
3. 初始化时建议勾选 **Add a README.md**，确保仓库不是空的。

![image-20260224215237632](https://cdn.jsdelivr.net/gh/Duziks/Image-Bed/img/image-20260224215237632.png)

### 第二步：生成 GitHub 个人访问令牌 (Token)

这是让 PicGo 有权限向你的仓库上传文件的关键：

1. 点击 GitHub 右上角头像 -> **Settings** -> **Developer settings**。
2. 选择 **Personal access tokens** -> **Tokens (classic)**。
3. 点击 **Generate new token (classic)**。
4. **Note** 填写用途（如 `PicGo-Token`），**Expiration** 选择永久（No expiration）或较长时间。
5. **重要：** 在 `Select scopes` 中勾选 **`repo`** 权限（这样 PicGo 才能读写你的代码库）。
6. 点击页面底部的 **Generate token**，**立即复制生成的字符串**。

### 第三步：配置 PicGo 客户端

1. 下载并安装 [PicGo](https://github.com/Molunerfinn/PicGo)，点击仓库右侧的 `Releases` 下载最新版。
2. 在左侧菜单选择 **图床设置** -> **GitHub图床**。
3. 按以下信息填写配置：
   - **仓库名**：`用户名/仓库名`（例如 `UserName/Image-Bed`）。
   - **分支名**：`main`（旧仓库可能是 `master`）。
   - **设定Token**：粘贴你刚才复制的 GitHub Token。
   - **指定存储路径**：建议填写 `img/`，这样图片会存在仓库的 img 文件夹里，不会乱。
   - **设定自定义域名**：由于 GitHub 原始链接在国内访问较慢，建议填写加速地址： `https://cdn.jsdelivr.net/gh/你的用户名/仓库名`。

![image-20260224215032574](https://cdn.jsdelivr.net/gh/Duziks/Image-Bed/img/image-20260224215032574.png)

1. 点击 **确定** 并设置为 **默认图床**。

### 第四步：关联 Typora (实现自动上传)

1. 打开 Typora，进入 **文件** -> **偏好设置** -> **图像**。
2. **插入图片时**：选择 **上传图片**。
3. **上传服务**：选择 **PicGo (app)**。
4. **验证图片上传选项**：点击测试，如果显示“成功”，以后你直接粘贴图片进 Typora，它就会自动变成 GitHub 的网络链接。

### 其他

要使用PicGo的手动上传图片功能，可以点击左侧的上传区，拖入图片待上传完成后即可自动复制图片的URL地址（需手动选择URL格式）

![image-20260224214541214](https://cdn.jsdelivr.net/gh/Duziks/Image-Bed/img/image-20260224214541214.png)

上传的图片可以在左侧的相册栏查看

![image-20260224214734998](https://cdn.jsdelivr.net/gh/Duziks/Image-Bed/img/image-20260224214734998.png)

同样选择URL后，点击每张图片左下角的按钮即可复制地址

这里推荐一个寻找当做博客封面或者头图的图片的免费网站：

https://wallhaven.cc/