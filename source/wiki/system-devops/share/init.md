---
wiki: system-devops
title: 初始化服务器
rightbar:
   - override: toc
     collapse: auto
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

#### 最大文件打开数配置

### 编译安装 CMake【可选】