# Functionality and Use Cases 功能和应用场景

## 功能和应用场景

下面的系统概览图清晰地说明了 Liquity v2 的内部机制，以及其组件如何紧密地结合和协同工作：
Borrower - 借款人
Troves - 金库
Collateral - 抵押资产
Stablecoin - 稳定币
Liquidated collateral - 被清算的抵押品
Liquidated debt - 被清算的债务
Stability pool - 稳定池

Pools containing BOLD for instant liquidations - 包含 BOLD 的池子用于即时清算
Liquidation gains - 清算收益
Earner - 收益人
or delegates IR management - 或委托利率管理
Delegate - 委托
manage IR - 管理利率
Position containing the borrower's debt and collateral - 包含借款人贷款和抵押品的头寸
Interest shares in BOLD - 以 BOLD 计价的利息收益
Redeemer - 赎回者
Mix of ETH/LSTs - ETH/LST 的混合
Interest shares in BOLD used as liquidity incentives distributed via gauge voting - 以 BOLD 计价的利息收益用作参与 gauge 投票分配以激励流动性
Multiple incentivized LP Pools - 多个受激励 LP 池
LP Pool (PIL) - 流动性池 (PIL)
e.g. BOLD/USDC pool - 例如 BOLD/USDC 池
Curve pool - Curve 池
Fees & Incentives - 费用与激励
Liquidity Provider - 流动性提供者
![](<https://liquity.gitbook.io/>~gitbook/image?url=https%3A%2F%2F2492574162-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F3RysRj3vtdw17QvcPosK%252Fuploads%252FGXv1J T5qBvk6lSSti1O6%252Fv2%2520system%2520overview.png%3Falt%3Dmedia%26token%3D05a68455-d72a-4c03-8e9c-2d189496ea1e&width=768&dpr=4&quality=100&sign=a3ef972554165beb9f2675d4d399d861022ce48137e9b0ec49addedea7940576)
![](https://liquity.gitbook.io/~gitbook/image?url=https%3A%2F%2F2492574162-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F3RysRj3vtdw17QvcPosK%252Fuploads%252FGXv1JT5qBvk6lSSti1O6%252Fv2%2520system%2520overview.png%3Falt%3Dmedia%26token%3D05a68455-d72a-4c03-8e9c-2d189496ea1e&width=768&dpr=4&quality=100&sign=a3ef972554165beb9f2675d4d399d861022ce48137e9b0ec49addedea7940576)

### 借款

#### 开通金庫 (Trove)

用户可以通过存入符合条件的抵押资产 [^9]（例如 WETH、wstETH、rETH）开通金庫 (Trove)，并借入最高 LTV 为 90.9% 的 BOLD（就 WETH 而言），这意味着最低抵押率为 110%。各種 LST 將根據其风险特征，而有更高 LTV 要求。

系统还要求开仓时有最低债务金额 [^10]，并留出储备金以激励及时清算。

重要的是，用户需要设置初始利率，范围在 0.5% 到 1000% 之间。虽然利息会随着时间的推移增加借款人的债务，但它会在开仓時预先收取頭 7 天利息（见下文）。

借款人除了自行设定利率，亦可以通过选择委托人的地址這種方式，将利率管理委托给第三方（见下文“利率委托”）。

借款后，协议会铸造所需的 BOLD 代幣并将其转移到借款人的以太坊地址。借款人可以自由使用 BOLD。

Trove 本身以 NFT 表示，並可以自由转移到其他以太坊地址。因此，同一个地址可以管理多个 Trove。

#### 利率调整和预融资期

借款人可以随时调整利率。但是，每次调整时，借款人都必须预付一周（年利率的 1/52）的当前未偿还债务利息。如果借款人在一周之前平仓或调整，他们仍需支付一周的全部利息。

这种预付机制旨在阻止借款人以 Trove 重新开仓和逃避赎回為策略，试图以不公平的方式减少利息支付 [^11]。

#### 清算

借款人必须始终保持其 Trove 有足够的抵押品。如果 Trove 的 LTV 超过相应的清算门槛（最高 LTV），则将被清算 [^12]。

被清算的 Trove 将失去其全部未偿还债务和相当于清算债务的 5%（特殊情况下为 10%，如下所述）的抵押品。扣除清算罚款后，任何剩余的抵押品都可以由清算借款人索取。

清算发生在清算抵押品资产的借款市场内。每项抵押品都有自己的清算门槛、稳定池和单独的再分配程序，仅影响与相同的抵押品支持其债务的借款人。

清算按以下优先顺序执行（例如 WETH）：

Liquidation mechanism - 清算机制
Situation - 情况
Recipients - 接收者
Penalty - 罚金
Stability Pool liquidation - 稳定池清算
Stability Pool contains BOLD - 稳定池尚有 BOLD
Stability Pool depositors pro rata to their deposits - 按存款比例分配给稳定池存款人
Just-in-time (JIT) liquidation - 即时清算
Stability Pool empty - 稳定池为空
User triggering the liquidation - 触发清算的用户
Redistribution - 再分配
All borrowers in proportion to their own collateral - 按其抵押品比例分配给所有借款人
Liquidated borrower can claim any remaining collateral after deducting the penalty - 被清算的借款人可以在扣除罚金后领取剩余的抵押品
![](https://liquity.gitbook.io/~gitbook/image?url=https%3A%2F%2F2492574162-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F3RysRj3vtdw17QvcPosK%252Fuploads%252FQXYguxfCfng6nMStVRDv%252F4.png%3Falt%3Dmedia&width=768&dpr=4&quality=100&sign=8dd36c07f055a2248b23fd63fd566c7b38c557826cc8db85699b513dbc40ac52)

1. **稳定池尚有 BOLD**：从 SP（WETH）中销毁与借款人债务对应的 BOLD 数量，同时从借款人的抵押品（WETH）中取出名义债务价值 [^13] 的 105% 并交给 SP（WETH）。受影响的 SP 存款人将按其当前 BOLD 存款比例获得债务和抵押品份额。

  如果稳定池无法覆盖全部债务，并因清算而完全清空，系统将退回到以下清算模式。

1. **稳定池为空**：清算人可以自由选择两种后备清算模式，以处理超过 SP 资金的债务：

- **(2a) 即时清算**：清算人发送与（剩余）债务相对应的 BOLD 数量，以换取其名义价值的 105% WETH。
- **(2b) 重新分配**：清算人触发重新分配，通过该重新分配，Trove 的全部债务和 110% 的 WETH 债务将按其自身抵押金额的比例重新分配给所有拥有 WETH 抵押品的借款人。因此，相应的借款人将获得清算抵押品的一部分，并看到他们的债务按比例增加。

只要清算触发点略高于最大 LTV（例如 WETH 的 110%）并且 BOLD 没有大幅超过挂钩，清算将为所有接收者（SP、JIT 清算人、其他借款人）带来利润。根据 Liquity v1[^14] 的历史数据，即使在大多数情况下有 5% 的罚款，清算也多數會产生净收益。

重新分配的清算罚款增加到 10%，旨在保护其他借款人在特殊情况下免受损失，特殊情况包括清算时的实际抵押品价值小于清算债务，例如在严重的流动性紧缩、闪电崩盘或延迟清算的情况。

清算是一种无需许可的功能，也可以一次对一批可清算的 Trove 進行清算。清算人将获得略多於實際 gas 成本的补偿。

### 利率委托

该协议提供了两种不同的利率管理委托方式：单独委托和批量委托。在这两种情况下，被推薦出的代表只能更改利率，而不能更改借款人的债务或抵押品。

批量管理提高了 gas 效率，讓第三方为借款人提供专业的利率管理服务。

#### 个人委托

借款人可以选择将利率更新权限授予个人代表地址（例如朋友或热钱包）以设置最高和最低利率。然后，该地址只能在给定范围内更新借款人的利率。

个人委托对于準備度假的用户或希望将 Trove 保存在冷库中但定期使用热钱包管理利率的机构很有用。

借款人可以随时撤销委托或更新范围。

#### 批量委托

批量委托人可同时为特定批次中的每个 Trove 设置和更新共同利率。与个人委托相比，批次具有由代表预设的范围（最高和最低利率）和更改后无法更新之间的最短間隔 [^15]。注册后，批量委托人还可以设置管理费（例如 0.05%），该费用将随着时间的推移对管理的债务收取。

借款人如要将利率管理委托给批量委托人，他必须选择批量委托人之前注册的地址。借款人可以随时撤销委托。

### 其他行动的委托

除了利率委托之外，借款人还可以授权第三方地址（不需要是利率委托人）与其 Trove 进行其他互動。

#### 债务和抵押品管理的委托

委托人通过添加或删除抵押品或借入或偿还 BOLD 获得管理 Trove 债务和抵押品的完全权限。这允许委托人提供自动杠杆或去杠杆等服务，而无需借款人使用代理合约。

虽然委托人只能铸造 BOLD 并将抵押品提取到所有者的地址，但这种委托形式需要对委托人的信任。

#### 授权以采取善举

通过这种更严格的授权，第三方地址仅获得偿还借款人债务或增加更多抵押品的权限。默认情况下，任何 Trove 所有者都可以使用此“善举”选项，所有者可以选择限制權限與单个指定地址或所有者地址本身。

由于其有益的性质，这种类型的授权无需信任，可以促进利率互换等新應用事例。例如，Trove 所有者（“所有者”）和第三方（“设定者”）可以达成外部协议，所有者向设定者支付固定利率，而设定者则设定利率并支付现行利率以避免赎回。

### 稳定池 (Stability Pools, SP)

稳定池是向 LTV 超过清算门槛的 Trove 處理清算需求的第一道防线（参见上文“清算”）。与 Liquity v1 相比，每个借贷市场都有自己的 SP，让存款人可以选择他们希望接触的抵押品以换取收益。

相应 SP 中包含的 BOLD 用于偿还清算借款人的债务以换取抵押品，从而为存款人带来高达 5% 的净清算收益 [^16]。

因此，给定借贷市场的稳定存款人（又称“收益人”）將获得

- 借款人以 BOLD 支付的大部分利息（例如 70%）
- 以相应抵押资产计价的清算收益

下图说明了单个稳定池（例如 WETH 借贷市场）的工作原理，展示了對 LTV 超过清算臨界阈值的 Trove 的清算過程：

Troves with different collateral - 不同抵押品的金库
One SP per collateral type - 每种抵押品类型一个稳定池
Troves - 金库
e.g. WETH - 例如 WETH
Stability Pool - 稳定池
e.g. WETH - 例如 WETH
Interest - 利息
in BOLD - 以 BOLD 计价
Liquidated debt - 被清算的债务
Liquidated collateral - 被清算的抵押品
Debt - 债务
Collateral - 抵押品
Deposits - 存款
Gains - 收益
Liquidated borrower can claim any remaining collateral after deducting the penalty - 被清算的借款人可以在扣除罚金后领取剩余的抵押品
![](<https://liquity.gitbook.io/>~gitbook/image?url=https%3A%2F%2F2492574162-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F3RysRj3vtdw17QvcPosK%252Fuploads%252F qAvzMlTuNwUl1p5QaYA0%252FBOLD%2520earning.png%3Falt%3Dmedia%26token%3D0639cd9f-2db0-467c-8194-695c477bc23a&width=768&dpr=4&quality=100&sign=5ca73d926ab3d9cb7f547aabb1267210994e9fdbbbf41d2661be998f14a13064)
![](https://liquity.gitbook.io/~gitbook/image?url=https%3A%2F%2F2492574162-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F3RysRj3vtdw17QvcPosK%252Fuploads%252FqAvzMlTuNwUl1p5QaYA0%252FBOLD%2520earning.png%3Falt%3Dmedia%26token%3D0639cd9f-2db0-467c-8194-695c477bc23a&width=768&dpr=4&quality=100&sign=5ca73d926ab3d9cb7f547aabb1267210994e9fdbbbf41d2661be998f14a13064)

#### 存款和取款

用户可以随时将 BOLD 存入 SP，并将开始从相应的借款市场收到利息，同时获得清算抵押品的一定比例份额，以换取清算时销毁的 BOLD。存款人隨時可以提取存款，除非相应的借贷市场存在抵押不足的债务，在这种情况下，债务必须先清算後才能提款 [^17]。

为了增加或实现收益，存款人隨時都可以領取 [^18] 其累积的利息和抵押收益。

### 赎回机制

赎回机制確保任何持有人将 1 BOLD 兑换为价值 1 美元的抵押品，从而确保 BOLD 不会持续跌破 1 美元。

当 BOLD 的价值低于 1 美元减去当前赎回费时，套利者将有动机购买和赎回 BOLD 以获取利润，将其价格推回到平价并创造 1 美元左右的价格底线。随着赎回的 BOLD 被销毁，赎回会与市场需求同步减少稳定币的供应。

虽然赎回机制实现了与 Liquity v1 相同的目的，但 Liquity v2 在程序、对借款人的影响、排序、费用和赎回者收到的抵押品分割方面也存在许多差异。

**程序（延迟赎回）**

赎回者首先必须通过发送一定数量的 BOLD 来承诺赎回，然后在允许的时间窗口内确认赎回，即在最短等待时间之后但在结束时间之前 [^19]。

这个两步过程使得在价格反馈更新前进行前置抢先交易变得更加困难，同时消除了由于 AMM 池落后于有效市场价格而产生的短期套利机会。总体而言，这应该会减少“不合理”的赎回。

#### 抵押品分割

与 LUSD 相比，BOLD 由多种抵押品支持。Liquity v2 通過优化流程，並且不允許让赎回者自由选择要赎回的抵押品以确保经济安全。因此，赎回程序以抵押品组合来提供服务，从而增强了 BOLD 的整体支持。

确认后，协议首先将赎回金额分配到所有貸款市场，即在符合条件的抵押品资产之间分配。

分割比例根据相应债务的「外部」部分确定，外部债务定义为针对特定抵押品借入的总债务，减去相应借款市场的 SP 规模。

查看下面三个抵押品市场的赎回示例；即 WETH、wstETH 和 rETH。假设外部债务金额分别为 100 BOLD、50 BOLD 和 100 BOLD，赎回将导致 40%（WETH）- 20%（wstETH）- 40%（rETH）分割：

![](<https://liquity.gitbook.io/>~gitbook/image?url=https%3A%2F%2F2492574162-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F3RysRj3vtdw17QvcPosK%252Fuploads%252FgRNkpBC6td6zmSwTsb zA%252FRedemption%2520flow%2520between%25203%2520assets.jpg%3Falt%3Dmedia%26token%3D9209ee8a-b7e9-4787-82eb-438d80602eff&width=768&dpr=4&quality=100&sign=cd8d265697643d4a6b7d489258078032dd728373c9fdd5ac89313fa909891fee)
Redeemer - 赎回者
Redeems - 赎回
Receives: - 收到：
$10 in WETH (40%) - $10 的 WETH (40%)
$5 in wstETH (25%) - $5 的 wstETH (25%)
$10 in rETH (40%) - $10 的 rETH (40%)
minus fee - 减去费用
WETH Market - WETH 市场
Total debt - 总债务
Troves - 金库
Stability Pool - 稳定池
e.g. wstETH Market - 例如 wstETH 市场
e.g. rETH Market - 例如 rETH 市场
Stability Pool Redeemed amount is split in proportion to the "outside debt" (shown as (=3) from each
market, reducing their total debts and collateral amounts (not shown above) accordingly. - 稳定池赎回金额按“外部债务”的比例分配（如图所示为每个市场的 (=3)），相应减少其总债务和抵押品数量（上图未显示）。
![](https://liquity.gitbook.io/~gitbook/image?url=https%3A%2F%2F2492574162-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F3RysRj3vtdw17QvcPosK%252Fuploads%252FgRNkpBC6td6zmSwTsbzA%252FRedemption%2520flow%2520between%25203%2520assets.jpg%3Falt%3Dmedia%26token%3D9209ee8a-b7e9-4787-82eb-438d80602eff&width=768&dpr=4&quality=100&sign=cd8d265697643d4a6b7d489258078032dd728373c9fdd5ac89313fa909891fee)

#### 对单个 Trove 的顺序和影响

根据确定的抵押品分割，然后協議會按利率順序对所有市场的借款人同时进行赎回，从利率最低的 Trove 开始。如果赎回金额超过当前最低利率借款人的债务，协议则将向下一个较高利率的 Trove 来赎回剩余部分，依此类推。

Interest Rate - 利息
Debt - 债务
Collateral - 抵押品
Redeemed BOLD - 被赎回的 BOLD
Same value removed from debt/collateral - 从债务/抵押品中移除相同的价值
![](<https://liquity.gitbook.io/>~gitbook/image?url=https%3A%2F%2F2492574162-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F3RysRj3vtdw1 7QvcPosK%252Fuploads%252Fl47LKUk2k6EWX1522aZX%252F7.png%3Falt%3Dmedia&width=768&dpr=4&quality=100&sign=35e9598ff8e84b8463394a4e9de9b508cb8b3e31af28e622b01d38143d8601c7)
![](https://liquity.gitbook.io/~gitbook/image?url=https%3A%2F%2F2492574162-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F3RysRj3vtdw17QvcPosK%252Fuploads%252Fl47LKUk2k6EWX1522aZX%252F7.png%3Falt%3Dmedia&width=768&dpr=4&quality=100&sign=35e9598ff8e84b8463394a4e9de9b508cb8b3e31af28e622b01d38143d8601c7)

赎回的 BOLD 用于偿还 Troves 的债务，以换取等值的抵押品。从受影响的 Trove 中取出并交给赎回者的抵押品的实际金额 **c** 计算如下：

c=m⋅pc(t)⋅(1−f(t))c=m⋅pc​(t)⋅(1−f(t))

其中 **m** 是相应抵押品和 Trove 的赎回金额，pc​(t) 是抵押品在当前时间 **t** 的美元预言机价格，**f(t)** 是当前赎回费。这样，赎回费将从取出的抵押品中扣除并保留在 Trove 所有者手中。

#### 赎回费

Liquity v2 采用与 Liquity v1 相同的公式来計算赎回费，但可能使用不同的参数。费率 **f(t)** 是最低费用 fmin​ 和指数衰减基准费率 **b(t)** 针对每个时间步骤 ti​計算出來的总和，如下：

b(ti)=b(ti−1)⋅βΔtb(ti​)=b(ti−1​)⋅βΔt

其中 **β** 是恒定衰减因子。

当在时间 tj​ 提交赎回交易时，基准费率会根据赎回金额 **m** 相对于当前 BOLD 供应量 **n** 出现峰值，具体公式如下：

b(tj)=b(tj−1)+α⋅mnb(tj​)=b(tj−1​)+α⋅nm​

其中 **α** 是恒定峰值参数。

赎回者产生的费用 f(tj)=fmin+b(tj)f(tj​)=fmin​+b(tj​) 在承诺时固定，并在确认赎回时应用于赎回金额（见上文）。因此，只有后续赎回者会受到费用飙升的影响。

### 临界阈值和抵押品关闭

该协议旨在保护每个借贷市场在抵押品资产崩溃的情况下仍不会出现抵押不足的情况。這將通过限制不健康市场中的创造债务和提取抵押品，以及关闭整个市场作为最后手段来实现这一点。

为此，该协议为系统的总抵押率 (Total Collateralization Ratio, TCR) 纳入了两个安全阈值：

- 临界阈值 (Critical Threshold, CT)
- 关闭阈值 (Shutdown Threshold, ST)

如果借入市场的 TCR 低于 CT（例如 150%），则禁止在受影响的市场中创造新债务。只要债务偿还金额大于或等于提取的抵押品（即，只要它总体上改善了个人 CR 和 TCR），就可以允许提取抵押品。与此同时，借款人仍然可以偿还债务或补充抵押品。与 Liquity v1 的恢复模式不同，单个 Trove 的清算门槛（最大 LTV）保持不变，避免了无辜的借款人被不必要地清算 [^20]。

如果 TCR 跌至 ST 以下（例如 110%），协议将触发借贷市场的关闭，并永久禁用除关闭 Trove 之外的所有借贷操作。触发抵押品关闭后，协议将启用并鼓励单一抵押品赎回 [^21]，旨在尽快偿还受影响市场的全部债务。

事实上，用户可以以比 LST 当前预言机价格更优惠的汇率（例如低于市场价格 1%）用相应的抵押品赎回 BOLD，即是以正折扣代替通常的赎回费。

只要 BOLD 的交易价格没有明显高于挂钩价格，套利者就会受到激励，从不健康的借贷市场 [^22] 赎回所有 BOLD，直到其债务跌至 0。

尽管有这些额外的激励措施，但如果其价值大幅下跌或持续下跌过快，系统就无法保证能够成功清算由相应抵押品支持的全部债务。在最坏的情况下，系统最终可能会出现其部分 BOLD 供应没有债务支持的情况。

[^9]: 抵押资产是硬编码的，不受治理。
[^10]: 此最小规模要求确保赎回保持 gas 效率。
[^11]: 精明的用户可以预测即将到来的赎回（包括通过监视内存池）并暂时提高其 Trove 的利率以避免赎回，让赎回向另一个用户执行，然后在不久之后将其利息降低。
[^12]: 清算是一个无需许可的功能，任何人都可以调用。
[^13]: 本协议将 1 BOLD 视为等于 1 美元。但是，BOLD 的市场价值可能高于 1 美元，尤其是在大规模清算和流动性紧缩的情况下，从而减少清算收益。
[^14]: 请参阅 <https://dune.com/queries/81466/162640> 上的图表。
[^15]: 等待时间可以保护委托借款人免于因过于频繁的利率调整而导致债务快速增加，从而导致 7 天预付款被重复收取。
[^16]: 分配的抵押品和销毁的债务均根据 BOLD 存款的规模按比例确定。
[^17]: 就像在 Liquity v1 中一样，这一限制使存款人更难逃避清算。
[^18]: 存款人还可以选择在调整存款时取出未决收益或“储存”以备日后索取。
[^19]: 如果赎回者的确认交易在允许的时间窗口之外处理，赎回者将可收回扣除罚款后的初始 BOLD。
[^20]: 还可以避免许多潜在的边缘情况和攻击面，在这些情况下系统会被拉入 TCR < CT 的状态。
[^21]: 除此之外，定期赎回和清算仍然是可能的。
[^22]: 请注意，当 BOLD 的交易价格远高于挂钩价时，激励措施可能会消失。
