---
wiki: system-devops
title: CVE 漏洞列表
rightbar:
  - layout: markdown
    title: '漏洞分类说明 [NOTE.2024.07.23.1]'
    content: |
      漏洞等级分为高危、中危、低危、信息写了四个等级
      - 高危：触发漏洞会对系统、资产造成严重影响
      - 中危：触发漏洞会对系统、资产造成中等程度影响
      - 低危：触发漏洞只会对系统、资产造成轻微的影响
      - 信息泄漏：不会对系统、资产造成可能的影响
  - layout: markdown
    title: '漏洞修复需求 [NOTE.2024.07.23.2]'
    content: |
      在日常工作中，高危和中危漏洞是必须要紧急修复并且持续关注最新的漏洞报告的。
      对于低危漏洞，尽快的修复。
      对于信息泄漏漏洞，查看有可能泄漏的信息，尽可能避免有用信息泄漏
banner: https://w.wallhaven.cc/full/2k/wallhaven-2k67om.jpg
---

## 服务器漏洞

{% quot 高危 icon:hashtag el:h3 %}

{% button CVE-2024-6387&nbsp;漏洞报告 https://www.tenable.com/cve/CVE-2024-6387 icon:https://www.tenablecloud.cn/themes/custom/tenable/img/favicons/favicon.ico %}&nbsp;{% button CVE-2002-20001&nbsp;漏洞报告 https://www.tenable.com/cve/CVE-2002-20001 icon:https://www.tenablecloud.cn/themes/custom/tenable/img/favicons/favicon.ico %}

{% folders %}
<!-- folder CVE-2024-6387 -->
影响版本：
  - OpenSSH < 4.1p1、8.5p1 <= OpenSSH < 9.8p1

解决办法：
  - 防火墙限制用户进行 SSH 访问
  - 升级 OpenSSH 至 9.8p1 版本及以上

<!-- folder CVE-2002-20001 -->
影响版本：
  - OpenSSH 所有版本

解决办法：

1. 确认客户端支持的 kexalgorithms
    
    ```bash
    ssh -Q kex
    ```

    结果如下：

    ```bash
    diffie-hellman-group1-sha1
    diffie-hellman-group14-sha1
    diffie-hellman-group14-sha256
    diffie-hellman-group16-sha512
    diffie-hellman-group18-sha512
    diffie-hellman-group-exchange-sha1
    diffie-hellman-group-exchange-sha256
    ecdh-sha2-nistp256
    ecdh-sha2-nistp384
    ecdh-sha2-nistp521
    curve25519-sha256
    curve25519-sha256@libssh.org
    sntrup761x25519-sha512@openssh.com
    ```

2. 查看客户端使用的 kexalgorithms

    ```bash
    sshd -T | grep -w kexalgorithms
    ```

    结果如下：

    ```bash
    kexalgorithms sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256
    ```

3. 测试使用 `diffie-hellman` 算法连接

    ```bash
    ssh -v -oKexAlgorithms=diffie-hellman-group-exchange-sha256 root@<target-ip>
    ```

4. 禁用 `diffie-hellman` 算法

    ```bash
    echo "KexAlgorithms sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521" >> /etc/ssh/sshd_config
   
    systemctl restart sshd
    ```

5. 再次使用 `diffie-hellman` 算法进行测试

    ```bash
    ssh -v -oKexAlgorithms=diffie-hellman-group-exchange-sha256 root@<target-ip>
    ```

    测试不加模块连接

    ```bash
    ssh root@<target-ip>
    ```

{% endfolders %}

{% quot 中危 icon:hashtag el:h3 %}

{% quot 低危 icon:hashtag el:h3 %}

## 软件漏洞

{% quot 高危 icon:hashtag el:h3 %}

{% button CVE-2016-2183&nbsp;漏洞报告 https://www.tenable.com/cve/CVE-2024-6387 icon:https://www.tenablecloud.cn/themes/custom/tenable/img/favicons/favicon.ico %}

{% folders %}
<!-- folder CVE-2016-2183 -->
影响版本：
解决办法：
  1. 升级 OpenSSL 到最新版本
  2. 对于 Nginx 等服务器禁止使用 DES 加密算法
{% endfolders %}

{% quot 中危 icon:hashtag el:h3 %}

{% quot 低危 icon:hashtag el:h3 %}

