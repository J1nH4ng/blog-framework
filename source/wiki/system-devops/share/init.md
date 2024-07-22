---
wiki: system-devops
title: 初始化服务器
rightbar: toc
---

## 为什么需要初始化

服务器初始化是指在服务器系统安装完毕后进行的一系列配置和操作优化，以确保服务器系统的安全性、性能和可用性。

服务器初始化的过程包括以下几个部分：

### 安全性配置

这是服务器初始化的首要任务。要保证服务器安全性，在初始化时可以执行以下操作：
- 更新服务器操作系统的补丁和安全修复程序，以确保系统中没有已知的漏洞和安全问题
- 禁用不必要的服务和端口，这些服务和端口可能对服务器造成安全威胁
- 设置强密码策略、限制 root 用户的登陆权限、开启 SSH 公钥认证、限制 Sudo 权限等

### 性能优化

在服务器初始化时，需要优化服务器性能，最常见的方法包括：
- 调整服务器硬件参数和系统内核参数来提高系统性能
- 安装必要的软件和库，如：Nginx、Php、Mysql 等，来支持系统的核心功能
- 对服务器进行总体性能测试，以确保服务器能够满足实际需求

### 常用软件和工具的安装

安装一些常用的软件和工具，以提高生产效率和方便日常维护

### 日志管理

在服务器初始化时需要对日志进行管理，开启日志记录，并配置相应的日志策略，以便日后进行分析和故障诊断

### 监控设置

在服务器初始化时，需要设置相应的监控策略和工具，保证服务器的稳定性和可用性，如 CPU 使用率、内存使用率、硬盘使用率、网络带宽等

在服务器初始化过程中，需要针对实际使用场景、硬件资源和其他因素进行不同的优化策略。例如：合理配置服务器负载均衡、使用 SSD 等高性能硬件、进行网络优化等。同时，还需要保持长期的监控和维护，及时发现和解决系统中的问题，再维护服务器即可。

## 初始化详细内容

### 修改服务器的 Hostname

修改服务器的 hostname 用于标记服务器的具体使用情况，具体的命令如下：

```bash
sudo hostnamectl set-hostname <newhostname>
```

对于服务器 hostname 的命名风格应该尽可能的统一，默认按照如下的命名规律进行修改：

- 单应用服务器：【应用名称-主从关系-标记位-IP地址】
- 多应用服务器：【应用名称1-应用名称2-主从关系-标记位-IP地址】

### 更新系统组件并安装需要的软件

{% tabs active:1 %}

<!-- tab 使用 YUM 包管理器 -->

更新系统软件

```bash
sudo yum update -y
```

安装需要的软件

```bash
sudo yum install -y gcc gcc-c++ make perl perl-devel \
        net-tools kernel-devel openssl-devel \
        telnet ntpdate vim lsof htop iotop wget lrzsz

sudo yum groupinstall "Development tools" -y
```

<!-- tab 使用 APT 包管理器 -->

更新系统软件

```bash
sudo apt update -y
```

{% endtabs %}

### 时区或时钟同步设置

1. 查看当前时间设置，使用`timedatectl`命令查看
    ```bash
    timedatectl
    ```
    输出结果如下：
    ```bash
                    Local time: 一 2024-07-22 15:14:19 CST
                Universal time: 一 2024-07-22 07:14:19 UTC
                      RTC time: 一 2024-07-22 07:07:00
                     Time zone: Asia/Shanghai (CST, +0800)
     System clock synchronized: yes
                   NTP service: active 
               RTC in local TZ: no
    ```
    其中：
    - Local time：当地时间
    - Universal time：伦敦时间
    - RTC time：硬件时间
    - Time zone：时区
2. 如果时区不对，可以使用下列命令进行修改（国内为东八区）
    ```bash
    sudo timedatectl set-timezone Asia/Shanghai
    ```
3. 配置时钟同步
    - 配置定时任务
        ```bash
        sudo crontab -e
        ```
      写入如下内容：
        ```bash
        */30 * * * * /usr/sbin/ntpdate ntp.aliyun.com
        ```
    - 首次手动执行时钟同步并写入硬件
        ```bash
        sudo /usr/sbin/ntpdate ntp.aliyun.com
        
        sudo clock -w
        ```

### 配置 VIM 编辑器

配置 `.vimrc` 文件

```bash
sudo vim ~/.vimrc
```

`.vimrc` 文件详细内容解释

```ini
" 使用 Vim 的 modeline 来设置当前文件的 textwidth，避免输入超过 78 个字符自动换行
" 使用 :verbose set textwidth? 命令可以看到 vim 默认为 vim 配置了 78 个字符，当超过时
" 按下空格键会自动换行，将 textwidth 设置为 0 关闭该功能
" vim: tw=0 :

" 去掉有关 vi 一致性模式，避免操作习惯上的局限
set nocompatible

" 显示行号，不使用，在复制时可能会导致行号也被复制进去
" set nu

" 禁止生成临时文件,看个人情况打开
" set nobackup
" set noswapfile

" 高亮显示匹配的括号
set showmatch

" 设置展开 tab 成空格
set et

" 语法高亮
syntax on

" 自动缩进，开启的情况下，当从外部拷贝多行以空格开头的内容时会有较多缩进
" set autoindent

" 智能缩进
set smartindent

" 检测文件类型，并载入文件类型插件，为特定文件类型载入相关缩进文件
filetype plugin indent on

" 设置自动补全
set completeopt=longest,menu

" 设置 softtabstop 有一个好处是使用 Backspace 键可以一次删除 4 个空格
" softtabstop 的值为负数时，会使用 shiftwidth 的值，两者保持一致，方便统一缩进
set softtabstop=-1

" 自动缩进时，缩进长度为4
set shiftwidth=4

" 设置制表符的缩进为4
set tabstop=4

" 设置退格键可用
set backspace=2

" 带有如下符号的单词不要被换行分割
set iskeyword+=_,$,@,%,#,-

" 在底部显示，当前处于什么模式
set showmode

" 光标所在行高亮
set cursorline

" 自动折行
set wrap
" 关闭自动折行
set nowrap

" 只有遇到指定的符号才发生折行，不会在单词内部折行
set linebreak

" 搜索时，高亮显示匹配结果
set hlsearch

" 搜索模式时，没搜索一个字符，就跳转到匹配的结果
set insearch

" 搜索时，忽略大小写
set ignorecase

" 进入粘贴模式（粘贴时禁用缩进等影响格式的东西）
" set paste
" 结束粘贴模式
" set nopaste
```

### 升级重要的系统组件

#### 升级 OpenSSL

{% ghcard openssl/openssl/releases theme:nord %}

{% emoji blobcat ablobcatattentionreverse %}：根据需要选择相应的版本

{% note color:cyan OpenSSL&nbsp;v1.1&nbsp;和&nbsp;OpenSSL&nbsp;v3.0&nbsp;的区别 %}
