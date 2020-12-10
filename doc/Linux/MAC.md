# 常用设置

```bash
# SSD 开启 TRIM 支持
$ sudo trimforce enable
# APP安装开启任何来源
$ sudo spctl --master-disable
# 修改主机名
$ sudo scutil --set HostName xxxxx
# 修改共享名称
$ sudo scutil --set ComputerName xxxxx
# 安装 xcode 命令行工具
$ xcode-select --install
```

# 常用软件

## Homebrew

- *Homebrew* 是一款自由及开放源代码的软件包管理系统，用以简化macOS系统上的软件安装过程，程序猿开发必备神器。

- 安装

  ```bash
  $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
  ```

- brew 加速

  使用国内源

  ```bash
  # 替换成国内源：
  # https://lug.ustc.edu.cn/wiki/mirrors/help/brew.git
  # 替换 Homebrew
  $ git -C "$(brew --repo)" remote set-url origin https://mirrors.ustc.edu.cn/brew.git
  
  # 替换 Homebrew Core
  $ git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
  
  # 替换 Homebrew Cask
  $ git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
  
  # 替换 Homebrew-bottles
  
  # 对于zsh用户
$ echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.zshrc
  $ source ~/.zshrc
  # 对于bash用户
  $ echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.bash_profile
  $ source ~/.bash_profile
  
  # 最后更新下
  $ brew update
  ```

## Java

- 下载安装JDK

  - 官网：[Oracle Technology Network > Java > Java SE > Downloads](https://link.jianshu.com/?t=http://www.oracle.com/technetwork/java/javase/downloads/index.html)

- 配置环境变量

  - 确认本机所使用的shell是哪个：zsh/bash
    在命令行中输入`echo $SHELL` 
    如果输出`/bin/bash`则为bash
    如果输出结果为 `/bin/zsh`则为zsh

  - 根据上面不同的结果修改不同的shell配置文件
    若为bash，则打开 `~/.bash_profile`，若为zsh打开`~/.zshrc`

  - 文件末尾添加以下内容，并保存

    ```shell
    $ export JAVA_HOME=$(/usr/libexec/java_home)
    ```

  - 命令行执行`source`命令，`source ~/.bash_profile` 或 `source ~/.zshrc`

## Python

macOS自带Python 2.7的版本，而且没有安装pip，得自己再折腾完善一下。

- pip2

  macOS里面Python自带easy_install，用来安装pip2非常方便

  ```bash
  # 安装pip2
  $ sudo easy_install pip
  # 查看pip的版本
  $ pip --version
  ```

- Python3 & pip3

  使用brew命令安装python3与pip3，一条龙服务，安装起来很方便

  ```bash
  # 安装Python3相关
  $ brew install python
  # 查看Python版本
  $ python3 --version
  # 查看pip的版本
  $ pip3 --version
  ```

## Oh My Zsh

macOS下默认的命令行shell环境为Bash，同时macOS也自带zsh shell环境，这个Oh My Zsh 是一个zsh美化增强插件，可以让自己的shell颜值更逼格。

- 安装

  ```bash
  # 更新下zsh
  $ brew install zsh
  # curl安装
  $ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
  ```

# [youtube-dl](https://github.com/ytdl-org/youtube-dl)

youtube-dl是一款开源、命令行界面的下载器，最初旨在从Youtube上下载视频，现在也支持其它网站。该项目也是GitHub星标数最高的项目之一

- 安装

  - MAC

    ```shell
    $ brew install youtube-dl
    ```

  - Other

    ```shell
    $ pip install --upgrade youtube-dl
    ```

- 安装FFMPEG可以实现格式转换功能

- [全局配置文件](https://github.com/ytdl-org/youtube-dl#configuration)

  - 配置文件路径

    `~/.config/youtube-dl/config`

  - 参考配置

    ```properties
    # 使用指定的HTTP/HTTPS/SOCKS代理,不需要的不配置
    --proxy 10.0.0.1:7890
    # 指定默认的保存位置
    -o "~/Downloads/youtube-dl/%(title)s-%(id)s.%(ext)s"
    # 打印日志
    -v
    ```

## 其他软件

```bash
# 安装iTerm2
$ brew cask install iTerm2
# 安装typora:Markdown格式文本编辑器
$ brew cask install typora
# 安装Chrome
$ brew cask install google-chrome
# iina 视频播放器
$ brew cask install iina
# mos 一个用于在MacOS上平滑你的鼠标滚动效果的小工具, 让你的滚轮爽如触控板。
$ brew cask install mos
# 快速预览mkv等视频
$ brew cask install qlvideo
# 快速预览markdown
$ brew cask install qlmarkdown
```