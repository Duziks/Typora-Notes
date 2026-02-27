# hexo博客部署到linux云服务器教程

大多数人都是把hexo博客部署到Github Pages上，但是由于国内网络访问Github较慢，而我恰好有一台已经部署过一个Online Judge的云服务器，于是突发奇想把我的hexo博客也部署到云服务器上试试。所以本文适用于和我一样已经把hexo博客部署到Github Pages上面了，想双线部署到云服务器的朋友们。

### 第一阶段：配置云服务器（接收端）

我们需要在服务器上建立一个“代码接收仓库”和一个“网站根目录”。

#### **1. 创建目录并初始化 Git 裸仓库**

```Bash
# 创建网站静态文件存放目录
sudo mkdir -p /var/www/hexo
# 创建 Git 裸仓库
sudo mkdir -p /var/repo && cd /var/repo
sudo git init --bare hexo.git
```

#### **2. 配置 Git Hook（实现自动部署）**

 当本地推送到 `hexo.git` 时，它会自动把文件同步到 `/var/www/hexo`。

```Bash
sudo vi /var/repo/hexo.git/hooks/post-receive
```

写入以下内容（注意路径对应）：

```bash
#!/bin/bash
# 强制将代码检出到网站根目录
git --work-tree=/var/www/hexo --git-dir=/var/repo/hexo.git checkout -f
```

保存并赋予执行权限：

```bash
sudo chmod +x /var/repo/hexo.git/hooks/post-receive
```

#### **3. 安装并配置Nginx** 

##### **在云服务器安装 Nginx**

```bash
sudo apt update
sudo apt install nginx -y
```

##### **配置 Hexo 博客**

1. 创建配置文件：

   ```bash
   sudo vi /etc/nginx/conf.d/blog.conf
   ```

2. 写入配置：

   按 `i` 进入编辑模式，粘贴以下内容：

   ```Nginx
   server {
       listen 8080; # 由于80端口已被oj占用，所以我们选择8080接口，如果没被占用，直接填80即可
       server_name _; # 暂时用 IP 访问，所以填下划线，如果你有自己的域名，可以填入，比如blog.YourName.com
   
       root /var/www/hexo; # 确保这和你之前 mkdir 的路径一致
       index index.html;
   
       location / {
           try_files $uri $uri/ =404;
       }
   
       # 开启 Gzip 优化
       gzip on;
       gzip_types text/plain text/css application/javascript;
   }
   ```

   按 `Esc`，输入 `:wq` 保存退出。

3. 检查并重载：

   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
   ```

4. 查看 Nginx 是否在运行

   在服务器终端执行

   ```Bash
   # 查看 Nginx 状态
   systemctl status nginx
   ```

   如果显示 `active (running)`，说明服务没问题。

   否则可能是因为新安装的 Nginx 试图去抢占 **80 端口**，但 80 端口已经被占用了。虽然写了 `blog.conf` 监听 8080，但 Nginx 安装后的**默认配置文件**（default site）依然在尝试监听 80。

   解决方法：删掉 Nginx 的默认“抢占”配置

   在终端执行：

   ```bash
   sudo rm /etc/nginx/sites-enabled/default
   ```

   再次测试 Nginx 配置：

   ```bash
   sudo nginx -t
   ```

   如果输出 `syntax is ok` 且 `test is successful`，说明冲突解除了。

   别忘了再次启动 Nginx：

   ```
   sudo systemctl restart nginx
   ```

##### **解决权限隐患**

Git Hook 是以 `root` 身份运行的，它检出的文件所有者是 `root`，但是刚安装的 Nginx 通常是以 `www-data` 用户运行的。

为了防止访问时出现 **403 Forbidden**，需要执行：

```bash
# 确保 Nginx 有权限读取这个目录
sudo chmod -R 755 /var/www/hexo
```

### 第二阶段：开放8080端口

1. 登录云服务器控制台。
2. 找到你的轻量应用服务器（或者 CVM 实例）。
3. 点击 **“防火墙”** 或 **“安全组”** 选项卡。
4. 点击 **“添加规则”**：
   - **协议：** TCP
   - **端口：** 8080
   - **策略：** 允许（或者 0.0.0.0/0）
5. 保存。

如果云平台安全组开了还不通，检查一下系统里的 `ufw`：

```Bash
sudo ufw allow 8080
```

### 第三阶段：修改本地 `_config.yml`

找到本地博客根目录下的 `_config.yml`，修改deploy部分如下：

```YAML
deploy:
  type: git
  repo:
    # 保留原来的github pages
    github: git@github.com:YourName/YourName.github.io.git,main
    # 新增你的云服务器 (将“你的服务器IP”替换为实际 IP)
    server: root@你的服务器IP:/var/repo/hexo.git,master
```

### 测试

现在，你可以执行hexo三件套`hexo clear && hexo g && hexo deploy`来测试一下是否成功了

执行后，访问 `http://云服务器IP:8080` 就可以看到你的博客了

### 免密部署

在执行`hexo deploy`时会要求你输入云服务器的密码，比较麻烦，我们可以SSH Key（公钥验证）来实现免密部署

如果你在部署到github pages时已经在本地电脑上生成了秘钥，可以直接打开以下路经找到秘钥文件：`C:\Users\UserName\.ssh`

请在你的 **PowerShell** 中执行以下命令将公钥发送给云服务器：

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh root@111.228.45.41 "mkdir -p .ssh && cat >> .ssh/authorized_keys"
```

其中`id_ed25519.pub`修改为你在`C:\Users\UserName\.ssh`目录下找到的`.pub`文件名，`111.228.45.41`修改为你自己的云服务器地址

执行完上面的命令后，直接输入：

```PowerShell
ssh root@111.228.45.41
```

如果直接进入了服务器而没有询问密码，那么你的“免密通行证”就已经正式生效了。

如果你没有在本地生成过秘钥，在PowerShell执行：

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

一路回车即可

生成完毕后再执行上述操作将公钥发送给云服务器

最后回到你的 Hexo 博客根目录，执行最后的部署命令：

```bash
hexo clean && hexo g && hexo d
```

发现不再需要输入云服务器密码就完成了
