# Sublime Text C/C++ 环境配置

## 官网下载 Sublime Text

https://www.sublimetext.com/

## 下载MinGW

https://github.com/niXman/mingw-builds-binaries

点击右侧`Releases`

![image-20260130125822174](./Sublime%20Text%20C++%20%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE.assets/image-20260130125822174.png)

选择最新版本的文件下载  

## 配置环境变量

下载完成后解压  

打开bin文件夹并复制目录  

![image-20260130130155704](./../Downloads/Sublime%2520Text%2520C++%2520%25E7%258E%25AF%25E5%25A2%2583%25E9%2585%258D%25E7%25BD%25AE.assets/image-20260130130155704-1769755292858-3.png)

在任务栏中搜索并打开`编辑系统环境变量`

![image-20260130130649173](./../Downloads/Sublime%2520Text%2520C++%2520%25E7%258E%25AF%25E5%25A2%2583%25E9%2585%258D%25E7%25BD%25AE.assets/image-20260130130649173-1769755300310-5.png)

点击环境变量

![image-20260130130729776](./../Downloads/Sublime%2520Text%2520C++%2520%25E7%258E%25AF%25E5%25A2%2583%25E9%2585%258D%25E7%25BD%25AE.assets/image-20260130130729776.png)

双击打开系统变量中的Path

![image-20260130130811456](./../Downloads/Sublime%2520Text%2520C++%2520%25E7%258E%25AF%25E5%25A2%2583%25E9%2585%258D%25E7%25BD%25AE.assets/image-20260130130811456-1769755301860-7.png)

点击右侧新建，将复制的地址加入，然后点击确定

## 配置Sublime Text

### 设置中文

打开Sublime Text  

按下快捷键 `Ctrl + Shift + P` 打开命令面板

在搜索框中输入 `install`

选择 `Install Package Control` 进行安装  

安装成功后再次使用快捷键 `Ctrl + Shift + P` 打开命令面板  

在搜索框中输入 `install`  

选择 `Package Control: Install Package`  

库加载完成后输入 `Chinese` ，选择`ChineseLocalizations`   

安装完成后Sublime界面会自动切换成中文  

### 编译运行C/C++程序

在安装配置好MinGW的前提下，Sublime会自动识别后缀为 `.c` 或 `.cpp` 的文件，按下 `Ctrl + B` 即会出现以下选项  

![image-20260130132015778](./../Downloads/Sublime%2520Text%2520C++%2520%25E7%258E%25AF%25E5%25A2%2583%25E9%2585%258D%25E7%25BD%25AE.assets/image-20260130132015778-1769755304859-9.png)

选择第二个即可编译并运行代码

![image-20260130132609322](./../Downloads/Sublime%2520Text%2520C++%2520%25E7%258E%25AF%25E5%25A2%2583%25E9%2585%258D%25E7%25BD%25AE.assets/image-20260130132609322-1769755306461-11.png)

在下方控制台面板中输入即可

如果想在弹出的cmd窗口中进行输入输出，可以点击顶部菜单栏的 **Tools (工具)** > **Build System (编译系统)** > **New Build System... (新建编译系统)**

将文件中的默认内容全部删除，复制并粘贴以下代码：

```json
{
    "shell_cmd": "g++ -std=c++20 \"${file}\" -o \"${file_path}/${file_base_name}\" && start cmd /c \"\"${file_path}/${file_base_name}\" & pause\"",
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "working_dir": "${file_path}",
    "selector": "source.c, source.c++",
    "encoding": "cp936"
}
```

按 `Ctrl + S` 保存。

文件名建议命名为 **`C++ Run in CMD.sublime-build`**（或者任何你能识别的名字）。

**注意：** 不要更改保存路径，默认应该保存在 `Sublime Text 3\Packages\User` 目录下。

然后点击菜单栏 **Tools** > **Build System**，在列表中选中你刚才保存的 **`C++ Run in CMD`**

现在再按下快捷键 `Ctrl + B` 就可以弹出一个黑色的 CMD 窗口运行你的代码，并等待你的输入。