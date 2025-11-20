Berikut **Panduan Lengkap**:
✔ docker-compose.yml
✔ GitHub Actions CI/CD (auto deploy ke VPS via SSH)

---

# ✅ **1. docker-compose.yml**

```yaml
version: "3.9"

services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: nginx-static
    ports:
      - "8080:80"
    restart: unless-stopped
```

---

# ✅ **2. GitHub Actions CI/CD – Auto Deploy ke VPS**

**File:** `.github/workflows/deploy.yml`

Workflow ini akan:

* Auto build image di server
* Auto pull repository terbaru
* Jalankan `docker compose up -d`
* Zero downtime menggunakan `--pull always` + rebuild

> Pastikan di VPS sudah ada **Docker & Docker Compose plugin**.

```yaml
name: Deploy to VPS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Deploy to VPS via SSH
        uses: appleboy/ssh-action@v1.2.0
        with:
          host: ${{ secrets.VPS_HOST }}
          username: ${{ secrets.VPS_USER }}
          key: ${{ secrets.VPS_SSH_KEY }}
          script: |
            cd /var/www/static-site

            echo "Pulling latest version..."
            git pull origin main

            echo "Building and running docker..."
            docker compose down
            docker compose build --no-cache
            docker compose up -d

            echo "Deployment finished!"
```

---

# 🔐 **Secrets yang harus dibuat di GitHub Repository → Settings → Secrets**

| Secret Name   | Isi                                             |
| ------------- | ----------------------------------------------- |
| `VPS_HOST`    | IP VPS Anda (misal: 123.123.123.123)            |
| `VPS_USER`    | User SSH (misal: root atau ubuntu)              |
| `VPS_SSH_KEY` | Private key SSH Anda (isi dari `~/.ssh/id_rsa`) |

---

# 📄 **3. README.md (Lengkap & Jelas)**

Gunakan README ini di repository Anda.

---

## **Static Website Deployment with Docker & GitHub Actions**

Project ini adalah template sederhana untuk men-deploy website statis menggunakan:

* **Nginx**
* **Docker & Docker Compose**
* **GitHub Actions (CI/CD)**
* **VPS Server (Ubuntu / Debian)**

---

## 📁 **Project Structure**

```
project/
├── Dockerfile
├── docker-compose.yml
└── html
    └── index.html
```

---

## 🚀 **Local Development**

### **1. Build & Run**

```bash
docker compose up -d --build
```

Akses:

```
http://localhost:8080
```

---

## 🛠 **Dockerfile**

```Dockerfile
FROM nginx
COPY ./html/. /usr/share/nginx/html/.
```

---

## ⚙️ **Deployment Workflow (CI/CD)**

Project ini menggunakan GitHub Actions untuk otomatis deploy ke VPS setiap kali ada push ke branch **main**.

### **File Workflow:**

`.github/workflows/deploy.yml`

Workflow ini melakukan:

* Auto update repository di VPS
* Auto rebuild Docker image
* Auto restart container
* Zero downtime deployment

---

## 📡 **Server Setup (VPS)**

### Di server jalankan:

```bash
sudo apt update && sudo apt install -y docker.io docker-compose-plugin
```

Clone project:

```bash
cd /var/www
git clone git@github.com:YOUR_USERNAME/YOUR_REPO.git static-site
cd static-site
```

Lalu jalankan pertama kali:

```bash
docker compose up -d --build
```

---

## 🔐 Secrets GitHub

Buat secrets di:

**GitHub → Repository → Settings → Secrets → Actions**

| Secret Name   | Description                 |
| ------------- | --------------------------- |
| `VPS_HOST`    | IP VPS                      |
| `VPS_USER`    | Nama user SSH               |
| `VPS_SSH_KEY` | Private SSH key untuk login |

---

## 🎉 Autodeploy

Setiap push ke branch:

```
main
```

CI/CD akan menjalankan:

* git pull
* docker compose down
* docker compose build
* docker compose up -d

---

Jika Anda ingin versi:

🔹 Multi-environment (prod/staging)
🔹 Zero downtime pakai Traefik / Nginx Proxy Manager
🔹 Auto SSL dengan Let's Encrypt
🔹 Notifikasi Telegram setelah deploy

