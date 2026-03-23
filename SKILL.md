---
name: personal-finance
description: |
  多账本日常记账工具 - 将支出记录保存到按年/月组织的 markdown 文件中，支持多账本隔离、分类管理和预算追踪。默认账本为"个人"，可创建自定义账本如"家庭"、"副业"、"旅行"等。当用户想要：记录今天/昨天的花销、查看本月支出汇总、设置或检查预算、管理支出分类、创建或切换账本时使用此技能。触发词包括："记账"、"花了"、"支出"、"预算"、"消费"、"买了"、"记一下"、"花销"、"开销"、"账本"、"spent"、"expense"、"budget"。注意：不要用于编写记账App代码、分析CSV/Excel账单数据、公司财务报表、股票投资等场景。
---

# Personal Finance Manager

A multi-ledger system for tracking expenses using markdown files, with ledger isolation, category management and budget tracking.

## Storage Structure

```
<finance_root>/
├── ledgers.json           # 账本索引和当前激活账本
├── personal/              # 默认账本 - 个人支出
│   ├── config.json
│   ├── 2026/
│   │   └── 03/
│   │       ├── 2026-03-23.md
│   │       └── summary.json
│   └── ...
├── household/             # 预设账本 - 家庭支出
│   ├── config.json
│   └── ...
├── side-business/         # 用户自定义账本 - 副业
│   ├── config.json
│   └── ...
└── ...
```

**Default `<finance_root>` priorities:**
1. `~/Library/Mobile Documents/com~apple~CloudDocs/记账/` (iCloud Drive - 推荐，支持多端同步)
2. `~/iCloud Drive/记账/` (iCloud 替代路径)
3. `~/finance/` (本地备份路径)

优先使用 iCloud 路径以便在 iPhone/iPad/Mac 间同步。如果路径不存在，按顺序尝试下一个。

### ledgers.json 格式

```json
{
  "current_ledger": "personal",
  "ledgers": {
    "personal": {
      "name": "个人",
      "description": "个人日常支出",
      "created_at": "2026-03-18",
      "is_default": true
    },
    "household": {
      "name": "家庭",
      "description": "家庭共同支出",
      "created_at": "2026-03-18",
      "keywords": ["家庭", "家里", "家用", "全家", "丈夫", "妻子", "孩子", "父母", "房贷"]
    },
    "side-business": {
      "name": "副业",
      "description": "副业相关支出",
      "created_at": "2026-03-23",
      "keywords": ["副业", "兼职", "生意"]
    }
  }
}
```

## Ledger Management

### 预设账本

系统预设两个账本：
- **personal (个人)**: 默认账本，未指定账本时使用
- **household (家庭)**: 家庭支出，通过关键词自动识别

### 创建新账本

用户说"创建一个旅行账本"或"新增副业账本"时：

1. 询问账本信息：
   - 账本 ID (URL-friendly, 如 `travel-2026`)
   - 显示名称 (如 "2026日本旅行")
   - 描述 (可选)
   - 识别关键词 (可选，用于自动识别)

2. 创建目录 `<finance_root>/<ledger_id>/`
3. 初始化 `config.json`
4. 更新 `ledgers.json`

### 切换账本

- "切换到家庭账本"
- "用旅行账本记一笔"
- "查看副业账本"

切换后，后续记录默认使用该账本，直到再次切换。

### 账本自动识别规则

记录时按以下优先级确定账本：

1. **显式指定**: "记到旅行账本"、"用副业记"
2. **关键词匹配**: 匹配 ledger 的 `keywords`
3. **当前激活账本**: 用户上次切换到的账本
4. **默认账本**: `personal`

## Config File

Each ledger has its own `config.json`:

```json
{
  "ledger_id": "personal",
  "ledger_name": "个人",
  "currency": "CNY",
  "currency_symbol": "¥",
  "locale": "zh-CN",
  "decimal_places": 2,
  "categories": ["餐饮", "交通", "购物", "娱乐", "居住", "医疗", "教育", "其他"],
  "budget": {
    "monthly_total": null,
    "by_category": {}
  },
  "created_at": "2026-03-18",
  "timezone": "Asia/Shanghai"
}
```

## Daily Expense File Format

File: `<ledger>/<year>/<month>/<YYYY-MM-DD>.md`

```markdown
# <YYYY-MM-DD> <weekday>

| 时间 | 分类 | 金额 | 备注 |
|------|------|------|------|
| 08:30 | 餐饮 | ¥15.50 | 早餐 |
| 12:15 | 餐饮 | ¥28.00 | 午饭-食堂 |
| 18:30 | 交通 | ¥4.00 | 地铁 |

---
**日合计**: ¥47.50
```

## Storage Path Resolution

每次使用前，按以下顺序确定 `<finance_root>`：

1. 检查 `~/Library/Mobile Documents/com~apple~CloudDocs/记账/` 是否存在
2. 检查 `~/iCloud Drive/记账/` 是否存在
3. 回退到 `~/finance/`

如果都不存在，**创建**第一个可用路径（优先 iCloud）。首次创建时告知用户存储位置。

## Core Operations

### 1. Ledger Management - 账本管理详细步骤

#### 列出所有账本: "查看所有账本"、"有哪些账本"

**操作步骤**:
1. 读取 `<finance_root>/ledgers.json`
2. 提取所有账本信息
3. 标注当前激活账本

**输出示例**:
```
当前账本: 个人 (personal)

全部账本:
  📗 [个人] - 个人日常支出
  📘 [家庭] - 家庭共同支出  (关键词: 家庭、家里、家用)
  📙 [副业] - 副业相关支出  (关键词: 副业、兼职、生意)
  📕 [旅行] - 2026日本旅行  (关键词: 旅行、酒店、机票)

提示: 说"切换到 [账本名称]"可切换当前账本
```

#### 切换账本: "切换到家庭账本"

**操作步骤**:
1. 读取 `ledgers.json`
2. 验证账本是否存在
3. 更新 `current_ledger` 字段
4. 保存文件

**响应**:
```
✅ 已切换到 [家庭] 账本
后续记录将默认记入家庭支出
```

#### 创建账本: "创建一个宠物账本"

**步骤 1 - 收集信息**:
询问用户 (或从输入推断):
- 账本 ID: URL-friendly 字符串，如 `pet`
- 显示名称: 如 "宠物"
- 描述: (可选) "宠物相关支出"
- 识别关键词: (可选) ["宠物", "猫", "狗", "猫粮"]

**步骤 2 - 创建目录结构**:
```
<finance_root>/<ledger_id>/
├── config.json
└── (year directories will be created when recording)
```

**步骤 3 - 初始化 config.json**:
```json
{
  "ledger_id": "pet",
  "ledger_name": "宠物",
  "currency": "CNY",
  "currency_symbol": "¥",
  "locale": "zh-CN",
  "decimal_places": 2,
  "categories": ["食品", "医疗", "用品", "美容", "其他"],
  "budget": {
    "monthly_total": null,
    "by_category": {}
  },
  "created_at": "2026-03-23",
  "timezone": "Asia/Shanghai"
}
```

**步骤 4 - 更新 ledgers.json**:
```json
{
  "current_ledger": "pet",
  "ledgers": {
    "personal": { ... },
    "pet": {
      "name": "宠物",
      "description": "宠物相关支出",
      "created_at": "2026-03-23",
      "keywords": ["宠物", "猫", "狗", "猫粮"]
    }
  }
}
```

**响应**:
```
✅ 已创建新账本

📗 [宠物]
   ID: pet
   关键词: 宠物、猫、狗、猫粮

已自动切换到此账本
下次说"宠物猫粮50"即可自动识别
```

### 2. Record Expense - 记录操作详细步骤

**用户输入示例**: "午饭花了35" / "副业服务器花了200"

**执行步骤**:

1. **解析账本** - 按以下优先级确定账本：
   - 显式指定: "记到旅行账本"、"用副业记"
   - 关键词匹配: 检查 ledger keywords
   - 当前激活账本: 从 `ledgers.json` 的 `current_ledger` 读取
   - 默认账本: `personal`

2. **确定存储路径** - 按 Storage Path Resolution 规则找到 `<finance_root>/`

3. **解析支出信息**:
   - 金额: 提取数字 (支持 "35"、"35块"、"35.5"、"¥35")
   - 分类: 从关键词推断 (餐饮→餐饮, 地铁→交通, 咖啡→餐饮)
   - 时间: 未指定时省略或使用当前时间
   - 日期: 未指定时使用今天
   - 备注: 保留额外描述

4. **读取或创建文件**:
   - 文件路径: `<finance_root>/<ledger>/<year>/<month>/<YYYY-MM-DD>.md`
   - 如果目录不存在，创建目录
   - 如果文件不存在，创建文件头 (`# <YYYY-MM-DD> <weekday>` 和表头)

5. **追加记录**:
   - 在表格末尾添加一行: `| <时间> | <分类> | ¥<金额> | <备注> |`
   - 更新时间顺序 (可选)
   - 更新 **日合计**

6. **更新预算状态** (可选但推荐):
   - 读取该账本的 `config.json`
   - 计算本月该分类累计支出
   - 对比预算，生成状态提示

7. **响应用户**:
   ```
   已记录 [个人]: 餐饮 ¥35.00 (午饭)
   本月餐饮: ¥892/¥1500 (59%)
   ```

**账本识别示例:**
| 用户输入 | 识别结果 | 说明 |
|---------|---------|------|
| "午饭30" | 个人 | 默认账本 |
| "家里买菜150" | 家庭 | 匹配 household 关键词 |
| "副业服务器200" | 副业 | 匹配 side-business 关键词 |
| "记到旅行账本 机票3000" | 旅行 | 显式指定 |
| "用宠物账本记 猫粮89" | 宠物 | 显式指定 |

**错误处理**:
- 账本不存在 → 建议使用现有账本或创建新账本
- 分类无法识别 → 询问用户或使用"其他"
- 金额解析失败 → 要求重新输入
- 文件写入失败 → 报告错误并建议检查权限

### 3. View Expenses - 查询操作详细步骤

#### 查询当月汇总 (单账本)

**用户**: "这个月花了多少" / "查看本月支出"

**操作步骤**:
1. 确定要查询的账本 (当前激活账本或指定账本)
2. 找到该账本的 `YYYY/MM/` 目录 (Y=当年, M=当月)
3. 读取该目录下所有 `YYYY-MM-DD.md` 文件
4. 解析每个文件中的表格，累加金额
   - 提取每行的金额列 (第3列)
   - 按分类汇总
   - 计算总计
5. 对比 `config.json` 中的预算设置
6. 生成汇总报告

**输出格式**:
```
📊 个人 - 2026年3月支出汇总

| 分类 | 金额 | 预算 | 进度 |
|------|------|------|------|
| 餐饮 | ¥1,245.50 | ¥1,500 | 83% |
| 交通 | ¥380.00 | ¥500 | 76% |
| 购物 | ¥2,100.00 | ¥1,000 | ⚠️ 210% |

**月合计**: ¥5,832.50 / ¥8,000 (73%)
剩余天数: 8天 | 日均可用: ¥270.94
```

#### 跨账本查询: "查看全部支出"

**操作步骤**:
1. 读取 `ledgers.json` 获取所有账本列表
2. 对每个账本执行当月汇总查询 (同上)
3. 累加所有账本的合计
4. 生成跨账本报告

**输出格式**:
```
=== 2026年3月 - 全部账本汇总 ===

[个人] 本月合计: ¥3,245.00
  - 餐饮: ¥1,200
  - 交通: ¥500

[家庭] 本月合计: ¥5,600.00
  - 居住: ¥3,000
  - 餐饮: ¥1,500

─────────────────
总计: ¥8,845.00
```

#### 查询单日明细: "今天花了多少"

**操作步骤**:
1. 确定账本和日期
2. 读取 `YYYY/MM/YYYY-MM-DD.md` 文件
3. 直接显示文件内容 (markdown 表格)
4. 显示日合计

**输出格式**:
```
📅 个人 - 2026-03-23 (周一)

| 时间 | 分类 | 金额 | 备注 |
|------|------|------|------|
| 08:30 | 餐饮 | ¥15.50 | 早餐 |
| 12:15 | 餐饮 | ¥28.00 | 午饭-食堂 |

**日合计**: ¥43.50
```

### 4. Budget Management

预算是 per-ledger 的，每个账本独立设置。

**设置预算**:
- "设置月预算8000" → 当前账本
- "设置家庭月预算10000" → 指定 household 账本
- "副业预算5000" → 指定 side-business 账本

## Example Interactions

**账本管理**:
- "有哪些账本" → 列出所有账本，标注当前激活账本
- "切换到家庭账本" → 切换当前账本
- "创建一个旅行账本" → 引导创建新账本
- "旅游花了5000" → 自动识别或询问使用哪个账本

**Recording**:
- "记账 午饭 32" → 记录到当前激活账本
- "副业域名续费80" → 记录到副业账本（关键词识别）
- "记到旅行账本 酒店2000" → 显式指定账本
- "家庭聚餐500" → 记录到家庭账本

**Viewing**:
- "今天花了多少" → 当前账本今日支出
- "查看全部支出" → 所有账本汇总
- "副业这个月花了多少" → 指定账本查询
- "对比各账本支出" → 多账本对比

**Budget**:
- "设置预算" → 当前账本预算设置
- "家庭预算还剩多少" → 指定账本预算
- "副业餐饮预算2000" → 指定账本分类预算

## Ledger Keywords Reference

**默认预设**:
- `personal`: 无特殊关键词（作为兜底）
- `household`: 家庭、家里、家用、全家、丈夫、老公、妻子、老婆、孩子、娃、长辈、父母、房贷、房租

**常见自定义账本关键词建议**:
- `travel`: 旅行、旅游、酒店、机票、门票、签证
- `side-business`: 副业、兼职、生意、客户、项目
- `pet`: 宠物、猫、狗、猫粮、狗粮、兽医
- `car`: 车、汽车、加油、保养、保险、停车
- `baby`: 宝宝、婴儿、奶粉、尿布、育儿

## Migration from Old Structure

旧版本使用 `personal/` 和 `household/` 直接放在根目录。迁移步骤：

1. 创建 `ledgers.json` 索引文件
2. 如果原 `personal/` 和 `household/` 存在，自动注册到索引
3. 新创建的账本使用新结构

## Localization

To switch locale, update config.json in each ledger. The skill adapts:
- Currency symbol and formatting (¥1,234.56 vs $1,234.56)
- Category names
- Column headers and labels
- Date format (2026年3月18日 vs March 18, 2026)
