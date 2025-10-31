---
title: 静态源代码安全扫描工具测评结果-SonarQube
slug: static-source-code-security-scanner-evaluation-sonarqube
author: 洞源实验室
date: 2023-10-12 18:00
categories: 安全测评
tags:
  - SonarQube
  - 静态代码分析
  - 源代码扫描
  - 安全产品
  - 安全测评
---

## 测评背景

随着数字技术的进步，网络安全行业日益发展，企业对于DevSecOps的应用和落地的需求日益增加，静态源代码安全扫描工具已成为其中的关键产品或工具。  

2023年5月30日，OWASP中国基于目前行业内的相关调研报告以及行业共识发布了 **《静态源代码安全扫描工具测评基准》v2.0** 版本，对于静态源代码安全扫描工具的测评基准进行了升级。

在此基础上，【供应链安全检测中心】联合【武汉金银湖实验室】邀请国内外各大厂商以部署环境、安全扫描、漏洞检测、源码支持、扩展集成、产品交互以及报告输出七个维度为基准，开展“静态源代码安全扫描工具测评活动”。

## 测评详情

**产品名称：** SonarQube Community Edition

**版本选择：** SonarQube Community Edition 10.1

**测评依据：** 《静态源代码安全扫描工具测评基准》 v2.0

**基准测评项：** 部署环境、安全扫描、漏洞检测、源码支持、扩展集成、产品交互、报告输出

**部署环境：** 处理器：Inter(R) Core(TM) i5-7200U / 内存：16 GB / 硬盘：500 GB

## 测评结果

**测评结果总览**

本次测评从七个维度对产品进行测评，根据测评详情描述，测评结果分为：满足、部分满足和不满足。

![](./static-source-code-security-scanner-evaluation-sonarqube/assets/17617399608990.07964355112494881.png)

**平均扫描速率（单位：秒）**

![](./static-source-code-security-scanner-evaluation-sonarqube/assets/17617399609730.4594952293703739.png)

![](./static-source-code-security-scanner-evaluation-sonarqube/assets/17617399610510.027457761937906833.png)

<center>千行级样本扫描速度</center>

![](./static-source-code-security-scanner-evaluation-sonarqube/assets/17617399611280.48637772265188917.png)

<center>万行级样本扫描速度</center>

![](./static-source-code-security-scanner-evaluation-sonarqube/assets/17617399612070.24489799384474165.png)

<center>百万行级样本扫描速度</center>

**平均漏洞误报率/漏报率**

![](./static-source-code-security-scanner-evaluation-sonarqube/assets/17617399612960.9326598257467313.png)

![](./static-source-code-security-scanner-evaluation-sonarqube/assets/17617399613830.5950536742155648.png)

<center>漏洞误报率/漏报率结果汇总</center>

## 报告下载

完整版报告下载：[点击下载](./assets/静态源代码安全分析工具-SonarQube测评报告（终版）.pdf)

> 版权所有 © 洞源实验室 2023
>
> 未经授权，禁止用于商业用途。
>
> 如需授权使用，请联系：repoog#gmail.com