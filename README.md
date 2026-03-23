# Personal Finance Skill for Claude Code

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Compatible-blue)](https://claude.ai/code)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

A powerful multi-ledger expense tracking skill for Claude Code. Track personal, household, and custom ledger expenses with budget management and iCloud sync.

## ✨ Features

- 📝 **Multi-Ledger Support** - Personal, household, and unlimited custom ledgers (travel, side business, pet, etc.)
- 🔍 **Smart Recognition** - Auto-detects ledger from keywords (e.g., "family dinner" → household ledger)
- 💰 **Budget Tracking** - Set monthly total and per-category budgets with real-time progress
- 📊 **Rich Summaries** - Daily details, monthly summaries, cross-ledger reports
- ☁️ **iCloud Sync** - Native iCloud Drive support for iPhone/iPad/Mac access
- 🌍 **Bilingual** - Full Chinese and English support

## 🚀 Quick Start

### Installation

```bash
# Download from GitHub
git clone https://github.com/nission/personal-finance.git

# Install in Claude Code
claude skills install personal-finance/personal-finance.skill
```

### Record Expense

```
You: Lunch cost me 35
Claude: ✅ Recorded [Personal]: Food ¥35.00 (Lunch)

You: Paid 200 for home electricity
Claude: ✅ Recorded [Household]: Housing ¥200.00 (Electricity)

You: Flight to Japan 3500
Claude: ✅ Recorded [Travel]: Transport ¥3,500.00
```

### View Summaries

```
You: How much did I spend this month?
Claude: 📊 Personal - March 2026 Summary
       ┌──────────┬────────┬────────┬──────┐
       │ Category │ Amount │ Budget │  %   │
       ├──────────┼────────┼────────┼──────┤
       │ Food     │ ¥245   │ ¥500   │ 49%  │
       │ Transport│ ¥120   │ ¥200   │ 60%  │
       │ Shopping │ ¥800   │ ¥1000  │ 80%  │
       └──────────┴────────┴────────┴──────┘
       **Total**: ¥1,165 / ¥2,000 (58%)
```

## 📁 Data Storage

Files are stored in iCloud Drive (auto-sync across devices):

```
iCloud Drive/记账/
├── ledgers.json          # Ledger index
├── personal/
│   ├── config.json       # Budget settings
│   └── 2026/03/2026-03-23.md  # Daily records
├── household/
└── travel/
```

Access your data directly in iPhone/iPad Files app!

## 🎯 Trigger Phrases

- **Record**: "spent", "cost", "expense", "记账", "花了"
- **Query**: "how much", "summary", "支出", "花了多少"
- **Budget**: "budget", "set budget", "预算"
- **Ledger**: "ledger", "switch to", "账本"

## 🛠️ Create Custom Ledger

```
You: Create a pet ledger
Claude: Created [Pet] ledger with categories:
       - Food, Medical, Supplies, Grooming

You: Cat food 89
Claude: ✅ Recorded [Pet]: Food ¥89.00
```

## 📝 Budget Configuration

Edit `config.json` in any ledger:

```json
{
  "currency": "CNY",
  "categories": ["Food", "Transport", "Shopping", "Other"],
  "budget": {
    "monthly_total": 5000,
    "by_category": {
      "Food": 1500,
      "Transport": 800
    }
  }
}
```

## 📸 Screenshots

### Recording Expense
```
已记录 [个人]: 餐饮 ¥35.00 (午饭)
本月餐饮: ¥892/¥1500 (59%)
```

### Monthly Summary
```
📊 个人 - 2026年3月支出汇总

| 分类 | 金额 | 预算 | 进度 |
|------|------|------|------|
| 餐饮 | ¥1,245 | ¥1,500 | 83% |
| 交通 | ¥380   | ¥500   | 76% |

**月合计**: ¥5,832 / ¥8,000 (73%)
剩余天数: 8天 | 日均可用: ¥270
```

## 🔧 Advanced Usage

### Keyword Recognition

The skill automatically detects ledgers:

| Input | Detected Ledger | Why |
|-------|-----------------|-----|
| "Lunch 35" | Personal | Default |
| "Family dinner 200" | Household | "Family" keyword |
| "Hotel 500" | Travel | Travel context |

### Cross-Ledger Summary

```
You: Show all expenses
Claude: === March 2026 - All Ledgers ===

[Personal] Total: ¥3,245
[Household] Total: ¥5,600
[Travel] Total: ¥8,200
─────────────────────────
Grand Total: ¥17,045
```

## 🤝 Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## 📄 License

MIT License - feel free to use and modify!

## 🙏 Credits

Created with ❤️ using [Claude Code](https://claude.ai/code)

---

⭐️ Star this repo if you find it useful!
