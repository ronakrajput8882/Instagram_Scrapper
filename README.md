# 📸 Instagram Scrapper — n8n Automation

An **n8n workflow** that scrapes Instagram profile data by account type and location using the **Apify Google Search Scraper** API, then saves results directly into **Airtable**.

---

## 🔄 Workflow Overview

```
[Form Trigger] → [Apify Search] → [Wait 30s] → [Parse JS] → [Airtable]
```

| Step | Node | What it does |
|---|---|---|
| 1 | **Form Trigger** | User enters Account Type (e.g. Doctor) + Location (e.g. Mumbai) |
| 2 | **HTTP Request** | Calls Apify's Google Search Scraper: `site:instagram.com <type> <location>` |
| 3 | **Wait 30s** | Waits for Apify to finish scraping (up to 3 pages × 100 results) |
| 4 | **Parse Results** | Extracts Name, Handle, URL, Followers (K/M/B), Description, PostType, Position |
| 5 | **Save to Airtable** | Creates one record per profile in your Airtable base |

---

## 📦 Data Collected per Profile

| Field | Example |
|---|---|
| `Name` | Dr. Rohan Mehta |
| `InstagramHandle` | @drrohanmehta |
| `ProfileURL` | https://instagram.com/drrohanmehta |
| `Description` | Cardiologist · Mumbai · 50K followers |
| `Followers` | 50000 |
| `ChannelName` | drrohanmehta |
| `PostType` | organic |
| `Position` | 3 |

---

## ⚙️ Setup Guide

### Prerequisites

- [n8n](https://n8n.io) (self-hosted or cloud)
- [Apify](https://apify.com) account (free tier works)
- [Airtable](https://airtable.com) account with a base ready

---

### Step 1 — Get your Apify API key

1. Sign up at [apify.com](https://apify.com)
2. Go to **Settings → Integrations → API token**
3. Copy your token

---

### Step 2 — Set up Airtable

Create a base with a table containing these columns:

| Column Name | Type |
|---|---|
| Name | Single line text |
| InstagramHandle | Single line text |
| ProfileURL | URL |
| Description | Long text |
| Followers | Number |
| ChannelName | Single line text |
| PostType | Single line text |
| Position | Number |

---

### Step 3 — Import the workflow into n8n

1. Open your n8n instance
2. Click **+** → **Import from file**
3. Upload `instagram-scrapper.json`

---

### Step 4 — Add credentials

#### Apify (HTTP Request node)
1. Click the **HTTP Request** node
2. Under Authentication, add a **Header Auth** credential:
   - Name: `Authorization`
   - Value: `Bearer YOUR_APIFY_TOKEN`

#### Airtable (Save to Airtable node)
1. Click the **Save to Airtable** node
2. Add a new **Airtable Personal Access Token** credential
3. Paste your token from [airtable.com/create/tokens](https://airtable.com/create/tokens)
4. Update the **Base ID** and **Table ID** to match your Airtable

---

### Step 5 — Activate & run

1. Click **Activate** (toggle top-right) to enable the form endpoint
2. Open the form URL shown in the Form Trigger node
3. Fill in **Account Type** and **Location** → Submit
4. Watch results flow into Airtable 🎉

---

## 🔒 Security Notes

> **Never commit API keys or tokens to GitHub.**

- All credentials are stored in n8n's encrypted credential store — not in the JSON file
- The `instagram-scrapper.json` in this repo contains **no secrets** — safe to share publicly
- Add `.env` and any `*token*` files to `.gitignore` (already done)

---

## 🗺️ Workflow Diagram

```
┌─────────────────────┐
│    Form Trigger     │  ← User fills: Account Type + Location
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│    HTTP Request     │  ← POST to Apify Google Search Scraper
│  (Apify API call)   │     Query: site:instagram.com <type> <location>
└────────┬────────────┘     Up to 300 results (3 pages × 100)
         │
         ▼
┌─────────────────────┐
│     Wait 30s        │  ← Let Apify finish processing
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  Parse Results (JS) │  ← Extract & clean all profile fields
│                     │     Parse 22.7K → 22700, filter non-IG URLs
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│   Save to Airtable  │  ← One record per Instagram profile
└─────────────────────┘
```

---

## 🐛 Troubleshooting

| Problem | Fix |
|---|---|
| No results returned | Try broader Account Type (e.g. "Doctor" not "Pediatric Cardiologist") |
| Apify 401 error | Check your API token is correct and active |
| Airtable write fails | Verify column names match exactly (case-sensitive) |
| Too few results | Increase `maxPagesPerQuery` in the HTTP Request node (max 10) |
| Wait too short/long | Adjust the Wait node duration based on your query size |

---

## 📁 Repository Structure

```
Instagram-Scrapper/
│
├── instagram-scrapper.json   # n8n workflow (no secrets inside)
├── Sheet-link.txt            # Reference link to sample output sheet
├── .gitignore                # Prevents accidental credential commits
└── README.md                 # This file
```

---

## 🤝 Contributing

Pull requests are welcome! Ideas for improvement:
- Add a **Google Sheets** output node as an alternative to Airtable
- Add a **filter node** to skip profiles below a follower threshold
- Add **email notification** when scrape completes

---

## 📄 License

MIT License — free to use and modify.

---

## 👤 Author

**Ronaksinh Rajput**
- GitHub: [@ronakrajput8882](https://github.com/ronakrajput8882)

---

> ⭐ Star this repo if it saved you time!
