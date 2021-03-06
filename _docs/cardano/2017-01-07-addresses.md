---
layout: default
title: 地址
permalink: /cardano/addresses/
group: cardano
---
<!-- Reviewed at a6a1cdf72c7e167a13f500c0679c01fe4cfa0ca8 -->

# 卡尔达诺结算层的地址

要发送和接收价值，基本所有加密货币都使用地址。卡尔达诺结算层支持 3 种类型的地址：

1. 公钥地址
2. 脚本地址
3. 兑换地址


公钥地址在就像其他的加密货币一样，是个正常的，经过哈希的公钥。

脚本地址被用在一个称为『支付脚本 Hash』（P2SH）的交易中。它会自动运作，就像银行里的存款一样：你可以向它汇款，但为了兑换这笔钱你必须满足一些特定的条件，条件由于地址相关的脚本决定。地址本身包含着序列化脚本的哈希值。请阅读下面的 [P2SH](#pay-to-script-hash) 获取更多信息。

赎回地址是 ADA 赎回的一种特殊地址类型。

不仅如此，卡尔达诺结算层还支持 `Unknown` 地址类型。这种类型可以允许我们在未来使用自定义的地址类型。


## 地址看起来像什么？

地址是 `base58` 编码的字符串，例如：

```
Ae2tdPwUPEZKmwoy3AU3cXb5Chnasj6mvVNxV1H11997q3VW5ihbSfQwGpm
```

### 编码

`base58` 编码也是比特币中使用的编码。它使用 58 个符号的字母表来对数据进行编码，这也是它名字的由来。下面就是我们使用的字母表：


```
123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz
```


它去除了非字母数字字符和显示时看起来模棱两可的字母（`0`, `O`, `I`, `l`）；因此它适用于手动输入数据，从可视化的源代码复制数据的普通用户，并且允许通过双击来简单的拷贝和粘贴，不过双击通常会选择整个字符串。


## 公钥地址

就像[介绍](/introduction/#you-own-your-money)章节提到的，在用户界面你看到钱包就代表着在这个特定的钱包中你拥有可以花费这笔钱的私钥。但是这样的花销是如何被网络验证，你又如何接收到别人支付给你的钱呢？答案就是跟控制你钱包价值的私钥一起产生的一个公钥。这个公共的部分即指可以被任何人知道，因此叫做『公钥』。

一个公钥的地址包含了公钥的哈希值。

公钥同时用来在你创建一个交易或其他辅助用途的时候验证你的身份。


## P2SH


P2SH 的思想是为花费制定复杂的规则提供很大的灵活性。与发送一笔交易到公钥地址不同，我们创建一个验证脚本，该脚本使用赎回脚本当做参数。为了赎回里面的资金，我们发送一个赎回请求给验证器，然后开始计算。如果计算结果是 `success`, 前就会被汇到指定的赎回者那里，否则的话什么都不会发生。

引用一下比特币 WiKi：

> 使用 P2SH，你可以将比特币打给一个用多种不常见的方法保护着的地址，不用知道关于该地址安全设置的任何详细信息。接收者可能需要多个人的签名，或一个密码，或一个非常独特的要求才能使用这些比特币。


## 赎回地址

赎回地址就是 P2PKH。这样的地址包含了赎回公钥的哈希值，并且这个钥匙是 [Ed25519](http://ed25519.cr.yp.to/) 公钥。

## 其他地址类型

在未来，我们可能会用升级的系统来引入其他地址类型。请阅读 [查看更多](/cardano/update-mechanism/#soft-fork-updates) 来了解以无缝升级的方式来扩展系统的相关信息。

## 地址结构

地址由三部分组成

* 地址跟
* 地址属性
* 地址类型

我们可以把地址想象成类似 JSON 的结构，例如：

```
Address {
    addrRoot = AbstractHash e63175c654dfd93a9290342a067158dc0f57a1108ddbd8cace3839bd,
    addrAttributes = Attributes {
        data: AddrAttributes {
            aaPkDerivationPath = Nothing,
            aaStakeDistribution = BootstrapEraDistr
        } 
    },
    addrType = ATPubKey
}
```

`addrRoot` 是由 `addrType`、`addrSpendingData`、`addrAttributes` 组成数组的 BLAKE2b-224 哈希值。


addrSpendingData 是一个与地址绑定的特殊值，消费在这个地址的币必须是被公开的。例如，对于公钥来说，这个值就包含着公钥。这样一来，在不知道公钥的时候不可能改变地址属性，因为如果属性改变了，整个地址就变成无效的了。

`addrAttributes` 包含了每个地址重要的属性：衍生的路径和权益的分配。

想要了解更多关于衍生路径的内容请阅读 [卡尔达诺结算层的 HD 钱包](https://cardanodocs.com/technical/hd-wallets/) 章节。

想要了解更多关于权益分配的内容请阅读 [卡尔达诺结算层的转账](https://cardanodocs.com/cardano/transactions/#stake-distribution) 章节。

`addrType` 的值与上面提到的地址类型相对应，在上面的例子中它是一个公钥地址。

### 长度

地址的长度可能是不同的，地址长度与地址类型和附加数据有关。

例如这个地址：


```
Ae2tdPwUPEZKmwoy3AU3cXb5Chnasj6mvVNxV1H11997q3VW5ihbSfQwGpm
```

以及这个地址：

```
4swhHtxKapQbj3TZEipgtp7NQzcRWDYqCxXYoPQWjGyHmhxS1w1TjUEszCQT1sQucGwmPQMYdv1FYs3d51KgoubviPBf
```

都是公钥地址。
