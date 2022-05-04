# API

从JavaScript与Homa流动性质押互动，你需要使用如下接口：

* ``[`@polkadot/api` ](https://polkadot.js.org/docs/api)
* `@acala-network/api`&#x20;
* [Homa staking SDK](https://github.com/AcalaNetwork/Acala/tree/master/modules/homa-lite)  提供了额外便捷的方法.

## 参考资料

* [模块源代码](https://github.com/AcalaNetwork/Acala/tree/master/modules/homa-lite)
* [SDK 文件](https://github.com/AcalaNetwork/acala.js/blob/master/packages/sdk/docs/homa.md)

## 安装SDK

```
yarn add @polkadot/api @acala-network/api@^4.0.2-17 @acala-network/sdk@^4.0.2-17
```

## 初始化SDK

```
const { ApiPromise, WsProvider } = require('@polkadot/api')
const { options } = require('@acala-network/api')
const { Wallet, Homa } = require('@acala-network/sdk')

async function main () {
        const ENDPOINT = 'wss://karura.api.onfinality.io/public-ws'

        const api = await ApiPromise.create(options({ provider: new WsProvider(ENDPOINT) }))
        const wallet = new Wallet(api)
        const homa = new Homa(api, wallet)

        // should wait homa sdk ready
        await homa.isReady

        const env = await homa.getEnv();

        // total staked token 
        console.log(env.totalStaking.toString())
        // total L-Token 
        console.log(env.totalLiquidity.toString())
        // estimated staking APY
        console.log(env.apy.toString())
        // exchange rate between L-Token and staked token e.g. rate of LDOT and DOT
        console.log(env.exchangeRate.toString())
        // minimum mint threshold
        console.log(env.mintThreshold.toString())
        // minimum redeem threshold
        console.log(env.redeemThreshold.toString())
        // staking soft cap
        console.log(env.stakingSoftCap.toString())
}

;main()
```

## 查询 <a href="#state-changing-functions" id="state-changing-functions"></a>

### 获取总质押资产数

通过Homa流动性质押协议质押的总资产数：

```
env.totalStaking.toString()
```

### 获取L-Token总资产数 <a href="#state-changing-functions" id="state-changing-functions"></a>

通过Homa流动性质押协议铸造的L-Token 资产数

```
env.totalLiquidity.toString()
```

### 获取汇率 <a href="#state-changing-functions" id="state-changing-functions"></a>

预期的质押APY

```
env.apy.toString()
```

### 获取质押APY <a href="#state-changing-functions" id="state-changing-functions"></a>

L-Token和质押token之间的汇率（比如，LDOT和DOT之间汇率）

```
env.exchangeRate.toString()
```

### 最小铸造阈值 <a href="#state-changing-functions" id="state-changing-functions"></a>

```
env.mintThreshold.toString()
```

### 最小赎回阈值 <a href="#state-changing-functions" id="state-changing-functions"></a>

```
env.redeemThreshold.toString()
```

### 质押软上限 <a href="#state-changing-functions" id="state-changing-functions"></a>

可以被质押Token的软上限

```
env.stakingSoftCap.toString()
```

### 在给定的L-Token数量的前提下，计算质押资产，反之亦然 <a href="#state-changing-functions" id="state-changing-functions"></a>

```
...init homa sdk

const {
  convertLiquidToStaking,
  convertStakingToLiquid
} = await homa.getConvertor()

const liquidToken = await wallet.getToken('LKSM')
const stakingToken = await wallet.getToken('KSM')

const liquidAmount = new FixedPointNumber(10, liquidToken.decimals)
const stakingAmount = new FixedPointNumber(10, stakingToken.decimals)

console.log(convertLiquidToStaking(liquidAmount).toString())
console.log(convertStakingToLiquid(stakingAmount).toString())
```

## 质押 <a href="#state-changing-functions" id="state-changing-functions"></a>

```
...init homa sdk

const keyring = new Keyring({ type: 'sr25519' })
const account = keyring.addFromMnemonic('XXX') // your account mnemonic
const stakingToken = await wallet.getToken('KSM')
const amount = new FixedPointNumber(10, stakingToken.decimals);

const mint = await homa.getEstimateMintResult(amount)

// check pay amount
console.log(mint.pay.toString())
// check receive amount
console.log(mint.receive.toString())

const call = homa.createMintCall(mint.pay)

// send transaction to mint LDOT/LKSM
await call.signAndSend(account)
```

## 快速赎回

当用户质押时，质押的Token在通过XCM发送到中继链之前，将会被存储在铸币池. 你可以采用快速赎回来将L-Token换成池子里的质押Token.

一些外部Web app可以采用如下流程来完成快速赎回

* 使用`mint.canTryFastReddem`来查询池中是否有足够的质押Token
* 如果有的话，用L-Token来兑换池中的质押Token&#x20;
* 否则，通过 [Acalaswap](https://docs.acalaswap.app) 去中心化交易所来用L-Token 兑换成质押Token

```
...init homa sdk

const keyring = new Keyring({ type: 'sr25519' })
const account = keyring.addFromMnemonic('XXX') // your account mnemonic
const liquidToken = await wallet.getToken('LKSM')
const amount = new FixedPointNumber(10, liquidToken.decimals);

const redeem = await homa.getEstimateRedeemResult(amount, true)

// check request amount to redeem
console.log(redeem.request.toString())
// check receive amount
console.log(redeem.receive.toString())

if (redeem.canTryFastReddem) {
  const call = homa.createRedeemCall(redeem.request, true, keyring.address)

  // send call to redeem LDOT/LKSM
  await call.signAndSend(account)
}
```

## 正常赎回

赎回并且等待解绑时间 + 1时段才能收到质押token. 解绑时间是由质押资产协议定义，比如，对于DOT来说，解绑时间就是28天，对于KSM来说，就是7天。

```
...init homa sdk

const keyring = new Keyring({ type: 'sr25519' })
const account = keyring.addFromMnemonic('XXX') // your account mnemonic
const liquidToken = await wallet.getToken('LKSM')
const amount = new FixedPointNumber(10, liquidToken.decimals);

const redeem = await homa.getEstimateRedeemResult(amount, false)

// check request amount to redeem
console.log(redeem.request.toString())
// check receive amount
console.log(redeem.receive.toString())

const call = homa.createRedeemCall(redeem.request, false)

// send transaction to redeem LDOT/LKSM
await call.signAndSend(account)
```

