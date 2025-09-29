# 🚀 Woodpecker CI/CD – Local Pipeline Demo (GitHub + Docker + Ngrok)

This project demonstrates a **self-hosted CI/CD pipeline using Woodpecker CI**, connected to **GitHub** and running locally via **Docker Compose**.  
A simple **static HTML/CSS site** is used as the application being tested and packaged.

---

## ✅ Project Overview

| Component | Technology |
|-----------|-------------|
| CI Engine | Woodpecker CI (Server + Agent via Docker) |
| Repository | GitHub |
| Webhook Tunnel | Ngrok |
| Application | Static HTML/CSS Website |
| Trigger | `git push` |

When code is pushed to GitHub, Woodpecker automatically **clones the repository, validates files, builds output, runs a test, and packages the result**.

---

## 📂 Project Structure

woodpecker-static-demo/
├─ .env
├─ docker-compose.yml
├─ .woodpecker.yaml
└─ site/
├─ index.html
└─ styles.css

yaml


---

## 🔧 Setup Instructions

### 1️⃣ Start Ngrok Tunnel

```bash
ngrok http 8000
Copy the HTTPS URL → Use in .env and GitHub OAuth.

2️⃣ Configure .env
ini

WOODPECKER_HOST=https://<YOUR_NGROK_URL>
WOODPECKER_GITHUB_CLIENT=<OAUTH_CLIENT_ID>
WOODPECKER_GITHUB_SECRET=<OAUTH_CLIENT_SECRET>
WOODPECKER_AGENT_SECRET=change_me_demo_secret
3️⃣ Launch Woodpecker (Server + Agent)
bash

docker compose up -d
Then open:

cpp

https://<YOUR_NGROK_URL>
Login via GitHub OAuth and enable the repository.

4️⃣ Push Code & Trigger Build
bash

git add .
git commit -m "Trigger CI"
git push
Woodpecker will automatically run the pipeline.

✅ CI Pipeline Configuration (.woodpecker.yaml)
yaml

kind: pipeline
type: docker
name: default

steps:
  - name: validate
    image: alpine:3.20
    commands:
      - echo "Validating repo contents..."
      - ls -lah
      - test -f site/index.html
      - test -f site/styles.css

  - name: build
    image: node:20-alpine
    commands:
      - sh -c "echo Pretend build: copy static files to dist/"
      - sh -c "mkdir -p dist"
      - sh -c "cp -r site/* dist/"
      - sh -c "ls -la dist"

  - name: test
    image: alpine:3.20
    commands:
      - sh -c "echo Running basic grep test..."
      - sh -c "grep -q Hello dist/index.html"
      - sh -c "echo All good!"

  - name: package
    image: alpine:3.20
    commands:
      - sh -c "tar -czf dist.tar.gz dist"
      - sh -c "ls -lh dist.tar.gz"
🧠 Lessons Learned
✔ YAML formatting must follow strict syntax (- before each command)
✔ Callback URL must be exactly <NGROK_URL>/authorize
✔ Local CI/CD is possible using Docker + Ngrok + GitHub

📌 Future Enhancements
Auto-deploy to GitHub Pages / S3 / FTP

Add Linting / HTML Validator / Deployment steps

Replace static site with Node / React / Python App
