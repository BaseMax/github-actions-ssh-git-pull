# GitHub Actions SSH Docker Update Template

This repository provides a **GitHub Actions CI/CD template** to automatically **update and restart a Docker Compose project** on a remote server using **SSH**.

> 📦 Ideal for quickly deploying updates to remote Docker apps from your GitHub repository.

---

## 🚀 Features

- ✅ Automatic deployment on push to `main` (or any branch)
- 🔐 Secure connection via SSH with secrets
- 🐳 Docker Compose support (`docker compose pull` + `up -d`)
- 🔁 Easy to fork and reuse across projects

---

## 📂 Project Structure

```
.github/
└── workflows/
   └── deploy.yml # CI/CD workflow
```

---

## ⚙️ Prerequisites

1. Remote server with:
   - Docker & Docker Compose installed
   - Public SSH key of the GitHub Action added to `~/.ssh/authorized_keys`
2. GitHub repository with:
   - This template forked or cloned
   - Required secrets added (see below)

---

## 🔐 Required GitHub Secrets

Go to your repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret** and add:

| Secret Name     | Description                                     |
|------------------|-------------------------------------------------|
| `SSH_HOST`       | IP or domain of your remote server              |
| `SSH_USERNAME`   | SSH user with access to the project directory   |
| `SSH_KEY`        | Private SSH key (no passphrase)                 |
| `PROJECT_PATH`   | Absolute path of the Docker Compose project     |

---

## 📦 GitHub Actions Workflow (`.github/workflows/deploy.yml`)

```yaml
name: Deploy via SSH

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v4

      - name: Set up SSH
        uses: webfactory/ssh-agent@v0.9.0
        with:
          ssh-private-key: ${{ secrets.SSH_KEY }}

      - name: Connect and Deploy
        run: |
          ssh -o StrictHostKeyChecking=no ${{ secrets.SSH_USERNAME }}@${{ secrets.SSH_HOST }} << 'EOF'
            cd ${{ secrets.PROJECT_PATH }}
            git pull origin main
            docker compose pull
            docker compose up -d
          EOF
```

## 🚀 Quick Start

- Fork this repo (or copy deploy.yml to your own)
- Add the required GitHub secrets
- Ensure SSH access from GitHub to your remote server
- Push to main branch – the deployment runs automatically!

## 🛠️ Customization

Change main to another branch in the on.push.branches section

Adjust the docker compose commands if your setup differs

Add steps for migrations, backups, health checks, etc.

## 🧪 Testing

You can manually trigger a run from GitHub:

Go to Actions → Deploy via SSH → Run workflow

## 🧾 License

MIT License — feel free to use and adapt.

## 🙋‍♂️ Author

Max Base (Ali)

🔗 GitHub: [@BaseMax](https://github.com/basemax)
