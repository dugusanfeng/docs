---
id: PlatON_system_contract
title: 系统合约
sidebar_label: 系统合约
---

## PlatON系统合约

在链启动之后系统内部已经内置了部分合约，这些合约的地址已固定，功能已实现，其中一部分合约为经济模型的实现，并提供各类合约接口与客户端进行交互。

### Staking合约

合约地址:**lat1zqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqzsjx8h7**

PlatON基于`PoS`机制的网络是依靠一组验证者维持正常运行。验证者参与节点间的共识出块，并获得出块收益与Staking收益。想要成为验证者需要至少质押100,000`LAT`到`Staking合约`，并且拥有正在运行的节点。

委托人是那些不能或不想成为验证者的`LAT` 持有者。他们可以将`LAT`委托给验证者并获得收益，委托人委托给验证者的`LAT`只是转移到`Staking合约`，并不是将自己的`LAT`转账给验证者，委托人从始至终都掌握着委托`LAT`的所有权。如果已经是验证人，将无法再成为委托人。

验证人和委托人间的交互主要通过`Staking合约`来完成。`Staking合约`具有以下功能:
  - 质押节点成为验证人
  - 修改验证人信息
  - 解除质押退出验证人列表
  - 查询验证人信息等操作
  - 委托验证人
  - 减持委托
  - 查询委托信息

#### 合约接口
1. 创建验证节点

   该接口是通过质押`LAT`到`Staking合约`中使得节点成为验证节点。质押前需要自己的节点接入网络，并且确保确保节点私钥与bls私钥安全存储。质押时须填写节点信息，接受出块奖励和质押奖励的收益账户，针对委托人的奖励分成(分成越多越有可能吸引到更多用户来委托)，想要质押的`LAT`数量(质押金存在门槛100,000`LAT`，如果低于该金额将质押失败)等信息。如果质押成功，质押金从用户账户/锁仓合约账户的余额转移到`Staking合约`中，节点会在下个结算周期参与选举成为出块节点。验证人的总权重等于自己质押的`LAT`加上被委托的`LAT`的总量，总权重越高被选为共识的概率越大。
  
   接口参数参考Java SDK:[ 创建验证节点](/docs/zh-CN/Java_SDK#staking)。 

2. 修改验证节点信息

   该接口主要用来修改验证人的收益账户，委托分成比例，节点信息等。分成比例每次修改间隔需大于10个共识周期，每次修改的幅度不能大于5%。

   接口参数参考Java SDK:[ 修改验证节点信息](/docs/zh-CN/Java_SDK#updatestaking)。 

3. 增加节点质押金

   该接口用来增加节点质押金额，提高节点权重。每次增加金额的最小值为10`LAT`。验证人的新权重会在下个结算周期生效。

   接口参数参考Java SDK:[ 增加节点质押金](/docs/zh-CN/Java_SDK#addstaking)。 

4. 解除质押

   该接口主要用来退出验证节点。用户无法减持质押，只能完全退出质押。质押解除后，质押金按以下规则解锁：
   
   - 解除质押与质押/增持处于同一结算周期，相关质押金会实时从`Staking合约`中退回到用户质押账户。

   - 解除质押与质押/增持不处于同一结算周期，质押金会锁定168个周期后从`Staking合约`中退还给用户质押时的账户。
   
   发起解除质押后，节点将不会被选为验证人参与共识。

   接口参数参考Java SDK:[ 解除质押](/docs/zh-CN/Java_SDK#unstaking)。 

5. 查询当前结算周期的验证人队列

   该接口用来查询当前结算周期的验证人信息。每个结算周期结束都会根据所有候选验证人的总权重更新下个结算周期的验证人列表。

   接口参数参考Java SDK:[ 查询当前结算周期的验证人队列](/docs/zh-CN/Java_SDK#getverifierlist)。 

6. 查询共识周期验证人列表

   该接口用来查询当前共识周期被选中出块的验证人列表。每个共识轮为430个块，在410块的时候会从当前结算周期的验证人队列中产生下个共识轮可以参与出块的验证人列表。

   接口参数参考Java SDK:[ 查询共识周期验证人列表](/docs/zh-CN/Java_SDK#getvalidatorlist)。 

7. 查询候选验证人信息列表

   该接口用来查询所有候选验证人信息列表，任何成功质押的节点都是候选验证人。

   接口参数参考Java SDK:[ 查询候选验证人信息列表](/docs/zh-CN/Java_SDK#getcandidatelist)。 

8. 查询委托人委托的验证人列表

   该接口用来查询指定的账户委托了哪些验证人。

   接口参数参考Java SDK:[ 查询委托人委托的验证人列表](/docs/zh-CN/Java_SDK#getrelatedlistbydeladdr)。


9. 查询节点质押信息

   该接口用来查询节点的质押信息

   接口参数参考Java SDK:[ 查询节点质押信息](/docs/zh-CN/Java_SDK#getstakinginfo)。


10. 委托/增持委托验证人

    该接口用来委托或者增持委托一个验证人节点。已经质押过节点的用户将无法进行委托。用户可以随时将`LAT`委托给验证人，每次委托的最小金额为10`LAT`，委托成功后，委托人委托给候选验证人的`LAT`转移到`Staking合约`。验证人的新权重会在下个结算周期生效。当验证人被选中参与共识时，会与委托人共享出块与Staking奖励。

    接口参数参考Java SDK:[ 委托/增持委托验证人](/docs/zh-CN/Java_SDK#delegate)。

11. 减持/撤销委托

    该接口用来做减持或者撤销委托操作。委托人可以随时减持或者撤销委托，每次减持委托的最小金额为10`LAT`，完成操作后，`LAT`会实时从`Staking合约`中退回到委托人的账户中，如果减持后剩余委托不足10`LAT`，该委托人的委托将会被撤销。验证人在当前周期获得的收益将按照减持后的委托金额来分配给委托人。

    接口参数参考Java SDK:[ 减持/撤销委托](/docs/zh-CN/Java_SDK#undelegate)。
    
12. 查询委托信息

    该接口用来查询用户的委托信息。

    接口参数参考Java SDK:[ 查询委托信息](/docs/zh-CN/Java_SDK#getdelegateinfo)。

13. 查询区块打包的平均时间

    该接口用来查询打包区块的平均时间。

    接口参数参考Java SDK:[ 查询区块打包的平均时间](/docs/zh-CN/Java_SDK#getavgpacktime)。


14. 查询区块奖励

    该接口用来查询当前结算周期的区块奖励。

    接口参数参考Java SDK:[ 查询区块奖励](/docs/zh-CN/Java_SDK#getpackagereward)。


15. 查询质押奖励

    该接口用来查询当前结算周期的质押奖励。

    接口参数参考Java SDK:[ 查询质押奖励](/docs/zh-CN/Java_SDK#getstakingreward)。


### 治理合约

合约地址:**lat1zqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq93t3hkm**

PlatON采用的链上治理方式，使其能够按照`LAT`持有人的要求发展。治理的目标是确保多数股权始终可以控制网络。任何在社区讨论的治理修改方案，都可通过验证人在链上发起提案并投票公决。
提案的发起与投票都是通过`治理合约`来操作。目前可以发起4种提案，提案说明如下:
- `文本提案`可以用来对某些社区讨论的内容进行投票。流程包括投票阶段，投票通过，投票失败。
- `升级提案`可以用来做链的版本升级。流程包括投票阶段，投票失败，预生效，生效，被取消。
- `取消提案`可以用来取消之前发起的提案。流程包括投票阶段，投票通过，投票失败。
- `参数提案`可以用来改变一些可治理的参数，优化链的生态。流程包括投票阶段，投票通过，投票失败。


#### 合约接口

1. 发起文本提案

   该接口用来发出文本提案。文本提案发出后，验证人可以投支持/反对/弃权票，投票截止时间约为2周左右，当投票率大于50%且投票的人中支持率大于66.7%时，该提案通过。

   接口参数参考Java SDK:[ 提交提案](/docs/zh-CN/Java_SDK#submitproposal)。

2. 发起升级提案

   该接口用来发起升级提案。提案只能由验证人来发起。提案通过后链上版本升级。验证人投票即视为支持票，投票截止时间过后，当支持率大于66.7%时，该提案进入到预生效阶段，于下个结算周期正式生效。

   接口参数参考Java SDK:[ 提交提案](/docs/zh-CN/Java_SDK#submitproposal)。

3. 发起参数提案

   该接口用来发起参数提案。参数提案用来改变链上一些可以治理的参数。验证人可以投支持/反对/弃权票，投票截止时间约为2周左右，当投票率大于50%且投票的人中支持率大于66.7%时，该提案通过。

   接口参数参考Java SDK:[ 提交提案](/docs/zh-CN/Java_SDK#submitproposal)。

4. 发起取消提案

   该接口用来发起取消提案。取消提案可以取消之前发起的文本、参数、升级提案。投票截止时间必须大于被取消的提案的投票截止时间。投票截止时间过后，当投票率大于50%且投票的人中支持率大于66.7%时，成功取消待取消的提案。 

   接口参数参考Java SDK:[ 提交提案](/docs/zh-CN/Java_SDK#submitproposal)。

5. 投票

   该接口用来对正在进行中的提案进行投票。对于版本升级提案只能投赞成票，并且需先升级节点到新版本才能进行投票。

   接口参数参考Java SDK:[ 投票](/docs/zh-CN/Java_SDK#vote)。

6. 发起版本声明

   该接口用来发起版本声明。当PlatON网络通过升级提案升级到新版本的期间，各未投票的验证节点需要同步升级节点到最新版本，并通过版本声明在网络中更新自己的版本状态，否则将不会被选择共识出块。

   接口参数参考Java SDK:[ 发起版本声明](/docs/zh-CN/Java_SDK#declareversion)。

7. 查询提案

   该接口用来查询指定的提案。

   接口参数参考Java SDK:[ 查询提案](/docs/zh-CN/Java_SDK#getproposal)。

8. 查询提案状态

   该接口用来查询提案当前的状态

   接口参数参考Java SDK:[ 查询提案状态](/docs/zh-CN/Java_SDK#gettallyresult)。

9. 查询提案列表

   该接口用来查询处于投票中以及已结束的提案列表。

   接口参数参考Java SDK:[ 查询提案列表](/docs/zh-CN/Java_SDK#getproposallist)。

10. 查询链的版本

    该接口用来查询链当前的生效版本

    接口参数参考Java SDK:[ 查询链的版本](/docs/zh-CN/Java_SDK#getactiveversion)。

11. 查询治理参数值

    该接口用来查询当前块高的治理参数值

    接口参数参考Python SDK:[ 查询治理参数值](/docs/zh-CN/Python_SDK#查询当前块高的治理参数值)。

12. 查询提案的累积可投票人数

    该接口用来查询提案的累积可投票人数。

    接口参数参考Python SDK:[ 查询提案的累积可投票人数](/docs/zh-CN/Python_SDK#查询提案的累积可投票人数)。

13. 查询治理参数列表

    该接口用来查询某个模块的所有治理参数。

    接口参数参考Python SDK:[ 查询治理参数列表](/docs/zh-CN/Python_SDK#查询治理参数列表)。

### Slashing合约

合约地址:**lat1zqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqyva9ztf**

slashing合约主要为了系统安全监测各个节点的行为，对破坏系统安全的违规节点执行处罚，具有以下两大功能：

- 处理不出块的节点

  按照系统规则，每个共识轮会有一批节点来负责生产该轮的区块，每个共识轮固定需要生产的区块数，其中每个节点分配了一个时间窗口和所需生产的区块个数，依次轮流出块完成整个共识轮的区块生产。当某个节点在自己负责的时间窗口内未生产出一个区块，将切换到下一个节点继续生产区块，如此循环直到完成共识轮所需的区块数整个共识轮即完成并由新的一批节点开启下一个共识轮。

  如果某个节点在整个共识轮中都未生产一个区块，那么该节点的零出块行为将被记录，而惩罚条件则为：

  ​    从节点第一次零出块的共识轮开始记录，统计接下来的一段共识轮，如果在此期间节点再次参与了共识轮并且出块了，那么之前的零出块记录将被清除免受处罚，否则当统计时间到了节点将会受到处罚，被罚款（罚款金额=当前区块奖励*n）和状态锁定（锁定一段时间后自动解锁恢复正常状态，在锁定期间节点没有奖励收益并且无法执行任何动作，包括修改信息、撤销质押、参与出块，接受委托）。

- 处理双出或双签的节点

  正常情况下节点在同一个块高只会产生一个区块或者对同一块高的不同区块只签名一个，否则属于违反规则将被处罚，当某个节点出现这种情况时双出、双签的违规证据将会被接收到的节点记录到本地，用户可通过RPC调用`platon_evidences`接口获取违规的证据，然后将证据通过交易的形式发送到链上，系统将对证据进行判别，证据属实将对节点进行处罚。

#### 合约接口

1. 举报双签/双出

   该接口用于举报某个节点的双签或双出行为，用户将证据通过交易的形式发送到惩罚合约，系统核实证据属实后将对节点进行罚款和强制解除质押（取消验证人、备选节点资格），罚款中的一部分（默认：50%）将会作为奖励奖给举报者并且立即到账（转入发起举报交易的账户）。

   接口参数参考Java SDK:[ 举报双签/双出](/docs/zh-CN/Java_SDK#reportdoublesign)。

2. 查询证据是否已举报

   该接口用于发起举报之前检查即将发起举报的证据是否已经被使用过（已举报过），当用户通过调用RPC接口获取到证据后，可先调用该接口检查是否已举报过以避免重复举报。因为举报接口需要花费不少的成本，并且不管证据是否已举报过（使用过）该笔交易的成本依然会被花费掉，所以此接口可提供预先检查，该接口的所有输入参数都从RPC接口`platon_evidences`返回的证据列表中解析而来。

   接口参数参考Java SDK:[ 查询证据是否已举报](/docs/zh-CN/Java_SDK#checkduplicatesign)。

### 锁仓合约

合约地址:**lat1zqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqp7pn3ep**

此合约可用于将资产锁定，限制其在一定时间段内进行流通，实现控制一定时间内的总资产流通量或外部人员其它的锁定需求，当资产的锁定期结束后资产将自动转入解锁接收账户（发起锁定时输入的解锁后接收账户），被锁定的资产被转入锁仓合约后，该笔资产的所属权将归于解锁接收账户，锁定期间该笔资产不能用于交易，虽然属于接收账户但是金额存在锁仓合约中账户余额中是查询不到的，只能从锁仓合约中查询锁仓记录得知该笔资产。合约将根据发起锁定的人指定的释放规则将资产释放给指定解锁接收人，释放规则可自定义为到达某个时间点时解锁全部并转入接收人账户；也可定义为将一笔资产拆分为N份，分M期进行释放；设定每期释放的时间点释放一份，直到M期后锁定资产全部释放完毕，每次释放完资产及时到达接收者账户可自由支配使用。

处于锁定期间的资产由于受限制不能转账，为了补偿用户损失的资产流动性和利益，可将该笔资产用于质押节点和委托节点以盘活资金获取收益以及抵消通胀所带来的价值稀释。但是当资产用于质押或者委托使用后，锁定期到了资产不会转入接收者账户中，而是需要等到用户解除质押或者解除委托时该笔资产才会转入接收者账户中，锁仓合约的释放规则会根据资产的使用情况进行不同处理，如果用于质押和委托的资金只使用了锁仓中的部分资产，则释放期到时只从未挪用的资金中释放，如果不够释放的金额则当前不进行释放需等待用户撤销挪用时释放。

#### 合约接口

1. 创建锁仓计划

   该接口用于创建一笔资产锁定，将发起者的账户中转入指定金额到锁仓合约中，达到指定时间点时转入指定接收者账户中。

   接口参数参考Java SDK:[ 创建锁仓计划](/docs/zh-CN/Java_SDK#createrestrictingplan)。

2. 查询锁仓计划

   该接口用于查询指定账户下的锁仓计划，指定的账户为解锁接收者账户。

   接口参数参考Java SDK:[ 查询锁仓计划](/docs/zh-CN/Java_SDK#getrestrictinginfo)。

### 委托奖励合约

合约地址:**lat1zqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqxlcypcy**

该合约主要用作处理委托收益相关的业务，每个用户委托一个节点之后会产生一笔委托记录，每笔委托记录将会得到被委托节点的分红，分红额度根据节点设置的分红比例从节点的收益中计算，分出来的总分红额度将根据节点的总委托均分，每单位委托将得到一定收益，因此单笔委托能获得的收益和委托量成正比。

在每个结算周期末系统会自动计算每个备选节点给自身所有委托者的分红奖励，所有分红奖励将会立即转入本合约中暂存，也就是每位委托者所获得的收益首先都存在本合约中，需要等待用户主动发起领取收益，系统将会计算该用户有多少待领取的收益在本合约中并立即转入用户账户中。

#### 合约接口

1. 领取委托奖励

   此接口用于提取用户尚未领取的委托奖励，当单个用户委托了多个节点产生多笔委托时，该接口单次只处理20笔委托的奖励领取，剩余的则需再次发起（领取20笔的规则是按照每笔委托未领取的结算周期数排序，未领取的周期数越多优先领取该笔委托的奖励）领取操作，如果待领取的委托奖励的数量少于20笔则直接全部进行领取。

   接口参数参考Java SDK:[ 领取委托奖励](/docs/zh-CN/Java_SDK#withdrawdelegatereward)。 

2. 查询委托奖励

   此接口可用于查询接口发起者所有待领取的委托收益明细，也可以指定查询某笔委托或者多笔委托，返回结果将是一个经过排序的收益明细列表，排序规则为按照每笔委托未领取的结算周期数逆序（待领取委托奖励少于20笔，返回数据将不进行排序）。

   接口参数参考Java SDK:[ 查询委托奖励](/docs/zh-CN/Java_SDK#getdelegatereward)。



### 随机数合约

合约地址:**lat1xqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqpe9fgva**

该合约主要用来生成多个随机数,参数为生成随机数的个数,最多支持500个随机数的生成.返回值为[]byte数组,每32位为一个随机数.

如果仅生成一个随机数,由当前区块的随机数与交易hash异或得到,如果需要生成多个随机数,  第一个随机数由当前区块的随机数与交易hash异或得到, 剩余的随机数由第一个随机数依次异或之前区块的随机数得到.
在同一笔交易中,该合约接口多次调用返回值相同.

区块的随机数生成原理:PlatON的节点打包完区块之后,会采用VRF为该区块生成一个随机数和证明，并且存储到区块的Nonce字段中,随机数种子为前一个块的随机数.

