# n8n on Render ⚙️

A ready-to-deploy **n8n workflow automation** setup on [Render.com](https://render.com) using Docker — completely free tier compatible.

![n8n](https://img.shields.io/badge/n8n-EA4B71?style=for-the-badge&logo=n8n&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![Render](https://img.shields.io/badge/Render-46E3B7?style=for-the-badge&logo=render&logoColor=black)

---

## ✨ What is n8n?

n8n is a **free, open-source workflow automation tool** — like Zapier or Make, but self-hosted. You can connect 400+ apps and automate any workflow without coding.

---

## ✨ Features

- 🐳 **Docker-based** deployment — no manual setup
- 🗄️ **PostgreSQL database** for persistent workflow storage
- 🆓 **Free tier** on Render — no credit card needed
- 🔄 **Auto-restart** on crash
- 🌐 **Public URL** provided by Render automatically
- ⚡ Deploy in under 5 minutes

---

## 🚀 How to Deploy

### Option 1 — One Click Deploy
1. Fork this repo
2. Go to [render.com](https://render.com) → New → Blueprint
3. Connect your forked repo
4. Render reads `render.yaml` and sets everything up automatically

### Option 2 — Manual Deploy
1. Go to [render.com](https://render.com)
2. Create a new **Web Service**
3. Choose **Docker** runtime
4. Use image: `docker.io/n8nio/n8n:latest`
5. Add environment variables as needed

---

## ⚙️ render.yaml Overview

```yaml
services:
  - type: web
    plan: free
    runtime: image
    name: n8n-service
    image:
      url: docker.io/n8nio/n8n:latest
```

---

## 🔗 Useful Links

- [n8n Documentation](https://docs.n8n.io)
- [Render Documentation](https://render.com/docs)
- [n8n Docker Hub](https://hub.docker.com/r/n8nio/n8n)

---

## 👨‍💻 Author

**Kadari Eshwar** — [github.com/Eshwarkadari](https://github.com/Eshwarkadari)
