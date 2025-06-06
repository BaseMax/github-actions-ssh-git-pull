# GitHub Actions SSH Git Update Template

This repository provides a **GitHub Actions CI/CD template** to automatically **update code from Git** and **restart a Linux service or PM2-managed app** on a remote server using **SSH**.

> 🚀 Ideal for deploying updates to remote Git projects directly from your GitHub repository.

---

## ✨ Features

- ✅ Automatic deployment on push to `main` (or any branch)
- 🔐 Secure connection via SSH (using GitHub secrets)
- 🌀 Pulls the latest code from Git
- 🔁 Restarts a **Linux service** or **PM2 process**
- 🔧 Easy to fork and adapt for any project

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
   - Git installed
   - PM2 (for Node.js apps) or a systemd-managed service
   - SSH access configured
2. GitHub repository with:
   - This template copied or forked
   - Required secrets added (see below)

---

## 🔐 Required GitHub Secrets

Go to your repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret** and add:

| Secret Name     | Description                                     |
|------------------|-------------------------------------------------|
| `SSH_HOST`       | IP or domain of your remote server              |
| `SSH_USERNAME`   | SSH user with access to the project directory   |
| `SSH_KEY`        | Private SSH key (no passphrase)                 |
| `SSH_PASSWORD`   | SSH password with access to the project directory   |
| `PROJECT_PATH`    | Absolute path of the Git project on the server   |
| `RESTART_COMMAND` | Command to restart the service or PM2 app        |

---

## 📦 GitHub Actions Workflow - SSH by Key (`.github/workflows/deploy-git-ssh-key.yml`)

```yaml
name: Deploy via SSH by Key

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
            git fetch origin main
            git reset --hard origin/main
            git clean -fd
            ${{ secrets.RESTART_COMMAND }}
          EOF
```

## 📦 GitHub Actions Workflow - SSH by Password (`.github/workflows/git-ssh-password.yml`)

```yaml
name: Deploy via SSH by Password

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Install sshpass
        run: sudo apt-get update && sudo apt-get install -y sshpass

      - name: Deploy to Server via SSH
        env:
          SSHPASS: ${{ secrets.SSH_PASSWORD }}
        run: |
          sshpass -e ssh -p ${{ secrets.SSH_PORT }} -o StrictHostKeyChecking=no ${{ secrets.SSH_USERNAME }}@${{ secrets.SSH_HOST }} << EOF
            cd ${{ secrets.PROJECT_DIRECTORY }}
            git fetch origin main
            git reset --hard origin/main
            git clean -fd
            ${{ secrets.RESTART_COMMAND }}
          EOF
```

## 🚀 Quick Start

- Fork this repo (or copy deploy.yml to your own)
- Add the required GitHub secrets
- Ensure SSH access from GitHub to your remote server
- Push to main branch - the deployment runs automatically!

## 🛠️ Customization

Change main to another branch in the on.push.branches section

Use different `RESTART_COMMAND` values such as:

- `pm2 restart app-name`
- `sudo systemctl restart my-service`
- `npm run build && pm2 reload ecosystem.config.js`

## 🧪 Testing

You can manually trigger a run from GitHub:

Go to Actions → Deploy via SSH → Run workflow

## 🧾 License

MIT License — feel free to use and adapt.

## 🙋‍♂️ Author

Max Base (Ali)

🔗 GitHub: [@BaseMax](https://github.com/basemax)
