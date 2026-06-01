# pricing-analysis — Claude Code Skill

讓 Claude Code 幫你系統性分析 App 各市場訂閱定價，輸出互動式 HTML 報告。

輸入競品定價表（含 MAU），輸出含四分位分析、定價評分（M1/M2/M4/M5）、GDP 購買力圖表的完整報告，適合 PM 快速整理後直接交給主管決策。

---

## AI 一鍵安裝

**前置條件**：已安裝 [Claude Code](https://claude.ai/code)

**步驟一**：Clone 此 repo

```bash
git clone https://github.com/poyu9090/pricing-analysis.git
```

**步驟二**：把以下 prompt 整段貼給 Claude Code，它會完成所有設定：

```
請幫我安裝 pricing-analysis skill，repo 路徑在 ~/Desktop/pricing-analysis-skill（如果路徑不同請告訴我）。

執行以下步驟：
1. 把 SKILL.md 複製到 ~/.claude/skills/pricing-analysis/SKILL.md
2. 把 evals/evals.json 複製到 ~/.claude/skills/pricing-analysis/evals/evals.json
3. 問我：工作目錄要放在哪裡？（預設 ~/Desktop/Claude/，直接回 Enter 使用預設值）
4. 在工作目錄內建立 pricing-data/ 子資料夾
5. 把 data/market-reference.json 複製到 {工作目錄}/pricing-data/
6. 把 templates/BeautyCam_UK_pricing_20260526.html 複製到 {工作目錄}/
7. 修改 ~/.claude/skills/pricing-analysis/SKILL.md 的 ⚙️ 路徑配置區塊，
   把 ~/Desktop/Claude/ 替換成步驟 3 確認的工作目錄路徑
8. 確認 SKILL.md 沒有殘留舊路徑，輸出安裝完成摘要
```

---

## 手動安裝

```bash
WORKDIR=~/Desktop/Claude   # 改成你想要的工作目錄

# 1. 安裝 Skill
mkdir -p ~/.claude/skills/pricing-analysis/evals
cp SKILL.md ~/.claude/skills/pricing-analysis/
cp evals/evals.json ~/.claude/skills/pricing-analysis/evals/

# 2. 複製資料與模板
mkdir -p $WORKDIR/pricing-data
cp data/market-reference.json $WORKDIR/pricing-data/
cp templates/BeautyCam_UK_pricing_20260526.html $WORKDIR/

# 3. 更新 SKILL.md 路徑
sed -i '' "s|~/Desktop/Claude/|$WORKDIR/|g" ~/.claude/skills/pricing-analysis/SKILL.md
```

---

## 使用方式

安裝完成後，在 Claude Code 輸入任一觸發詞：

```
幫我跑 BeautyPlus 英國年月定價分析
https://docs.google.com/spreadsheets/d/.../edit?gid=1859198647
```

或直接說：

```
幫我做定價分析 / pricing analysis / 訂閱價格合理嗎
```

Claude 會自動觸發 skill，依序執行資料解析 → GDP 確認 → 評分計算 → 輸出 HTML 報告。

---

## 輸出報告包含

| 區塊 | 說明 |
|------|------|
| **定價決策評分** | M1 競品定位缺口 × M2 市場類型 × M4 MAU 定價一致性 × M5 集團關係，0–100 分，≥70 調漲 / 40–69 維持 / <40 調降 |
| **互動式定價橫條圖** | 年/月切換、含/不含 CapCut 切換、簡單/MAU 加權四分位切換 |
| **MAU vs 定價象限圖** | 對數 Y 軸，四象限：高滲透低定價 / 品質溢價 / 市場摸索 / 利基高端 |
| **折扣率長條圖** | 年 vs 月省多少，標示自家與集團產品 |
| **GDP 購買力雙圖** | 名目 GDP 排名 + Big Mac Index 比較 |
| **完整競品定價表** | 含無年方案的 app，附 USD 換算 |

---

## 檔案結構

```
pricing-analysis-skill/
├── README.md
├── SKILL.md                    ← Claude Code skill 定義（安裝到 ~/.claude/skills/）
├── evals/
│   └── evals.json             ← 3 個測試案例（台灣 / 日本 / 跨市場）
├── data/
│   └── market-reference.json  ← 市場 GDP / Big Mac 快取（含美國、英國、日本等 14 個市場）
└── templates/
    └── BeautyCam_UK_pricing_20260526.html  ← HTML 報告格式基準模板（不要修改）
```

---

## 更新市場資料

`data/market-reference.json` 是 GDP 和 Big Mac Index 的本地快取，避免每次分析都重新搜尋。

- 分析新市場時，skill 會自動 WebSearch 並補充至你本機的 `pricing-data/market-reference.json`
- 歡迎 PR 把新市場資料貢獻回此 repo 的 `data/market-reference.json`，讓社群共用

資料來源：IMF WEO 2026（名目 GDP）、World Bank 2024（PPP）、The Economist Big Mac Index 2026

---

## 適用產品

BeautyPlus · BeautyCam · Meitu · Wink · 其他訂閱制 App（需調整 SKILL.md 的品牌名稱說明）
