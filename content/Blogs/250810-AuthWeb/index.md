---
title: 苏州大学校园网认证脚本
summary: 苏州大学校园网认证脚本,办公室路由器分发网络。
tags:
  - Web
date: 2025-08-10T13:17:09Z
categories:
  - Work
draft: false
isCJKLanguage: true
showAuthor: true
authors:
  - 葛世杰
---
> 在 [苏大校园网の小妙招 - 四叶草の博客](http://www.wzcwzc.cool/post/32834fab.html) 提供的脚本基础上进行修改调整，详细的原理和实现可点击 🔗 进一步了解

## 一、起因

众所周知，高校的校园网都需要进行认证才能访问网络，每次网络掉线再认证就比较麻烦。于是在网上看看有没有高手研究过相关的内容。果然有前人指路，但是博主写的脚本是网关设备直连校园网，与我要实现的情况不同。

办公室和机房的服务器网关的 ip 地址是上级路由器的 WAN口地址，而非本机 ip 地址。只有网关 ip地址和账户密码绑定才能正确访问外网，于是在原来的脚本基础上稍作修改。

## 二、需求实现

办公室有台群晖 NAS，机房服务器也是 Linux，将 Python 脚本挂载到后台每小时运行一次。简单说一下原理：校园网页面登录账号认证会发送链接请求，我们直接用脚本模拟网页请求实现网关认证。

关键是如何获取上级路由器 WAN 口的地址。要实现和路由器通信获取 WAN 口地址比较麻烦，也不能使用第三方工具获得公网 ip 地址作为网关地址。不过还好校园网认证页面会告诉你的网关地址是多少，可以直接使用 requests 模块抓取网页内容解析网关 ip 。

## 三、也可以直接看这

将脚本内容存为 auth_web.py，在终端中运行 python ./auth_web.py （已经配置好 Python 环境）不要忘记修改认证 ID ，PASSWORD 和 ISP（校园网、中国移动、中国联通、中国电信）

脚本内容：

```bash
import os
import urllib.request
import json
import requests
import re

def get_v4ip_from_webpage():
    url = "http://a.suda.edu.cn"
    try:
        # 发送HTTP GET请求
        response = requests.get(url, timeout=5)
        response.raise_for_status()  # 检查请求是否成功

        # 使用正则表达式查找v4ip的值
        match = re.search(r"v46ip\s*=\s*'([\d\.]+)'", response.text)
        if match:
            return match.group(1)
        else:
            print("未在网页中找到v4ip信息")
            return None
    except requests.exceptions.RequestException as e:
        print(f"请求网页时出错: {e}")
        return None

def check_network():
    # Ping百度，发送2个包，超时2秒
    response = os.system("ping -c 2 -W 2 www.baidu.com > /dev/null 2>&1")
    return response == 0  # 返回True如果能ping通

def campus_auth():
    # 用户配置
    user_name = "id"  # 替换为你的账号
    user_password = "password"  # 替换为你的密码
    ISP = ""  # 运营商：校园网（可选 @zgyd 中国移动 @cucc 联通、@ctc 电信）

    # 直接使用固定IP 10.9.1.3，不检测本机IP
    login_url = f"http://10.9.1.3:801/eportal/?c=Portal&a=login&callback=dr1003&login_method=1&user_account=%2C0%2C{user_name}{ISP}&user_password={user_password}&wlan_user_ip={target_ip}&wlan_user_ipv6=&wlan_user_mac=000000000000&wlan_ac_ip=&wlan_ac_name=&jsVersion=3.3.3"

    # 备用服务器（可选）
    backup_url = login_url.replace("10.9.1.3", "10.9.1.4")  # 替换为备用IP

    print("尝试登录，URL:", login_url)

    try:
        response = urllib.request.urlopen(login_url)
    except urllib.error.URLError:
        print("主服务器连接失败，尝试备用服务器...")
        try:
            response = urllib.request.urlopen(backup_url)
        except urllib.error.URLError:
            print("网络连接失败！请检查网络。")
            return False

    # 解析返回结果
    str_resp = response.read().decode('utf-8')
    str_json = str_resp[7:-1]  # 去除回调函数名（dr1003(...)）
    data = json.loads(str_json)

    # 判断登录结果
    if data.get('result') == "1" and data.get('msg') == "\u8ba4\u8bc1\u6210\u529f":  # "认证成功"
        print("✅ 认证成功！")
        return True
    elif data.get('ret_code') == 2:
        print("⚠️ 已经登录！")
        return True
    elif data.get('ret_code') == 1:
        if data.get('msg') == "QXV0aGVudGljYXRpb24gZmFpbA==":  # "Authentication fail" (Base64)
            print("❌ 运营商错误！")
        elif data.get('msg') == "bGRhcCBhdXRoIGVycm9y":  # "ldap auth error" (Base64)
            print("❌ 密码错误！")
        elif data.get('msg') == "dXNlcmlkIGVycm9yMQ==":  # "userid error1" (Base64)
            print("❌ 用户名错误！")
        else:
            print("❌ 登录失败，未知错误:", data)
    else:
        print("❌ 未知响应:", data)
    return False

if __name__ == "__main__":
    if not check_network():
        print("网络不通，尝试校园网认证...")
        # 获取v4ip并赋值给target_ip
        target_ip = get_v4ip_from_webpage()
        if target_ip:
            print(f"成功获取v4ip: {target_ip}")
        else:
            print("未能获取v4ip")
        campus_auth()
    else:
        print("网络正常，无需认证")

```

