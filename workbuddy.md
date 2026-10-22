# McSplit 项目对话导出

> 导出时间：2026-10-22 21:28 · 工作区：D:\Users\T\Desktop\McSplit

---

## 1. 将《McSplit 麦麦拼单精算师.md》设为项目 Skill

**用户**：将这个作为你这个项目的项目 skill

**WorkBuddy**：
- 读取原文件（五步拼单工作流：收集信息 → 确认订单 → 实时询价 → 优惠券规划 → AA 账单）
- 创建项目级 Skill：`D:\Users\T\Desktop\McSplit\.workbuddy\skills\mcsplit\SKILL.md`
- 补充 frontmatter（name/description，触发词：拼单、麦当劳拼单、AA 账单、拼单点餐），流程原文保留
- 提示：STEP 3/4 依赖麦当劳 MCP 工具，当时未见对应 connector

---

## 2. 发起 4 人拼单

**用户**：
> 帮我组织拼单，门店在上海市静安区南京西路1号，外送。
> 参与者：
> - 小明：麦辣鸡腿堡套餐（可乐换无糖）
> - 小红：1+1随心配，汉堡选双层吉士堡，饮料选橙汁中杯
> - 小李：大薯条×2
> - 我自己：麦辣鸡腿堡单品 + 麦咖啡美式中杯不加糖

**WorkBuddy**：
- 输出订单确认表（5 项餐品，标注 ❓：1+1随心配饮料规格）
- 发现阻塞：环境未安装麦当劳 MCP connector，插件市场无候选，按 skill 规则禁止凭印象报价
- 给出两条路径：A. 提供 MCP；B. 手动报价模式

---

## 3. 排查 mcd-mcp 可用性

**用户**：mcd-mcp，能调用吗

**WorkBuddy**：
- 发现 `~/.workbuddy/mcp.json` 已配置 mcd-mcp（`https://mcp.mcd.cn`，含 Bearer Token），但会话未加载其工具
- 引导用户：在 connector 管理页面右上角自定义入口对该 server 点「信任」启用

**用户**：启用这个mcp

**WorkBuddy**：通过工具检索确认 mcd-mcp 工具已上线（delivery-query-stores、query-nearby-stores、query-meal-assistance、query-meals、query-meal-detail、query-store-coupons、available-coupons、query-my-coupons、calculate-price、create-order 等）

---

## 4. 地址与门店

**WorkBuddy**：查询已存地址，仅有「黄浦区南京西路479弄17号」，与目标不符；MCP 无地址搜索工具，仅有创建地址接口，需联系人+手机号

**用户**：张三，12344558877

**WorkBuddy**：
- 创建地址成功（addressId: 1036488160167140201893479073）
- 查到 3 家可配送门店：

| # | 门店 | storeCode | 营业时间 |
|---|---|---|---|
| 1 | 麦当劳上海吴江路餐厅（推荐，最近） | 1450118 | 06:30–22:00 |
| 2 | 麦当劳上海静安锦沧文华餐厅 | 1450403 | 07:00–22:00 |
| 3 | 麦当劳上海西康路冠生园餐厅 | 1450422 | 06:00–23:00 |

**用户**：继续（确认 1 号吴江路餐厅）

---

## 5. 实时询价与券扫描

**WorkBuddy**（并行调用 query-meals / query-meal-detail×3 / query-store-coupons / available-coupons / query-my-coupons）：

- ✅ 小明：麦辣鸡腿汉堡中套餐（9900002995）¥41，饮料可换无糖可乐 +¥0
- ❓ 小红：外送菜单无「1+1随心配」→ 候选 A 拆单品 ¥39 / B 双层吉士套餐 ¥40.5
- ❓ 小李：外送仅有「薯条中」¥15.5，无大薯单品
- ✅ 先生：麦辣堡单品 ¥25.5 + 热美式中杯促销价 ¥11.9（原 ¥19，不加糖默认）
- 券扫描：「11.9元麦辣鸡腿堡」可用（省 ¥13.6）；「19.9王牌炸鸡任选2」无对应餐品；「免费脆薯饼」该店外送不可用

**用户**：对（接受默认：小红拆单品、小李薯条中×2）

---

## 6. 算价与 AA 账单

**WorkBuddy**：
- 首次 calculate-price 报错 `600012 促销规则不支持该商品`
- 定位：用券商品 productCode 须用券面编码 `1440`（而非菜单码 `901440`），修正后成功
- 返回：商品现价 ¥134.8、配送费 ¥6、打包费 ¥3（原 ¥5.7）、总计 **¥143.8**（原 ¥167.2，省 ¥23.4）

最终账单：

| 参与者 | 应付 | 备注 |
|---|---|---|
| 小明 | 43.3 | 餐费41 + 均摊2.3 |
| 小红 | 41.3 | 餐费39 + 均摊2.3 |
| 小李 | 33.3 | 餐费31 + 均摊2.3 |
| 先生 | 25.9 | 餐费23.8 + 均摊2.3 - 兜底0.2 |

人均省 ¥5.9。状态：待用户回复「创建订单」。

---

## 7. 生成参赛项目文件

**用户**：根据刚才的测试，生成参赛项目文件（README.md / MCP_INTEGRATION.md / mcp-config.example.json / CONTEST_DECLARATION.md，声明部分一字不改）

**WorkBuddy**：在 `D:\Users\T\Desktop\McSplit\` 生成四件套：

| 文件 | 内容 |
|---|---|
| `README.md` | 项目介绍、核心功能、快速开始、真实对话示例、ASCII 架构图、目标用户、免责声明 |
| `MCP_INTEGRATION.md` | Server 信息、11 个工具「名称+调用时机+业务价值」表、Mermaid 流程图、踩坑记录 |
| `mcp-config.example.json` | Token 使用 `${MCD_MCP_TOKEN}` 占位符 |
| `CONTEST_DECLARATION.md` | 参赛声明，按用户原文照抄 |

项目定位：麦当劳程序员节创意开发大赛参赛作品。

---

## 8. 对话导出

**用户**：将我和你的对话导出，并创建 workbuddy.md 保存

**WorkBuddy**：生成本文件 → `D:\Users\T\Desktop\McSplit\workbuddy.md`
