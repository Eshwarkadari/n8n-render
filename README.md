# 📌 Pinterest → Instagram Auto-Poster on Render

> **Self-hosted n8n** running on Render.com — posts 3 Pinterest images to Instagram daily, fully automated. No coding needed after setup.

![n8n](https://img.shields.io/badge/n8n-EA4B71?style=for-the-badge&logo=n8n&logoColor=white)
![Instagram](https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)
![Telegram](https://img.shields.io/badge/Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)
![Render](https://img.shields.io/badge/Render-46E3B7?style=for-the-badge&logo=render&logoColor=black)

---

## ✨ What This Does

| Feature | Detail |
|---|---|
| 📥 **Queue** | Paste any Pinterest URL via webhook → stored in Google Sheets |
| 🔁 **Auto-post** | 3 posts/day at 8:30 AM, 12:30 PM, 7:00 PM IST |
| ✅ **Never repeat** | Posted URLs marked `POSTED` — never reposted |
| ❌ **Error handling** | Failed posts marked `FAILED` + Telegram alert |
| 📲 **Notifications** | Telegram message on every success & failure |
| 🔍 **Dedup** | Duplicate URLs rejected at entry |

---

## 📁 Repository Structure

```
n8n-render/
├── render.yaml                          ← Render deployment config (auto-deploys n8n)
├── workflows/
│   ├── workflow1_add_urls.json          ← Import into n8n: Add Pinterest URLs
│   └── workflow2_daily_post.json        ← Import into n8n: Daily auto-poster
└── README.md
```

---

## 🗂️ Google Sheet Setup

**Sheet ID:** `15jDXVQTA7mjc9vWkF134EGWGvmvdNrzchNsjdSbN960`

Your sheet needs exactly **2 columns** in Row 1:

| A | B |
|---|---|
| `Pinterest_URL` | `Status` |

Status values (managed automatically):
- `PENDING` → waiting to post
- `POSTED` → done, never posted again
- `FAILED` → error, change back to `PENDING` to retry

---

## 🚀 Deploy n8n to Render (One-Time)

### Step 1 — Fork & Deploy

1. **Fork** this repo to your GitHub
2. Go to [render.com](https://render.com) → **New** → **Blueprint**
3. Connect your forked repo
4. Render reads `render.yaml` and automatically creates:
   - n8n web service
   - PostgreSQL database
5. Click **Apply** → wait ~3 minutes → n8n is live!

### Step 2 — First Login

1. Open your Render URL: `https://n8n-service.onrender.com`
2. Create your n8n admin account (email + password)

### Step 3 — Add Google Sheets Credential

1. n8n → **Credentials** → **New** → **Google Sheets OAuth2 API**
2. Authorize with your Google account
3. Save and note the Credential ID

### Step 4 — Add Telegram Bot Credential

1. Message [@BotFather](https://t.me/BotFather) → `/newbot` → get your token
2. n8n → **Credentials** → **New** → **Telegram API** → paste token
3. Save

### Step 5 — Import Workflows

1. n8n → **Workflows** → **Import from File**
2. Import `workflows/workflow1_add_urls.json`
3. Import `workflows/workflow2_daily_post.json`
4. In **each workflow**, open the Google Sheets nodes → connect your credential
5. In Workflow 2, open the Telegram nodes → connect your Telegram credential
6. **Activate both workflows** (toggle ON)

### Step 6 — Set Webhook URL in Render

After deploy, your n8n URL will be something like `https://n8n-service.onrender.com`.

In Render dashboard → n8n service → **Environment** → update:
```
WEBHOOK_URL = https://YOUR-ACTUAL-URL.onrender.com
N8N_EDITOR_BASE_URL = https://YOUR-ACTUAL-URL.onrender.com
```

---

## 📬 Adding Pinterest URLs to Queue

Once Workflow 1 is active, copy its **Webhook URL** from n8n and POST to it:

```bash
curl -X POST "https://n8n-service.onrender.com/webhook/add-pinterest-url" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://www.pinterest.com/pin/123456789/"}'
```

**Success response (201):**
```json
{ "success": true, "message": "Added to queue", "url": "https://..." }
```

**Duplicate response (409):**
```json
{ "success": false, "duplicate": true, "message": "URL already in queue" }
```

**Accepted URL formats:**
- `https://pinterest.com/pin/123456789/`
- `https://www.pinterest.com/pin/123456789/`
- `https://pin.it/AbCdE123`

---

## ⏰ Posting Schedule (IST)

| Run | IST Time | UTC (cron) |
|---|---|---|
| Morning | 8:30 AM | `0 3 * * *` |
| Afternoon | 12:30 PM | `0 7 * * *` |
| Evening | 7:00 PM | `30 13 * * *` |

Each run posts **1 URL** → **3 posts/day** total.

---

## 📲 Telegram Notifications

**On success:**
```
✅ Posted to Instagram!
📌 Pinterest: https://pinterest.com/pin/...
📸 Instagram Post ID: 123456789
🕐 2024-01-15 08:30:22
```

**On failure:**
```
❌ Posting FAILED
🔴 Node: HTTP: IG Create Container
💬 Error: Invalid image URL
🕐 2024-01-15 08:30:22
To retry: change row Status back to PENDING in Google Sheet.
```

---

## 🔄 Retrying Failed Posts

1. Open your Google Sheet
2. Find the row with `Status = FAILED`
3. Change it to `PENDING`
4. Will be picked up at the next scheduled run

---

## ⚠️ Instagram Token Refresh

Your Instagram token expires every **60 days**. Refresh it here:

```
https://graph.facebook.com/v19.0/oauth/access_token
  ?grant_type=fb_exchange_token
  &client_id=YOUR_APP_ID
  &client_secret=YOUR_APP_SECRET
  &fb_exchange_token=CURRENT_TOKEN
```

Then update `INSTAGRAM_ACCESS_TOKEN` in Render → Environment Variables.

---

## 🛡️ Keep n8n Awake (Free Tier)

Render free tier sleeps after 15 min of inactivity. Set up a free ping:

1. Go to [uptimerobot.com](https://uptimerobot.com) (free)
2. Add monitor → HTTP → `https://n8n-service.onrender.com/healthz`
3. Interval: every 5 minutes

This keeps n8n awake so cron jobs fire on time.

---

## 👨‍💻 Author

**Kadari Eshwar** — [@Eshwarkadari](https://github.com/Eshwarkadari)

Instagram: [@styleformenindia](https://instagram.com/styleformenindia)
