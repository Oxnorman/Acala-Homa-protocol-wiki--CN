# 概述

## 简介

Homa是一个去中心化的流动性质押协议，在Acala和Karura网络上作为可升级的Runime模块（又称Pallet）进行开发和部署。Homa协议将作为预编译的智能合约在两个网络上的EVM+运行。它质押衍生品L-Token可以作为原生的Substrate和ERC-20 Token使用。&#x20;

将在别的应用中使用网络Token相比于在网络中的质押，PoS网络其实创造了机会成本。如果DeFi应用提供了比质押更好的收益，它可能会促使资金从质押集体转移到借贷上去，造成 "银行挤兑 "并危及整个网络的安全。&#x20;

Homa协议建立了一个非托管、去信任的跨链质押池，用户质押自己的Token并收到一个L-Token（例如，质押DOT，收到LDOT），LToken代表了主要的质押资产加上不断累积的质押收益。Homa的设计是为了向投机者提供&#x20;

* 以L-Token为形式的质押资产流动性，该L-Token是同质化的，并自动累积质押收益率&#x20;
* 即时提现，无需等待解绑期
* 一键质押，轻松赚钱&#x20;

Homa是一个开放的、非托管的、无许可的流动性质押协议，任何第三方应用程序和协议都可以自由地集成和使用L-Token来赚取赌注收益，而不受任何限制。

## L-Token

L-token是一个具有收益的质押Token

* 在Acala稳定币借贷中可以作为抵押品，**放大抵押资产（比如DOT，KSM）的风险敞口**，同时获得因质押而收到的年化收益
* 该资产在AcalaSwap中可以提供流动性，**同时赚取交易费和质押收益**
* 可以在账户内产生质押收益，且**不改变账户余额**
* 该资产是**可转账的**，同时可轻松与其他协议**集成**

## 网络

Homa协议可用于

* **Acala网络**：创建了一个去中心化的DOT质押池，质押者收到具有收益的LDOT资产
* **Karura网络**：创建了一个去中心化的KSM质押池，质押这收到具有收益的LKSM资产

## Homa流动性质押网页端App

Homa协议网页端App

* Acala网络，[点击这里](https://apps.acala.network/ldot)
* Karura网络，[点击这里](https://apps.karura.network/lksm)

用到网页端App的钱包

* [Polkadot{js} Extension](https://polkadot.js.org/extension/)

集成Homa协议的移动端钱包

* [Polkawallet](https://polkawallet.io)

