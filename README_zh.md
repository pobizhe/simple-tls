# simple-tls

[中文](README_zh.md) [English](README.md)

---

可能是最简单的TLS插件。它可以：

- 使用真正的TLS1.3协议来保护并混淆连接。
- 以SIP003插件运行。并且可用于Android系统。
- 在合适的时刻发送随机填充数据包，这能改变连接中数据包时序特征。用于对抗时序流量分析。(可选，目前处于实验阶段)

---

- [simple-tls](#simple-tls)
  - [如何构建](#如何构建)
  - [参数](#参数)
  - [独立模式](#独立模式)
  - [SIP003模式](#sip003模式)
  - [如何无证书时启动服务端](#如何无证书时启动服务端)
  - [客户端如何导入CA证书](#客户端如何导入ca证书)
  - [Android](#android)

## 如何构建

需要go 1.14及以上版本。

    go build

## 参数

          客户端监听地址               服务端监听地址
               |                            |
    |客户端|-->|simple-tls 客户端|--TLS1.3-->|simple-tls 服务端|-->|最终目的地|
                                            |                     |   
                                       客户端目的地地址     服务端目的地地址  

    # 通用参数
    -b string
        [Host:Port] 监听地址
    -d string
        [Host:Port] 目的地地址

    # 传输模式 (客户端和服务端需保持一致)
    -pd
        启用填充数据模式，服务端会发送填充数据来对抗流量分析。

    # 客户端参数
    -n string
        服务器证书名
    -no-verify
        客户端将不会验证服务端的证书。
    -ca string
        加载PEM CA证书文件
    -cca string
        加载base64编码的PEM CA证书

    # 服务端参数
    -s    
        以服务端运行
    -cert string
        PEM 证书路径 
    -key string
        PEM 密钥路径

    # 其他参数
    -cpu int
        The maximum number of CPUs that can be executing simultaneously
    -fast-open
        Enable tfo, only available on linux kernel 4.11+
    -t int
        Idle timeout in sec (default 300)

    # 命令
    -gen-cert
        快速生成一个ECC证书

## 独立模式

    # server
    simple-tls -b 0.0.0.0:1080 -d 127.0.0.1:12345 -s -key /path/to/your/key -cert /path/to/your/cert

    # client
    simple-tls -b 127.0.0.1:1080 -d your_server_ip:1080 -n your.server.certificates.dnsname

## SIP003模式

支持[SIP003](https://shadowsocks.org/en/spec/Plugin.html)插件协议。Shadowsocks会自动设置`-d`和`-b`参数，无需手动设置。

以[shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev)为例:

    ss-server -c config.json --plugin simple-tls --plugin-opts "s;key=/path/to/your/key;cert=/path/to/your/cert"
    ss-local -c config.json --plugin simple-tls --plugin-opts "n=your.server.certificates.dnsname"

## 如何无证书时启动服务端

可以用`-gen-cert`命令快速生成一个ECC证书。

    simple-tls -gen-cert -n certificate.dnsname -key ./my_ecc_cert.key -cert ./my_ecc_cert.cert 

或者`-key`和`-cert`留空，直接启动服务端。服务端会自己生成一个临时的证书。

**请注意：** 这种情况下，客户端需要导入之前生成的证书作为CA。见下。或者`-no-verify`禁用证书验证(不建议，因为这样有潜在MITM攻击风险)。

## 客户端如何导入CA证书

可以用`-cca`或`-ca`导入一个证书或证书包(ca-bundle)作为CA。

`-ca`接受一个路径。

    simple-tls ... ... -ca ./my.ca.cert

`-cca`接受一个被base64编码的证书。

    simple-tls ... ... -cca VkRJWkpCK1R1c3h...4eGdFbz0K==

## Android

simple-tls-android是[shadowsocks-android](https://github.com/shadowsocks/shadowsocks-android)的GUI插件，需要先下载shadowsocks-android。simple-tls-android同样是开源软件，源代码在[这里](https://github.com/IrineSistiana/simple-tls-android)。

<details><summary><code>屏幕截图</code></summary><br>

![avatar](/assets/simple-tls-android-screenshot.jpg)

</details>

---
