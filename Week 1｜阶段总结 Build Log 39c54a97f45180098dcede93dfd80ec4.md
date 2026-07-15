# Week 1｜阶段总结 Build Log

# 学习路径

## 完成第一笔测试网交易，并使用区块浏览器查看交易状态

1. **建立课程专属钱包Account**

0xdd567fD2e41762fC895154F8c5368C48E6D3d096

1. **Add Network - Monad Testnet** 

1. **第一笔转账**

0x6e479edec79d88a270a7bd6f628b8834d7f281c91901575fd31a54165b725b21

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image.png)

## **了解智能合约部署原理和流程，用 AI 生成一个最小 Solidity 合约**

### 智能合约“上链” (部署) 过程

“上链”在技术术语中称为 **部署 (Deploy)**。本质上是把人类能看懂的代码，变成链上自动运行的“无人售货机”。

#### **上链 4 步曲**

1. **编译 (Compile)**
    - **作用：** 把 Solidity 代码翻译成机器语言。
    - **产出 A：字节码 (Bytecode)。** 一堆十六进制乱码（如 `0x6080...`），是真正上链的本体。
    - **产出 B：ABI。** 相当于合约的“操作说明书”，告诉外界（前端网页、数据分析师）有哪些函数可以调用。
2. **构建交易 (Build Transaction)**
    - 发送一笔特殊的转账交易。
    - **接收方 (To)：** 留空。
    - **交易内容 (Data)：** 填入字节码。
3. **支付 Gas 费 (Pay Gas)**
    - 链上存储极贵（寸土寸金）。代码越长，燃烧的 Gas 费（给矿工的工钱）越多。支付后向全网广播。
4. **诞生新地址 (Contract Creation)**
    - 交易被打包进区块。
    - 区块链凭空生成一个**全新的 `0x` 独立地址**，合约正式营业，不可篡改。

## 实操神器：Remix IDE

- **网址：** `remix.ethereum.org`
- **定位：** Web3 新手友好的网页端代码编辑器。
- **极简流程：**
    1. 粘贴代码 ➡️ 2. 点击编译 ➡️ 3. 点击部署（自带免费虚拟测试网） ➡️ 4. 直接在网页底部点按钮测试漏洞。

### 第一个智能合约-积分合约

#### **Prompt to [Replit](https://replit.com/)**

“Act as a senior Web3 developer. Please write a minimal Solidity smart contract (version 0.8.x) for a Point Record system.Requirements:1. The contract should have an `admin` (the address that deploys the contract).2. A mapping to store the point balance of each address.3. A function `addPoints(address user, uint256 amount)` to increase a user's points.4. A function `deductPoints(address user, uint256 amount)` to decrease a user's points.5. ONLY the `admin` can call the add and deduct functions.6. A function to check the balance of any address.Please keep the code as simple as possible and add explanatory comments.”

#### Solidity Code

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

/**

@title PointRecord

@notice A simple on-chain point ledger.

`Only the admin (deployer) can add or deduct points.`

/
contract PointRecord {

```
// -----------------------------------------------------------------------
// State variables
// -----------------------------------------------------------------------

/// @notice The account that deployed the contract; has exclusive write access.
address public admin;

/// @notice Stores the point balance for every address.
mapping(address => uint256) private balances;

// -----------------------------------------------------------------------
// Events
// -----------------------------------------------------------------------

/// @notice Emitted when points are added to a user's balance.
event PointsAdded(address indexed user, uint256 amount);

/// @notice Emitted when points are deducted from a user's balance.
event PointsDeducted(address indexed user, uint256 amount);

// -----------------------------------------------------------------------
// Modifiers
// -----------------------------------------------------------------------

/// @dev Reverts if the caller is not the admin.
modifier onlyAdmin() {
    require(msg.sender == admin, "PointRecord: caller is not the admin");
    _;
}

// -----------------------------------------------------------------------
// Constructor
// -----------------------------------------------------------------------

/**
 * @notice Sets the deployer as the admin.
 */
constructor() {
    admin = msg.sender;
}

// -----------------------------------------------------------------------
// Write functions (admin only)
// -----------------------------------------------------------------------

/**
 * @notice Increase a user's point balance.
 * @param user   The address to credit.
 * @param amount The number of points to add (must be > 0).
 */
function addPoints(address user, uint256 amount) external onlyAdmin {
    require(user != address(0), "PointRecord: zero address");
    require(amount > 0,        "PointRecord: amount must be > 0");

    balances[user] += amount;
    emit PointsAdded(user, amount);
}

/**
 * @notice Decrease a user's point balance.
 * @param user   The address to debit.
 * @param amount The number of points to deduct (must be > 0 and <= current balance).
 */
function deductPoints(address user, uint256 amount) external onlyAdmin {
    require(user != address(0),          "PointRecord: zero address");
    require(amount > 0,                  "PointRecord: amount must be > 0");
    require(balances[user] >= amount,    "PointRecord: insufficient balance");

    balances[user] -= amount;
    emit PointsDeducted(user, amount);
}

// -----------------------------------------------------------------------
// Read functions (public)
// -----------------------------------------------------------------------

/**
 * @notice Returns the point balance of any address.
 * @param user The address to query.
 * @return     The user's current point balance.
 */
function getBalance(address user) external view returns (uint256) {
    return balances[user];
}
```

}

#### **合约检查**

[Solidity 合约结构解析与审计报告.pdf](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/Solidity_%E5%90%88%E7%BA%A6%E7%BB%93%E6%9E%84%E8%A7%A3%E6%9E%90%E4%B8%8E%E5%AE%A1%E8%AE%A1%E6%8A%A5%E5%91%8A.pdf)

## **部署第一个合约，并实现合约交互**

### **First time deploying a smart contract!**

**(PointRecord)**

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image%201.png)

⬆️environment设置

![Screenshot 2026-07-09 at 01.12.33.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/Screenshot_2026-07-09_at_01.12.33.png)

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image%202.png)

**我的第一个智能合约地址：**0xb11c55db7fe0a03c22e12e92e4a6b06bac708b18

**部署交易 hash:** 0x80b6e392ec39bdd29030dd4e647c052f13259ab4ba5d36d669d5064738a14e10

**on block explore:** https://testnet.monadscan.com/tx/0x80b6e392ec39bdd29030dd4e647c052f13259ab4ba5d36d669d5064738a14e10

### **Interact with a deployed contract**

#### **ABI（应用程序二进制接口）**

当你点击“编译（Compile）”按钮时，Remix 实际上为你生成了一份隐藏的“说明书”（也就是 ABI）。这份说明书详细记录了你代码里有哪些函数、参数是什么格式。

Remix 的交互界面正是根据这份“食谱”动态生成的——它读取了你代码里所有定义为 `public` 或 `external` 的函数，自动为你把对应的按钮（函数调用入口）画在界面上。

所以，当你看着那些按钮时，你实际上是手里握着这份代码专属的“动态遥控器”！你写的每一行代码，现在都变成了可以随时触发的按钮，直接操控着链上的逻辑。这种“代码即界面”的感觉，就是 Web3 开发最酷的地方！

#### **Write function**

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image%203.png)

![Screenshot 2026-07-09 at 01.46.09.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/Screenshot_2026-07-09_at_01.46.09.png)

Transaction hash: 0x4b4b613c27d845ae473219fbac4392cb3bf3b5f7f0eecf6bc1ffc47727b9c75b

https://testnet.monadscan.com/tx/0x4b4b613c27d845ae473219fbac4392cb3bf3b5f7f0eecf6bc1ffc47727b9c75b

#### **Read function**

Read function（读函数）只是向节点查询数据，它们不会改变区块链的状态，当然也不会产生任何新地址。

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image%204.png)

Read function在Remix上容易报错，

- **测试网压力：** 你正在使用的 Monad 测试网，节点可能正在同步数百万个区块，或者同时有成千上万个开发者在疯狂请求数据。
- **节点超时：** RPC 节点通常设置了很短的响应超时时间（例如 5-10 秒）。如果节点响应慢了一拍，Remix 就会认为连接断开了，直接抛出一个 `missing revert data` (即“我没收到预期的数据”)。

```
call to PointRecord.getBalance errored: Error occurred: missing revert data (action="call", data=null, reason=null, transaction={ "data": "0xf8b2cb4f000000000000000000000000dd567fd2e41762fc895154f8c5368c48e6d3d096", "from": "0xdd567fD2e41762fC895154F8c5368C48E6D3d096", "to": "0x95066e5B84955b6df5D61219c5ACb586252153dd" }, invocation=null, revert=null, code=CALL_EXCEPTION, version=6.14.0).

missing revert data (action="call", data=null, reason=null, transaction={ "data": "0xf8b2cb4f000000000000000000000000dd567fd2e41762fc895154f8c5368c48e6d3d096", "from": "0xdd567fD2e41762fC895154F8c5368C48E6D3d096", "to": "0x95066e5B84955b6df5D61219c5ACb586252153dd" }, invocation=null, revert=null, code=CALL_EXCEPTION, version=6.14.0)
```

**Block Explorer上数据永远是最稳的**，不会像 Remix 这样容易受到网络波动干扰。

### README

# PointRecord 积分账本合约

这是一个极简且功能完备的积分管理智能合约。它允许管理员通过区块链记录、增减特定地址的积分余额，并提供公开查询接口。

## 什么是这个合约？

PointRecord 就像一个部署在区块链上的“自动记账员”。它有三个核心功能：

1. **积分增发 (Add Points)：** 仅限管理员操作，给指定用户增加积分。
2. **积分扣除 (Deduct Points)：** 仅限管理员操作，扣除积分（内含防负数逻辑）。
3. **余额查询 (Get Balance)：** 任何人都可以随时查询任意地址的当前积分。

## 🚀 部署指南 (Remix IDE)

1. **准备环境：**
    - 打开 [Remix IDE](https://remix.ethereum.org/)。
    - 在左侧文件面板中创建 `PointRecord.sol` 并粘贴你的代码。
2. **编译：**
    - 点击左侧的 "Solidity Compiler" (S 图标)。
    - 点击 **Compile PointRecord.sol**。确保出现绿色对勾。
3. **部署：**
    - 点击左侧的 "Deploy & Run Transactions" (箭头图标)。
    - **Environment** 选择 Browser Extension `- MetaMask` (确保你的小狐狸已经连上了目标测试网，如 Monad Testnet)。
    - 点击 **Deploy**。
    - 在 MetaMask 弹窗中点击“确认”。

## 💡 如何交互 (Read vs Write)

一旦部署成功，你会在 `Deployed Contracts` 下方看到你的合约。

### 1. Read Function (读函数 - 蓝色按钮)

- **功能：** 例如 `getBalance`。
- **特点：** 调用完全免费，不需要消耗 Gas，不需要签名。
- **注意事项：** 在测试网上，如果网络不稳定（报错 `missing revert data`），这属于节点问题，**多点几次**即可，不用担心代码错误。

### 2. Write Function (写函数 - 橙色按钮)

- **功能：** 例如 `addPoints`, `deductPoints`。
- **特点：** 会修改区块链状态。调用需消耗 Gas，且**必须通过 MetaMask 签名确认**。
- **操作：** 填写参数（地址, 数量），点击按钮，然后在弹出的 MetaMask 弹窗中点击“确认”。

## 🛠️ 常见问题排查 (Troubleshooting)

- **报错 `missing revert data` (Read 函数)：**
这是测试网 RPC 节点抽风，数据没返回。直接多点几次，或者去**区块链浏览器 (Explorer)** 输入你的合约地址查询，那是数据最真实的地方。
- **按钮点不动/交互没反应：**
    - 检查 MetaMask 是否已连接到 Remix（看左侧 Account 栏是否有数字）。
    - 如果 Remix 没反应，尝试刷新页面，然后通过`Add Contract` 重新挂载合约地址。
- **如何确认合约是否成功上链？**
复制你的合约地址，直接在 [Monad Chain Testnet Explorer](https://testnet.monadscan.com/) 搜索。如果能看到[**Transaction Details]**，说明合约在链上已“正式营业”。

## **Monad 理解**

#### **以【**GameFi 实时竞技领域】**为例**

### 1. 为什么需要频繁交互？

在这类实时竞技（如卡牌对决、暗棋）中，玩家的每一次**抽牌、出牌、走位、发动技能和格挡**都是一次独立的状态变更。一局 3 分钟的对局中，双边玩家会产生高达上百次的操作交互，属于典型的高频微操作场景。

### 2. 链慢或费高会怎样？

- **体验灾难**：若出块慢，每次出牌都要转圈等待数秒，实时博弈将退化成“PPT 幻灯片”游戏。
- **经济崩溃**：若 Gas 费高昂，单次出牌成本几美分，一局对局累积的手续费将远超博弈筹码，导致游戏完全失去经济可行性。

### 3. Monad 的强力赋能

- **极速反馈**：亚秒级最终性确认（$< 1\text{s}$）配合**会话密钥（Session Key）**，能让玩家在无钱包弹窗、零延迟感的情况下，获得逼近 Web2 的丝滑交互。
- **并行吞吐**：Monad 的并行执行允许**成千上万个游戏房间同时独立计算与扣减押金**，房间之间互不阻塞，且 Gas 费始终维持在微美分级。

### 4. 为什么必须上链？

因为游戏的核心是**真金白银的押金（Wager）博弈**。胜负结算直接决定了资金池（Total Pot）在链上的即时分配。这种涉及直接资产转移的判定，必须交由智能合约（如代码化裁判）无许可、防篡改地强行执行。如果放在 Web2 数据库，项目方将面临极大的“篡改数据/作弊”和“卷款跑路”的单点信任危机。

<aside>
💡

#### 会话密钥 (Session Key)是什么

> **Web3 的“免密支付” —— 让钱包在后台自动帮你签名，再也不用频繁弹窗。**
> 

### 💡 核心定义

基于**账户抽象 (AA)** 产生的临时私钥。
由你的主钱包授权生成，临时存在浏览器内存中。在**限定时间、限定场景、限定额度**内自动帮用户签发交易。

### 🏨 房卡比喻

- **主私钥 (MetaMask) = 你家的大门钥匙**
· 拥有最高控制权，丢了会倾家荡产，绝不能给别人。
- **会话密钥 (Session Key) = 酒店的临时房卡**
· 只能刷开特定的 302 房间。
· 24 小时后自动失效。
· 弄丢了也不影响你家里的财产安全。

### 🛡️ 三大安全防线 (链上硬约束)

主钱包在派发房卡时，会在链上写死以下规则，黑客拿到也无能为力：

1. **时间锁 (Expiry)**
超时自动作废：

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image%205.png)

1. **白名单 (Target)**
只能调用指定合约的指定函数（如 `play()`），**禁止**调用高危函数（如 `transfer()` / `approve()`）。
2. **额度限制 (Value Limit)**
限制该密钥能支配的最高 Gas 费或资产上限，防止资金损耗。
</aside>

# 直播分享Notes

### Web3生态

7.6回放-DevRel 的成长之路 —— 从 Builder 到生态连接者   https://x.com/LXDAO_Official/status/2074086824663023840?s=20 

xiaohai老师经验- https://www.xiaohai.xyz/#identity

1. 目前运营、BD需求较多，技术初级/实习岗位少

交易所岗位比项目方多

适合从web3生态开始，小白适合运营开始做起

（xiaohai对小白信心十足）

1. AI工具写码区别

Claude code - 适合小白，输入想法即有框架搭建，细节不如codex

Codex - 有更多细节，适合给指定任务

Gemeni - 适合做UI

在工作流中配合使用

1. 黑客松-创意和UI（第一视觉印象）很重要

要熬夜

1. 工作管理模式

每天例会

每周周会 - 总结上一周，展望下一周

月度会 - 任务目标完成情况

### **AI Agent - Payment**

#### 7.8 回放-**AI Agent 如何拥有支付能力？** https://x.com/LXDAO_Official/status/2074809051406581822

FluxA

身份 收付款 

安全 风控 如何让AI Agent在有限制的情况下更高效地去花钱

和visa合作 未来传统金融接轨

研究user case

identity- 平台Google连接一个区块链钱包

法币 稳定币 两条链路

strip

自动驾驶 对比

#### 7.9回放-**从「裸奔」到「受控」：AI Agent 高危案例解析与安全防线构建** https://x.com/LXDAO_Official/status/2075189102371352648

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image%206.png)

AI幻觉

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image%207.png)

AgentGuard

定期审查AI权限清单

**高风险实例**

- 过度授权
- Prompt注入
- 恶意Skills
- 系统漏洞、恶意安装包、AI幻觉等

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image%208.png)

![image.png](Week%201%EF%BD%9C%E9%98%B6%E6%AE%B5%E6%80%BB%E7%BB%93%20Build%20Log/image%209.png)