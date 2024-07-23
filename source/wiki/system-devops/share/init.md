---
wiki: system-devops
title: 初始化服务器
rightbar:
   - toc
mathjax: true
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
sudo hostnamectl set-hostname <new_hostname>
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

{% note color:red OpenSSL&nbsp;v1.1&nbsp;和OpenSSL&nbsp;v3.0的生命周期 OpenSSL&nbsp;v1.1&nbsp;于&nbsp;2023&nbsp;年&nbsp;9&nbsp;月&nbsp;11&nbsp;日结束支持，最后一个版本是&nbsp;OpenSSL&nbsp;v1.1.1w OpenSSL&nbsp;v3.0&nbsp;支持至&nbsp;2026&nbsp;年&nbsp;9&nbsp;月&nbsp;7日&nbsp; %}

{% tabs active:1 %}
<!-- tab 升级&nbsp;OpenSSL&nbsp;v3.0&nbsp;版本 -->

下列所有命令在`root`用户或拥有`root`权限的用户下执行

1. 下载安装包并解压
   ```bash
   cd /usr/local/src
   
   wget https://github.com/openssl/openssl/releases/download/openssl-3.0.14/openssl-3.0.14.tar.gz
   
   tar -zxvf openssl-3.0.14.tar.gz
   ```
2. 编译安装
   ```bash
   cd /usr/local/openssl-3.0.14
   
   ./config --prefix=/usr/local/openssl3.0
   
   make && make install
   ```
   编译可选的配置参数有：

   - `--api`：构建 OpenSSL 库以支持指定版本的 API，如果未给出，默认为当前 OpenSSL 版本，和`no-deprecated`使用时，表示不构建对已经废弃 API 的支持
   - `--libdir=DIR`：OpenSSL 安装目录下的目录名称，将在其中安装库文件，默认情况下是`lib`
   - `--openssldir=DIR`：OpenSSL 配置文件的目录已经默认证书和密钥的位置，默认值为`/usr/local/ssl`
   - `--prefix=DIR`：OpenSSL 安装的位置，默认值为`/usr/local`
   - `--strict-warnings`：用于 OpenSSL 开发，如果要为 OpenSSL 开发补丁，则建议使用该选项
   - `--with-brotli-include=DIR`：brotli 安装位置，只有在使用了`enable-brotli`且系统环境变量中不包含时，才使用此选项
   - `--with-brotli-lib=LIB`：brotli lib 库的位置，如果不提供，将使用系统路径中的值
   - `--with-zlib-include=DIR`：zlib 安装位置，只有在使用`zlib`且系统变量中不包含时，才使用此选项
   - `--with-zlib-lib=LIB`：zlib lib 库的位置，如果不提供，将使用系统路径中的值
   - `no-afalgeng`：不构建 AFALG 引擎，在不支持 AFALG 的平台上，将强制执行该选项
   - `enable-ktls`：内核 TLS 支持，该选项将启用内核 TLS 数据路径，从而提高性能，并允许在 TLS 套接字上使用 sendfile 和 splice 系统调用。如果系统上有 TLS 加速器，内核可以使用。在不支持内核 TLS 数据路径的系统上，该选项将会被强制关闭
   - `enable-asan`：这是开发人员选项，它可能无法在所有平台上运行，也绝对不允许在生产环境中使用，它只能与 gcc 或 clang 一起使用，并与`no-shared`选项一起使用
   - `enable-acvp-tests`：构建对自动加密验证协议（ACVP）测试的支持，这是 FIPS 验证所必须的
   - `no-app`：无应用程序，不构建应用程序，例如 OpenSSL 程序，这对最小化程序非常方便，该选项还可禁用测试
   - `no-asm`：不使用汇编代码，这被视为调试、故障处理选项，而不试用于生产
   - `no-async`：不构建对异步操作的支持
   - `no-atexit`：在构建`libcrypto`时不使用`atexit()`，`atexit()`在不同的平台上有不同的语义，在某些情况下会导致 SIGSEGV。此选项会禁用 OPENSSL_cleanup 的`atexit()`注册。默认情况下，NonStop 配置使用`no-atexit`
   - `no-autoalginit`：不自动加载所有支持的密码和摘要，通常情况下，OpenSSL 会提供所有支持的密码和摘要。这只会影响`libcrypto`，如果使用该选项，则必须使用`EVP_add_cipher()`和`EVP_add_digest()`手动添加密码和摘要。该选项将强制执行非共享（non-shared）编译
   - `no-autoerrinit`：不自动加载所有`libcrypto/libssl`错误字符串，通常情况下，OpenSSL 会自动加载人类可读的错误字符串。
   - `enable-brotli`：生成支持 brotli  压缩/解压缩的版本
   - `enable-brotli-dynamic`：与启用`enable-brotli`类型，但 OpenSSL 会在需要时动态加载 brotli 库，只有支持加载共享库的系统才支持该选项
   - `no-autoload-config`：不自动加载默认的 openssl.cnf 文件，默认情况下，OpenSSL 会自动加载配置默认 SSL 选项的系统配置文件
   - `enable-buildtest-c++`：测试时，生成 C++ buildtest 文件，简单检查公共 OpenSSL 头文件是否可以与 C++ 独立使用。启用该选项需要格外小心，对于任何直接作为配置选项给出的编辑器标志，必须确保它对 C 和 C++ 编译器都有效，否则，C++ 编译测试可能会失败，作为替代方案，可以使用特定语言变量`CFLAGS`和`CXXFLAGS`
   - `--banner=text`：在配置文件的最后使用特殊的文本而不是默认的 banner
   - `--w`：在未明确指定选择 32 位或 64 位架构的平台上，Configure 会打印一条警告信息，并等待几秒钟让你中断配置，使用此标记可以跳过等待时间
   - `no-bulk`：只构建一些最小功能集，这是一个开发人员选项，内部用于项目的 CI 构建测试
   - `no-cached-fetch`：不缓存获取，从提供程序获取算法时，绝不缓存算法。通常，提供程序会说明其提供的算法是否可以缓存。使用该选项可以减少运行时的内存使用量，但也会带来明显的性能损失。该选项主要用于帮助检测错误的引用计数
   - `no-capieng`：不构建 capi 引擎，在不支持 CAPI 的平台上，将强制执行该选项
   - `no-cmp`：不构建对证书管理协议（CMP）和证书请求消息格式（CRMF）的支持
   - `no-cms`：不构建对加密信息语法（CMS）的支持
   - `no-comp`：不构建对 SSL/TLS 压缩的支持，如果启用该选项（默认值），则只有同时选择 zlib 或 zlib-dynamic 选项时，压缩才会起作用
   - `enable-crypto-mdebug`：启用加密调试，现在只启用失败的 malloc 功能
   - `enable-crypto-mdebug-backtrace`：选项无效，该项目将使用编译器的地址/漏洞清除器
   - `no-ct`：不构建对证书透明的支持
   - `no-deprecated`：在使用`--api`指定的版本（或当前版本，如果未指定`--api`的话）之前，不构建对已废弃 API 的支持
   - `no-dgram`：无数据格式，不支持基于数据报的 BIO，该选项还会强制禁用 DTLS
   - `no-docs`：无文档，不构建和安装文档
   - `no-dso`：不构建对加载动态共享对象（DSO）的支持
   - `enable-devcryptoeng`：构建 /dev/crypto 引擎，在 BSD 平台上，该选项会被自动选中，可以使用`no-devcryptoeng`禁用
   - `no-dynamic-engine`：不构建动态加载引擎，该选项仅在共享编译时有效
   - `no-ec`：不构建对椭圆曲线的支持
   - `no-ec2`：不构建对二进制椭圆曲线的支持
   - `enable-ec_nistp_64_gcc_128`：启用对某些常用 NIST 椭圆曲线优化实现的支持
   - `no-engine`：不构建对加载引擎的支持
   - `no-err`：不编译任何错误字符串
   - `enable-external-tests`：启用外部测试，与外部测试套件集成
   - `no-filenames`：无文件名，不编译文件名和行号信息
   - `enable-fips`：编译并安装 FIPS 提供程序
   - `no-fips-securitychecks`：不执行与执行安全参数（如密钥最低安全强度）有关的 FIPS 模块运行时检查
   - `enable-fuzz-libfuzzer/enable-fuzz-afl`：支持使用`libfuzzer`或`AFL`进行模糊测试，这些仅为开发人员选项
   - `no-gost`：不构建对基于 GOST 的密码套件的支持
   - `no-http`：禁用 HTTP 支持
   - `no-legacy`：不构建传统提供程序，禁用此项也会禁用传统算法：MD2（默认情况下已禁用）
   - `no-makedepend`：不生成依赖关系
   - `no-module`：不构建任何可动态加载的引擎，这也意味着`no-dynamic-engine`
   - `no-multiblock`：不在 libssl 中构建对一次性写入多条记录的支持
   - `no-nextprotoneg`：不支持下一代协议协商（NPN）TLS 拓展
   - `no-ocsp`：不支持在线证书状态协议（ocsp）
   - `no-padlockeng`：不构建挂锁引擎
   - `no-hw-padlock`：已废弃
   - `no-pic`：不构建支持位置独立代码的程序
   - `enable-pie`：联编时支持位置独立执行
   - `no-pinshared`：不钉住共享库
   - `no-posix-io`：不使用 posix IO 功能
   - `no-psk`：不构建对基于预共享密钥的密码套件的支持
   - `no-rdrand`：不使用硬件 RDRAND 功能
   - `no-rfc3779`：不构建对 RFC3779 “X509 IP 地址和 AS 标识符拓展”的支持
   - `sctp`：构建对流控制传输协议（SCTP）的支持
   - `no-shared`：不构建共享库，只创建静态库
   - `no-sm2-precomp`：禁止在 aarch64 上使用 SM2 预计算表，以使库更小
   - `no-sock`：不构建对套接字 BIO 的支持
   - `no-srp`：不构建对安全远程密码协议（SRP）或基于 SRP 的密码套件的支持 
   - `no-srtp`：不构建对安全实时传输协议（SRTP）的支持
   - `no-sse2`：从 32 位 x86 汇编模块中排除 SSE2 代码路径
   - `no-ssl-trace`：不使用 SSL 跟踪功能
   - `no-static-engine`：不编译静态链接引擎，这只有在不够用就共享引擎时才会产生影响
   - `no-stdio`：不使用 C 头文件 stdio.h 中使用 FILE 类型的任何内容
   - `no-test`：不构建测试程序或运行任何测试
   - `enable-tfo`：编译时支持 TCP 快速打开
   - `no-quic`：不编译 QUIC  支持
   - `no-threads`：不支持多线程应用程序
   - `threads`：运行时支持多线程应用程序，大多数平台默认开启
   - `no-thread-pool`：不支持线程池功能
   - `thread-pool`：构建线程池功能，如果启用，OpenSSL 算法可使用线程池执行并行计算
   - `no-default-thread-pool`：不支持默认线程池功能
   - `default-thread-pool`：使用默认线程池功能构建，如果启用，OpenSSL 可创建和管理的线程数可达到应用程序授权的最大线程数
   - `enable-trace`：支持集成跟踪 api
   - `no-ts`：不构建时间戳（TS）权限支持
   - `enable-ubsan`：使用未定义行为 sanitiser（UBSAN）进行编译，这是一个开发人员选项
   - `no-ui-console`：不使用用户界面（ui）控制台方法进行编译
   - `enable-unit-test`：启动额外的单元测试 API
   - `no-uplink`：不构建对 UPLINK 接口的支持
   - `enable-weak-ssl-ciphers`：构建对被视为‘弱’的 SSL/TLS 密码的支持，启用此选项包括基于 RC4 的密码套件
   - `zlib`：支持 zlib 压缩/解压缩的构建
   - `zlib-dynamic`：与 zlib 选项类似，但 OpenSSL 会在需要时动态加载 zlib 库，只有支持加载共享库的系统才支持该选项
   - `enable-zstd`：构建时支持 zstd 压缩/解压缩
   - `enable-zstd-dynamic`：与启用 zstd 类似，但是在需要时让 OpenSSL 动态加载 zstd 库，只有在支持加载共享库的系统才支持该选项
   - `enable-unstable-qlog`：启用 QUIC 协议的 qlog 输出支持
   - `386`：在 32 位 x86 版本中，仅在汇编模块中使用 80386 指令集
3. 配置环境变量
   ```bash
   mv /usr/bin/openssl /usr/bin/openssl.bak
   mv /usr/include/openssl /usr/include/openssl.bak
   
   ln -s /usr/local/openssl3.0/bin/openssl /usr/bin/openssl
   ln -s /usr/local/openssl3.0/include/openssl /usr/include/openssl
   ```
4. 添加动态链接库路径
   ```bash
   cat /etc/ld.so.conf
   
   echo '/usr/local/openssl3.0/lib' >> /etc/ld.so.conf
   
   ldconfig -v
   ```
5. 验证是否成功
   ```bash
   openssl version
   ```

<!-- tab 升级&nbsp;OpenSSL&nbsp;v1.1.1w&nbsp;版本 -->

下列所有命令在`root`用户或拥有`root`权限的用户下执行

1. 下载安装包并解压
   ```bash
   cd /usr/local/src
   
   wget https://github.com/openssl/openssl/releases/download/OpenSSL_1_1_1w/openssl-1.1.1w.tar.gz
   
   tar -zxvf openssl-1.1.1w.tar.gz
   ```
2. 编译安装
   ```bash
   cd /usr/local/src/openssl-1.1.1w
   
   ./config --prefix=/usr/local/openssl1.1
   
   make && make install
   ```
3. 配置环境变量
   ```bash
   mv /usr/bin/openssl /usr/bin/openssl.bak
   mv /usr/include/openssl /usr/include/openssl.bak
   
   ln -s /usr/local/openssl1.1/bin/openssl /usr/bin/openssl
   ln -s /usr/local/openssl1.1/include/openssl /usr/include/openssl
   ln -s /usr/local/openssl1.1/lib/libssl.so.1.1 /usr/local/lib64/libssl.so
   ```
4. 添加动态链接库路径
   ```bash
   cat /etc/ld.so.conf
   
   echo '/usr/local/openssl1.1/lib' >> /etc/ld.so.conf
   
   ldconfig -v
   ```
5. 验证是否成功
   ```bash
   openssl version
   ```

{% endtabs %}

#### 升级 OpenSSH

{% note color:red 注意事项 SSH&nbsp;升级会导致后续链接断连，一旦产生网络波动的情况下，就无法通过&nbsp;SSH&nbsp;协议进行连接恢复，所以在升级&nbsp;SSH&nbsp;之前，首先配置&nbsp;telnet，后续使用&nbsp;telnet&nbsp;协议进行连接升级配置。%}

{% emoji blobcat ablobcatattentionreverse %}：OpenSSH 9.8p1 要求OpenSSL 版本大于等于 1.1.1 以上

由于升级 SSH 的特殊性，以下操作均在`root`或拥有`root`权限的用户下执行

##### 配置 telnet

{% tabs active:1 %}

<!-- tab 基于&nbsp;yum&nbsp;包管理器的发行版 -->

1. 确认`telnet`及依赖安装`xinetd`的安装情况
   ```bash
   rpm -qa | grep -E "telnet|xinetd"
   ```
2. 安装`telnet`及依赖的`xinetd`
   ```bash
   yum -y install xinetd telnet-server
   ```
3. 修改`telnet`配置文件
   ```bash
   cat >> /etc/securetty <<EOF
   pts/0
   pts/1
   pts/2
   pts/3
   pts/4
   EOF
   ```
4. 启动`telnet`及依赖的`xinetd`，并设置开机自启
   ```bash
   systemctl enable xinetd --now
   systemctl enable telnet.socket --now
   ```
5. 查看`telnet`和`xinetd`状态
   ```bash
   systemctl status xinetd
   systemctl status telnet.socket
   ```
6. 关闭防火墙以及防火墙自启，避免使用`telnet`连接失败
   ```bash
   systemctl stop firewalld
   systemctl disable firewalld
   ```
7. 验证开启的`telnet`
   ```bash
   ss -nltp | grep :23
   ```
8. 使用非本机的终端进行验证
   ```bash
   telnet <$IP>
   ```
   如果出现错误，可以通过`/var/log/secure`日志进行排查

##### 编译安装

1. 安装依赖
   ```bash
   yum -y install gcc gcc-c++ glibc make autoconf pcre-devel pam-devel zlib zlib-devel
   ```
2. 下载源码并解压
   ```bash
   cd /usr/local/src
   
   wget https://mirrors.aliyun.com/pub/OpenBSD/OpenSSH/portable/openssh-9.8p1.tar.gz
   
   tar -zxvf openssh-9.8p1.tar.gz
   ```
3. 编译安装
   ```bash
   mv /etc/ssh /etc/ssh.bak_$(date "+%Y%m%d")
   mv /etc/pam.d/sshd /etc/pam.d/sshd.bak_$(date "+%Y%m%d")
   
   cd /usr/local/src/openssh-9.8p1
   
   ./configure --prefix=/usr/local/openssh9.8 \
      --sysconfdir=/etc/ssh \
      --with-openssl-includes=/usr/local/include \
      --with-ssl-dir=/usr/local/openssl3.0 \
      --with-zlib --with-md5-passwords --with-pam
   
   make && make install
   ```
4. 修改配置文件
   ```bash
   cat >>/etc/ssh/sshd_config << EOF
   UseDNS no
   PermitRootLogin yes
   PubkeyAuthentication yes
   PasswordAuthentication yes
   X11Forwarding yes
   X11UseLocalhost no
   XAuthLocation /usr/bin/xauth
   EOF
   ```
5. 备份可执行文件，并配置新版本的可执行文件
   ```bash
   mv /usr/bin/ssh /usr/bin/ssh.bak_$(date "+%Y%m%d")
   mv /usr/sbin/sshd /usr/sbin/sshd.bak_$(date "+%Y%m%d")
   mv /usr/bin/ssh-keygen /usr/bin/ssh-keygen.bak_$(date "+%Y%m%d")
   
   ln -s /usr/local/openssh9.8/bin/ssh /usr/bin/ssh
   ln -s /usr/local/openssh9.8/bin/ssh-keygen /usr/bin/ssh-keygen
   ln -s /usr/local/openssh9.8/sbin/sshd /usr/sbin/sshd
   ```
6. 使用 Service 管理进程
   ```bash
   # 备份服务文件
   mv /etc/systemd/system/sshd.service /etc/systemd/system/sshd.service.bak_$(date "+%Y%m%d")
   
   # 配置新版本启动脚本
   cp -a /usr/local/src/openssh-9.8p1/contrib/redhat/sshd.init /etc/init.d/sshd
   cp -a /usr/local/src/openssh-9.8p1/contrib/redhat/sshd.pam /etc/pam.d/sshd
   
   # 配置可执行权限
   chmod +x /etc/init.d/sshd
   
   # 重启sshd服务
   /etc/init.d/sshd restart
   ```
7. {% mark 设置开机自启 color:red %}
   ```bash
   # 配置 sshd 加入 chkconfig 管理
   chkconfig --add sshd

   # 配置sshd开机自启动
   chkconfig sshd on
   ```
8. 验证 SSH 版本以及连接性
   ```bash
   ssh -V
   ```
   使用非本机终端进行 SSH 连接验证

##### 关闭`telnet`

在验证 SSH 版本和连接性成功之后，关闭`telnet`端口，保证服务器的安全性。

```bash
systemctl stop xinetd.service
systemctl disable xinetd.service

systemctl stop telnet.socket
systemctl disable telnet.socket
```

<!-- tab 基于&nbsp;apt&nbsp;包管理器的发行版 -->

待编写...

{% endtabs %}

### 系统调优

#### 内核参数配置

内核参数的配置文件在`/etc/sysctl.conf`，对于内核参数的具体讲解如下，根据个人需要选择调试

{% quot 内核参数部分 icon:hashtag %}

{% folders %}
<!-- folder kernel.sysrq = 0 -->

魔法请求键，具体文档内容可以查看：[kernel.sysrq](https://www.kernel.org/doc/html/latest/translations/zh_CN/admin-guide/sysrq.html)

值有三种类型：
- 0 - 完全不能使用 sysrq 键
- 1 - 完全可以使用 sysrq 键
- \>1 - 对于允许的 sysrq 键功能的比特掩码，具体内容文档查看

<!-- folder kernel.core_uses_pid = 1 -->

用于决定是否在生成核心转储文件时包含进程的 PID 信息，这个参数用于区分不同的核心转储文件，使得在系统中存在多个转储文件时，更容易区分它们的进程来源

> 核心转储文件介绍：
> 核心转储文件是在 Unix 和 类 Unix 系统上发生程序崩溃时生成的文件。当一个程序因为某种原因（比如内存错误、段错误等）异常终止时。操作系统会将程序的内存状态以及其他相关信息写入一个特殊的文件中，这个文件就是核心转储文件。
> 核心转储文件包含了程序崩溃时的内存映像、程序计数器值、寄存器内容等信息，这些信息对于诊断程序崩溃的原因非常有用。 通常，核心转储文件的生成是由操作系统内核自动完成的，但是可以通过配置操作系统的参数来控制核心转储文件的生成方式和存储位置。 在一些系统中，核心转储文件可能会被用于调试分析程序崩溃的原因，或者用于改进程序的稳定性和可靠性。

值有两种类型：
- 0 - 生成核心转储文件时不包含进程的 PID 信息
- 1 - 生成核心转储文件时包含进程的 PID 信息

<!-- folder kernel.core_pattern = | /usr/bin/coredump_helper.sh core_%e_%I_%p_sig_%s_time_%t.gz -->

与上一个配置结合使用，脚本内容如下，最终会在`/var/coredump`目录下生成`core_<线程名>_<线程ID>_<进程ID>_sig_<信号值>_time_<coredump时间>.gz`文件

```bash coredump_helper.sh
#!/bin/bash
if [! -d "/var/coredump" ]; then
   mkdir /var/coredump
fi
gzip > "/var/coredump/$1"
```

<!-- folder kernel.msgmnb = 65536 -->

默认值为 16384（16 KB），用来控制系统中消息队列的最大字节数

消息队列是一种进程间通信的方式，用来在不同进程之间传递数据，适当调整这个值可以提高系统的性能和可靠性，但是需要根据具体的应用场景和系统资源来进行权衡和调整

<!-- folder kernel.magmax = 65536 -->

与 msgmnb 不同，msgmax 限制单个信息的大小而不是整个消息队列的大小

<!-- folder kernel.shmmax = 68719476735 -->

用于定义单个共享内存段的最大值，设置应该足够大，能在一个共享片段下容纳整个 SGA，设置过低可能会导致需要创建多个共享内存段，这样可能导致系统性能的下降，本质原因是在实例启动以及 ServerProcesses 创建的时候，多个小的共享内存片段可能会导致当时轻微的系统性能的降低，（在启动时需要创建多个虚拟地址段，在进程创建的时候要让进程对多个段进行“识别”，会有一些影响），但是其他时候都不会有影响。

官方建议值：

对于 32 位系统：可取最大值为 4GB - 1byte，即为 4294967295。建议值为多于内存的一半，所以如果是 32 位系统，一般可取值为 4294967295。32 位系统对 SGA 大小有限制，所以 SGA 肯定可以包含在单个共享内存段中。

对于 64 位系统：可取最大值为物理内存值 - 1byte，建议值为多于物理内存的一半，一般取值大于 SGA_MAX_SIZE 即可，可以取物理内存 - 1byte
- 12G 时： 12 * 1024 * 1024 * 1024 - 1 = 12884901887
- 16G 时： 16 * 1024 * 1024 * 1024 - 1 = 17179869183
- 32G 时： 32 * 1024 * 1024 * 1024 - 1 = 34359738367
- 64G 时： 64 * 1024 * 1024 * 1024 - 1 = 68719476735
- 128G 时： 128 * 1024 * 1024 * 1024 - 1 = 137438953471

<!-- folder kernel.shmall = 16777216 -->

控制可以使用的共享内存的总页数，Linux 的共享内存页大小为 4KB，共享内存段大小读书共享内存页大小的整数倍

内存为 12G 时： 12 * 1024 * 1024 * 1024 / 4 * 1024 = 3145728
内存为 16G 时： 16 * 1024 * 1024 * 1024 / 4 * 1024 = 4194304
内存为 32G 时： 32 * 1024 * 1024 * 1024 / 4 * 1024 = 8388608
内存为 64G 时： 64 * 1024 * 1024 * 1024 / 4 * 1024 = 16777216
内存为 128G 时: 128 * 1024 * 1024 * 1024 / 4 * 1024 = 33554432

官方计算为 ceil(shmmax / PAGE_SIZE )，ceil 为向上取整函数

<!-- folder kernel.shmmni = 4096 -->

可以创建的共享内存段的最大数量

<!-- folder kernel.pid_max = 1000000 -->

用于限制系统可以分配的进程 ID（PID）的最大值

PID 是用来唯一标识每个正在运行的进程的数字标识符，通过限制 kernel.pid_max 的值，系统可以控制能够分配的 PID 的数量，这个数量通常是为了防止 PID 用尽

- 32 位系统：PID 的范围是 0 ~ 2^15 - 1
- 64 位系统：PID 的范围是 0 ~ 2^22 - 1

<!-- folder kernel.dmesg_restrict = 0 -->

限制谁可以访问 dmesg 的信息

值有两种类型：
- 0 - 无限制
- 1 - 受限，只对具有 CAY_SYSLOG 功能的用户访问

<!-- folder fs.file-max = 1638400 -->

在旧内核中，每 4M RAM 可增加 256 个

- 内存为 16G 时：16 * 1024 * 256 / 4 = 1048576
- 内存为 32G 时：32 * 1024 * 256 / 4 = 2097152

在新内核中，每 1M RAM 可增加 100 个

- 内存为 16G 时：16 * 1024 * 100 = 1638400
- 内存为 32G 时：32 * 1024 * 100 = 3276800

默认设置为内存（KB）的 10%

<!-- folder fs.may_detach_mounts = 1 -->

用于控制是否允许卸载已经分离的挂载点

值有两种类型：
- 0 - 不允许
- 1 - 允许

设置为 0 时，不允许`umount`命令

<!-- folder fs.inotify.max_user_instances = 524288 -->

用于限制单个用户可以创建的 inotify 实例的最大数量，inotify 是 Linux 内核提供的一种文件系统监控机制，可以监视文件系统的变化

<!-- folder vm.overcommit_memory = 0 -->

用于控制内存过度承诺策略，内存过度承诺策略允许进程分配比实际可用物理内存更多的内存量，可以提供系统的内存利用率，但是也可能导致内存耗尽和进程被杀死的风险。

值有三种类型：
- 0 - 启用经典的内存过度承诺策略，内核尝试估算出剩余可用的内存
- 1 - 总是过度投入，内核允许超量使用内存直到用完为止，主要用于科学计算
- 2 - 不要过度投入

Redis 设置为 1

<!-- folder vm.overcommit_ratio = [value] -->

当`vm.overcommit_memory = 0`时取消注释，[value] 的值为 0 ~ 100，内存总量为：物理内存 * （value / 100）+ SWAP 分区大小，默认值为 50

<!-- folder vm.swappiness = 60 -->

用于控制系统对 SWAP 空间的使用倾向，默认值为 60，大小可以设置为 0 - 200

值有三种类型：
- 0 - 表示尽可能少的使用 SWAP
- 100 - 表示更倾向于使用 SWAP
- \>100 - 磁盘交互比内存快时使用，如果快2倍，计算值为 2x + x = 200（两个设备 100 的和）, x = 133.33，值为 133.33

Linux 进程使用的内存有两种方式：

1. file-backed pages (有文件背景的页面，比如代码段、比如 read/write 方法读写的文件、 比如 mmap 读写的文件，它们有对应的硬盘文件，因此如果要交换，可以直接和硬盘对应的文件进行交换； 比如读取一个文件，没有关闭，也没有修改，交换时，就可以将这个文件直接放回硬盘，代码处理其实就是删除这部分内容，只保留一个索引，让系统知道这个文件还处于打开状态，只是它的内容不在内存，在硬盘上)，此部分页面叫做 page cache
2. anonymous pages (匿名页，如 stack，heap，CoW 后的数据段等；他们没有对应的硬盘文件，因此如果要交换，只能交换到 swap 分区)，此部分页面，如果系统内存不充分，可以被swap 到 swapfile 或者硬盘的 swap 分区 

第（1）种内存是 Linux 系统更 prefer 的 reclaim 区域，它的 IO cost 相对较低；第（2）种 swap 的 IO cost 相对较高。而 swappiness 是 Linux 的一个内核参数，控制系统在进行内存 swap 时，使用 swap 分区或 filesystem page cache 的权重。 它跟使用了多少百分比的系统内存后才开发 swap 没有关系，这是网络上很多文章的一个错误。

Linux 在进行内存回收（memory reclaim）的时候，实际上可以从1类和2类这两种页面里面进行回收，而 swappiness 值就决定了回收这2类页面的优先级。 swappiness 越大，越倾向于回收匿名页；swappiness 越小，越倾向于回收 file-backed 的页面。当然，它们的回收方法都是一样的LRU算法。

<!-- folder vm.vfs_cache_pressure = 100 -->

该项表示内核回收用于 directory 和 inode cache 内存的倾向

值有三种类型：
- = 100 - 内核会根据 paagecache 和 swapcache，把这两个值保持在一个合理的百分比
- \> 100 - 内核倾向于回收 directory 和 inode cache
- \< 100 - 内核倾向于保留 directory 和 inode cache

<!-- folder vm.max_map_count = 262144 -->

定义了一个进程可以拥有的内存映射区域的最大数量，通常用于限制一个进程可以打开的文件数量

当 Elasticsearch 启动时需要设置

<!-- folder net.ipv4.ip_forward = 0 -->

用于控制 IP 数据包的转发，在单个主机作为终端节点而不是路由器或网关时使用

该值有两种类型：
- 0 - 禁用 IP 数据包转发
- 1 - 启用 IP 数据包转发

在 K8S 集群中，需要设置值为 1，否则某些 CNI 实现会有问题

{% endfolders %}


{% quot 网络参数部分 icon:hashtag %}

{% folders %}
<!-- folder net.ipv4.conf.all.send_redirects = 0 -->

用于控制是否发送 ICMP 重定向消息，当启用时，系统在网路中充当路由器角色时会发送 ICMP 重定向消息，通知发送方有更优的路由路径，这有助于优化网络路由，但也可能被用于某些类型的网络攻击

该值有两种类型：
- 0 - 关闭发送
- 1 - 启用发送

<!-- folder net.ipv4.conf.default.send_redirects = 0 -->

同上

<!-- folder net.ipv4.conf.all.accept_redirects = 0 -->

用于控制是否接受并处理 ICMP 重定向消息

该值有两种类型：
- 0 - 不接受
- 1 - 接受（默认值）

<!-- folder net.ipv4.conf.default.accept_redirects = 0 -->

同上

<!-- folder net.ipv4.conf.all.secure_redirects = 0 -->

用于控制系统是否接受并处理安全的 ICMP 的重定向消息

该值有两种类型：
- 0 - 不接受
- 1 - 接受（默认值）

<!-- folder net.ipv4.conf.default.secure_redirects = 0 -->

同上

<!-- folder net.ipv4.conf.all.rp_filter = 1 -->

该值有三种类型：
- 0 - 禁用反向路径过滤
- 1 - 启用反向路径过滤且对于不是同一接口出去的数据包，将其丢弃
- 2 - 表示严格模式的反向路径过滤，只有当接受到的数据包的源地址能够通过任意可用的接口发出时，才允许通过

<!-- folder net.ipv4.conf.default.rp_filter = 1 -->

同上

<!-- folder net.ipv4.icmp_echo_ignore_all = 0 -->

忽略 ICMP 请求，出于安全考虑，建议开启此项，即禁 Ping

该值有两种类型：
- 0 - 不忽略 ICMP 请求
- 1 - 忽略 ICMP 请求

<!-- folder net.ipv4.icmp_echo_ignore_broadcasts = 1 -->

表示系统将忽略收到的发送到广播地址的 ICMP 的回显请求，不做任何响应，意味着系统不会回应任何发送到广播地址的 ping 请求

该值有两种类型：
- 0 - 不忽略
- 1 - 忽略

<!-- folder net.ipv4.icmp_ignore_bogus_error_responses = 1 -->

用于控制系统是否忽略虚假的 ICMP 错误信息的参数

该值有两种类型：
- 0 - 不忽略
- 1 - 忽略

<!-- folder net.ipv4.conf.all.accept_source_route = 0 -->

用于控制系统是否接受 IP 数据包中指定的源路由选项

该值有两种类型：
- 0 - 表示系统将忽略 IP 数据包中指定的源路由选项
- 1 - 表示系统将接受 IP 数据包中指定的源路由选项

设置为 0 时，是一个安全的选项

<!-- folder net.ipv4.conf.default.accept_source_route = 0 -->

同上

<!-- folder net.ipv4.tcp_syncookies = 1 -->

该参数与性能无关，用于解决 TCP 的 SYN 攻击

<!-- folder net.ipv4.tcp_max_tw_buckets = 6000 -->

用于设置 TIME-WAIT 状态的 TCP 连接的最大数量

在 TCP 协议中，当一条连接关闭时，它会进入 TIME-WAIT 状态，等待两倍的报文最大生存时间（MSL）后才会完全关闭

在 TIME-WAIT 状态下的连接不会占用太多的系统资源，但是如果短时间内产生了大量的连接关闭，TIME-WAIT 状态可能累积起来，导致资源浪费，用于控制系统中 TIME-WAIT 状态连接的数量上限。

用于防止 Dos 攻击

<!-- folder net.ipv4.tcp_tw_recycle = 0 -->

用于控制系统是否启用 TCP TIME-WAIT 状态的快速回收机制

启用此选项后，内核会尝试在 TIME-WAIT 状态的连接上进行时间戳检测，并将相同时间戳的连接视为重复连接，这样系统在处理大量短周期连接时，更快的回收 TIME-WAIT 状态的连接资源

启用后可能导致一些特定网络拓扑下的连接问题，尤其上当连接穿越了对称 NAT 网关时

该值有两种类型：
- 0 - 禁用
- 1 - 启用

<!-- folder net.ipv4.tcp_timestamps = 0 -->

用于控制系统是否启用 TCP 时间戳选项

该值有两种类型：
- 0 - 禁用
- 1 - 启用

<!-- folder net.ipv4.tcp_tw_reuse = 1 -->

用于控制系统是否启用 TCP TIME-WAIT 状态的端口重用机制

启用后，内核会允许在 TIME-WAIT 状态的连接关闭后，立即重用该端口，这样可以更有效地利用系统资源，但是可能会导致一些复用的问题，特别是当之前的连接尚未完全关闭或连接复用不当时

- 0 - 禁用
- 1 - 启用

<!-- folder net.ipv4.tcp_fin_timeout = 30 -->

用于设置 TCP 连接的 FIN-WAIT-2 状态的超时时间

在 TCP 协议中，当一方发送 FIN 报文以表示连接关闭后，它进入到 FIN-WAIT-1 状态，等待另一方确认

另一方收到 FIN 报文后，发送确认后进入 CLOSE-WAIT 状态，并开始关闭连接，而发送了确认一方进入到 FIN-WAIT-2 状态，在此状态下等待一段时间以确保另一方已经完全关闭连接

指定了在 FIN-WAIT-2 状态下等待的超时时间，单位为秒

<!-- folder net.ipv4.tcp_keepalive_time = 1200 -->

用于设置 TCP 连接的空闲超时时间，单位为秒

<!-- folder net.ipv4.tcp_keepalive_probes = 3 -->

启用 keepalive 后，TCP 发出多少个保持性探针，直到觉得断开，默认值为 9

<!-- folder net.ipv4.tcp_keepalive_intvl = 30 -->

探针发送的频率，乘以 tcp_keepalive_probes 时间后杀死不响应的连接，默认值为 75s

<!-- folder net.ipv4.tcp_sack = 1 -->

是否启用有选择的应答，可以通过有选择的应答乱序接受到的报文来提高性能，这样可以让发送者只发送丢失的报文段

对于广域网来说这个选项应该启用，但是会增加 CPU 的占用

<!-- folder net.ipv4.tcp_window_scaling = 1 -->

用于控制是否启用 TCP 窗口缩放选项

TCP 窗口缩放是一种 TCP 拓展机制，用于解决 TCP 的窗口大小受限问题，在传统的 TCP 中，TCP 的窗口大小是 0-65535，这个大小对于高速网络来说可能会限制 TCP 的性能

TCP 窗口缩放允许发送方和接收方在 TCP 连接建立过程中协商一个更大的窗口大小，以适应更高带宽的网络

<!-- folder net.ipv4.tcp_rmem = 4096 131072 6291456 -->

用于设置 TCP 接收缓冲区的大小，TCP 接收缓冲区用于存储接收到的数据，等待应用程序来读取。

由三个数字组成，分别表示最小值，默认值和最大值

<!-- folder net.ipv4.tcp_wmem = 4096 16384 4194304 -->

用于设置 TCP 发送缓冲区的大小，TCP 发送缓冲区用于存储待发送的数据，等待发送到网络上。

这个参数由三个数字组成，分别表示最小值、默认值和最大值
- 4096（4KB） 是最小缓存区大小，默认情况下与系统页大小相同，是为了确保即便在低内存机器上或者存在内存压力的情况下也有缓存
- 16384 （16KB）是默认大小，系统上所有 TCP 套接字缓冲区默认大小，会覆盖 net.core.wmem_default 的大小（只对 TCP 协议生效），net.core.wmem_default 是所有网络协议（TCP、UDP）的默认发送缓冲区大小
- 4194304 （4MB）是最大缓冲区大小，与默认大小不同，该值不会覆盖 net.core.wmem_max 的大小

<!-- folder  net.ipv4.tcp_mem = 94500000 915000000 927000000 -->

用于设置 TCP 协议的内存使用策略，而不是单独的缓冲区大小。

<!-- folder net.core.wmem_default = 8388608 -->

net.core.wmem_default 是所有网络协议（TCP、UDP）的默认发送缓冲区大小

<!-- folder  net.core.wmem_max = 16777216 -->

net.core.wmem_default 是所有网络协议（TCP、UDP）的最大发送缓冲区大小

<!-- folder  net.core.rmem_default = 8388608 -->

net.core.rmem_default 是所有网络协议（TCP、UDP）的默认接收缓冲区大小

<!-- folder  net.core.rmem_max = 16777216 -->

net.core.rmem_max 是所有网络协议（TCP、UDP）的最大接收缓冲区大小

<!-- folder  net.ipv4.ip_local_port_range = 1024 65000 -->

内核参数，用于设置本地端口号的范围，本地端口号是由客户端程序或服务端程序在发起网络连接或监听网络连接时所使用的端口号

第一个数字表示本地端口号的最小值，第二个数字表示本地端口号的最大值

0-1023 是系统保留端口号，用于一些常见的网络服务，故不会影响 nginx 监听的 80，443 端口

<!-- folder  net.ipv4.route.gc_timeout = 300 -->

用于设置路由缓存项的过期超时时间

路由缓存用于存储系统中已知的网络路由信息，以便在转发数据包时快速查找目的地址的路由信息。

当路由缓存中的条目长时间未被使用时，系统可以选择将其从缓存中删除，以释放资源以保持路由表的更新

<!-- folder  net.core.netdev_max_backlog = 262144 -->

用于设置网络设备接收队列的最大长度

网络设备接收队列用于暂时存储从网络接口接收到的数据包，这些数据包等待倍内核进程接收和处理，当接收队列已满时，新到达的数据包将被丢弃或倍网络设备本地处理

单位是数据包数量，它影响系统在高网络负载情况下的性能和稳定性，增加这个值可以提高系统对网络流量的处理能力，但也会增加系统的内存消耗和延迟。

<!-- folder  net.core.somaxconn = 65535 -->

用于设置套接字的最大排队连接数

当服务器接收到大量连接请求时，这些连接请求会排队等待被服务器接受。

当排队连接数达到这个值时，新地连接请求将被拒绝或者延迟处理，直到有空闲的连接队列位置

<!-- folder  net.core.default_qdisc = fq -->

TCP 拥塞控制算法

<!-- folder  net.ipv4.tcp_congestion_control = bbr -->

TCP 拥塞控制算法

<!-- folder  net.ipv4.tcp_max_orphans = 3276800 -->

用于设置系统中允许的最大孤立 TCP 连接数

孤立连接是指客户端发起了连接但是没有完全建立起连接，或者连接已经被关闭但是仍然存在于系统中等待被清理的连接，这些孤立连接会占用系统资源，包括内存和文件描述符等，如果数量过多，可能会导致资源耗尽和系统性能下降

<!-- folder  net.ipv4.tcp_max_syn_backlog = 262144 -->

用于设置 TCP 半连接队列的最大长度

在 TCP 三次握手过程中，服务器在接收到客户端的 SYN 请求后，会将这个连接放入到半连接队列中等待三次握手的完成

这个值指定了可以容纳的等待完成三次握手的连接的最大数量，当半连接队列已满时，新的 SYN 请求将被拒绝或延迟处理，直到有空闲的半连接队列位置，这个参数的设置可以影响系统处理新连接的速度和并发连接数

<!-- folder  net.ipv4.tcp_syn_retries = 1 -->

用于设置 TCP 连接建立中的 SYN 的尝试次数

<!-- folder  net.ipv4.tcp_synack_retries = 1 -->

用于设置 TCP 连接建立在的 SYN+ACK 的尝试次数

<!-- folder  net.ipv4.tcp_slow_start_after_idle = 0 -->

关闭慢启动重启(Slow-Start Restart)，SSR 对于会出现突发空闲的长周期 TLS 连接有很大的负面影响，建议关闭

<!-- folder  net.ipv4.tcp_mtu_probing = 1 -->

启用 MTU 探测，在链路上存在 ICMP 黑洞时启用
- 0 - 禁用
- 1 - 默认关闭，当检测到 ICMP 黑洞时启用
- 2 - 始终启用，使用 tcp_base_mss 的初始 mss

建议为 1

ICMP 黑洞：路由器不能转发超过“下一跳规定的 MTU”长度的数据，最常见地是导致防火墙丢弃 ICMP 包，客户端就会一直重发但没有结果


<!-- folder  net.ipv6.conf.all.disable_ipv6 = 0 -->

是否禁用 IPv6 协议
- 0 - 不禁用
- 1 - 禁用

<!-- folder  net.ipv6.conf.default.disable_ipv6 = 0 -->

同上

<!-- folder  net.ipv6.conf.all.accept_redirects = 0 -->

用于控制 IPv6 网络接口是否接受 ICMPv6 重定向消息
- 0 - 禁止所有的 IPv6 接受是否接受 ICMPv6 重定向消息
- 1 - 允许（默认值）

<!-- folder  net.ipv6.conf.default.accept_redirects = 0 -->

同上


<!-- folder  net.netfilter.nf_conntrack_max = 262144 -->

用于设置连接跟踪表的最大容量

连接跟踪表：是 Linux 内核中的一个功能，用于跟踪网络连接的状态，当数据包经过 Linux 系统时，连接跟踪会检测数据包的状态，并根据需要更新连接跟踪表中的信息，连接跟踪表用于存储当前活动的网络连接信息，包括源地址、目标地址、协议类型、端口号等

net.nf_conntrack_max 和 net.netfilter.nf_conntrack_max 是同一个参数的两种别名，但是它们具有相同的功能和作用

默认计算公式为：

8G 内存大小：

$$ 8 * 1024 * 1024 * 1024 \div 16384\text{(HASH 表大小)} \div (64\text{(64位机器)} \div 32) = 262144 $$

<!-- folder net.netfilter.nf_conntrack_buckets = [value] -->

$$ \text{[value]} = \text{net.netfilter.nf_conntrack_max} \div 4 $$

net.netfilter.nf_conntrack_buckets {% wavy 无法直接修改 %}

临时生效：

8G 内存大小：

```bash
echo 262144 > /sys/module/nf_conntrack/parameters/hashsize
```

重启永久生效

```bash
vim /etc/modprobe.d/iptables.conf
options nf_conntrack hashsize = 262144
```

<!-- folder net.netfilter.nf_conntrack_icmp_timeout = 10 -->

用于设置 ICMP 连接的超时时间

<!-- folder net.netfilter.nf_conntrack_tcp_timeout_syn_recv = 5 -->

<!-- folder net.netfilter.nf_conntrack_tcp_timeout_syn_sent = 5 -->


<!-- folder net.netfilter.nf_conntrack_tcp_timeout_close_wait = 10 -->

用于设置 TCP 连接在 CLOSE_WAIT 下的超时时间

默认值： 60

<!-- folder net.netfilter.nf_conntrack_tcp_timeout_fin_wait = 10 -->

用于设置 TCP 连接在 FIN_WAIT 下的超时时间

默认值： 60

<!-- folder net.netfilter.nf_conntrack_tcp_timeout_time_wait = 10 -->

用于设置 TCP 连接在 TIME_WAIT 下的超时时间

默认值： 120

<!-- folder net.netfilter.nf_conntrack_tcp_timeout_close_wait = 20 -->

默认值： 60

<!-- folder net.netfilter.nf_conntrack_tcp_timeout_last_ack = 10 -->

默认值： 30

<!-- folder net.netfilter.nf_conntrack_tcp_timeout_established = 3600 -->

用于已经建立的 TCP 连接的超时时间，当连接在这个时间内没收到数据包时，内核会自动关闭这个连接

{% endfolders %}


在修改完成后，使用`root`用户强制重新加载`/etc/sysctl.conf`的新配置

```bash
sysctl -p
```

对于网络参数部分，all 的设置会覆盖 default 的设置，新增的网卡会走 default 配置，但是在使用 `sysctl -p` 的情况下，{% wavy 全部恢复为 all 的设置 %}

#### 最大文件打开数配置

暂时未找到相关的说明文档

### 编译安装 CMake【可选】

{% ghcard Kitware/CMake/releases theme:nord %}

1. 卸载已有版本的 CMake
   ```bash
   sudo yum remove cmake -y
   ```
2. 编译安装
   ```bash
   cd /usr/local/src
   
   wget https://github.com/Kitware/CMake/archive/refs/tags/v3.27.0.tar.gz -O cmake-3.27.0.tar.gz
   
   sudo mkdir cmake-3.27.0 && tar -zxvf cmake-3.27.0.tar.gz -C cmake-3.27.0 --strip-components 1
   
   cd /usr/local/src/cmake-3.27.0
   
   sudo ./bootstrap && make && make install
   ```
3. 验证版本
   ```bash
   cmake --version
   ```
