# Finance Planner

A personal finance planning tool for Thai salary earners. Plan your DCA investments, track monthly expenses, and visualize long-term wealth growth — all in a single offline-capable HTML file.

**Live Demo:** `[your-vercel-url]` *(update after deploying)*

---

## Features

### Interactive Planner
- **Salary setup** — enter gross salary and PVD contribution % (2–15%), take-home calculated automatically
- **6 adjustable sliders** — weekly living budget, commute, family obligations, personal money, DCA amount, and auto-calculated buffer
- **Custom fixed costs** — add/remove recurring expenses (e.g. dentist, gym, insurance) with bilingual names; each item shows % of gross salary
- **Percentage pills** — every line item shows its % of gross salary, color-coded by category
- **Live donut chart** — allocation across PVD, DCA, expenses, personal, and buffer updates on every change
- **DCA growth projection table** — estimated portfolio value at years 1, 2, 3, 5, 10, 15, 20, 30 at 7% annual return

### Recommendation Tab
- **3 key numbers** — DCA, personal money, and buffer displayed prominently with health assessment
- **Full monthly breakdown** — step-by-step ledger from gross salary to remaining buffer, each line with % pill
- **Stacked allocation bar** — visual proportion of every spending category
- **Why these numbers** — shows DCA %, personal %, and savings rate against take-home
- **DCA guide** — 4 tiers (Too Low / Balanced / Aggressive / Too Much) with "YOU ARE HERE" indicator, live example amounts, and 10-year portfolio estimates based on your actual salary
- **Key milestones** — projected portfolio at year 1, 3, 5, and 10 based on your DCA amount
- **5 rules** — personalized with your live numbers

### Fund Recommendations
| Fund | Exposure |
|---|---|
| SCBDOW | S&P 500 / Dow Jones (US) |
| TMBAGLF | Global index — US, Europe, Asia |
| KFGBRANRMF | Global index + RMF tax deduction |

### Export & Import
| Action | Format | Details |
|---|---|---|
| PNG | `.png` | Screenshots the active tab at 2× resolution |
| PDF | Browser print | All panels rendered, print-optimized CSS |
| Export | `.json` | Saves all slider values, custom costs, and language |
| Import | `.json` | Restores all settings including language |

### Languages
- **English** and **Thai (ภาษาไทย)** — toggle in the top bar
- All labels, descriptions, chart axes, toasts, and dynamic rule text switch instantly
- Language is saved and restored with JSON export/import

---

## Tech Stack

| Library | Version | Purpose |
|---|---|---|
| [Chart.js](https://www.chartjs.org/) | latest | Donut and line charts |
| [html2canvas](https://html2canvas.hertzen.com/) | 1.4.1 | PNG export |

No frameworks. No build step. No Node.js required. Everything is a single HTML file.

---

## Getting Started

### Run locally

```bash
# Clone the repo
git clone https://github.com/your-username/finance-planner.git
cd finance-planner

# Just open the file — no server needed
open index.html         # macOS
xdg-open index.html     # Linux
start index.html        # Windows
```

Or drag `index.html` into any browser.

---

## Deploy to Vercel

### Option A — Vercel CLI (fastest)

```bash
# Install CLI
npm install -g vercel

# Deploy from the project folder
cd finance-planner
vercel
```

Follow the prompts. You'll get a live URL in under a minute.

### Option B — GitHub + Vercel (recommended for ongoing updates)

```bash
# Initialize git and push to GitHub
git init
git add .
git commit -m "Initial deploy"
gh repo create finance-planner --public --push --source=.
```

Then go to [vercel.com/new](https://vercel.com/new), import the GitHub repo, and click **Deploy**.

Every `git push` will auto-deploy going forward.

### Option C — Drag and drop

Go to [vercel.com/new](https://vercel.com/new) and drag the `finance-planner` folder onto the page.

---

## How to Use

1. **Set your salary** — enter gross salary and PVD percentage in the Salary Setup card
2. **Adjust sliders** — set your weekly budget, commute, family obligations, personal money, and DCA amount
3. **Add fixed costs** — click `+ Add cost item` to add recurring expenses like dentist, gym, or subscriptions
4. **Switch to Recommendation tab** — see your personalized plan, DCA tier, milestones, and 5 rules
5. **Export** — save as PNG to share, PDF to print, or JSON to reload later
6. **Import** — click Import to restore a previously saved plan

---

## File Structure

```
finance-planner/
├── index.html   — full app (planner + recommendation + export)
├── bugs.html    — bug audit report (Thai)
└── README.md    — this file
```

---

## Default Values

The planner ships with values based on a realistic Thai new-graduate scenario:

| Setting | Default | Notes |
|---|---|---|
| Gross salary | 56,500 THB | Entry-level Bangkok salary |
| PVD | 15% | Maximum employer-matched rate |
| Weekly living | 1,000 THB | Food + daily expenses |
| Commute | 2,500 THB | BTS/MRT estimate |
| Family help | 7,500 THB | Mid-range family support |
| Personal money | 10,000 THB | Lifestyle, social, subscriptions |
| DCA | 12,000 THB | 25% of take-home |
| Fixed costs | 500 THB | Dentist (example) |

All values are fully adjustable.

---

## License

MIT
