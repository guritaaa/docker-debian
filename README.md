# Gurita VPS - Debian 12 (Bookworm) Container

Container image berbasis **Debian 12 Slim** yang dikonfigurasi sebagai lingkungan VPS instan berbasis SSH. Dilengkapi dengan paket utilitas penting untuk networking, monitoring, sistem administrasi, serta kustomisasi branding banner interaktif.

---

## 🚀 Fitur Utama

- **Base System:** Debian 12 (`bookworm-slim`), platform `linux/amd64`.
- **Locale & Timezone:** `en_US.UTF-8` dengan zona waktu default `Asia/Jakarta (WIB)`.
- **Remote Access:** OpenSSH Server pre-configured, mendukung akses root via password.
- **Custom Branding:**
  - Pre-login SSH banner.
  - Interactive ASCII Art banner (`GURITA VPS`) dengan info real-time user, uptime, dan waktu sistem.
- **Peralatan Lengkap:**
  - **Networking:** `curl`, `wget`, `ping`, `net-tools`, `iproute2`, `dnsutils`, `traceroute`, `socat`, `telnet`, `netcat`.
  - **Editor & Shell:** `vim`, `nano`, `less`, `bash-completion`.
  - **Monitoring:** `htop`, `lsof`, `psmisc`, `procps`.
  - **Security & System:** `sudo`, `ufw`, `fail2ban`, `cron`, `logrotate`, `rsyslog`.

---

## 📋 Kredensial Default

> ⚠️ **Penting:** Segera ganti password default saat pertama kali menjalankan container di lingkungan publik.

| Parameter | Nilai Default |
| :--- | :--- |
| **Username** | `root` |
| **Password** | `root` |
| **SSH Port (Container)** | `22` |

---

## 🛠️ Cara Build Image

Buka direktori tempat file `Dockerfile` berada, lalu jalankan perintah:

```bash
docker build -t gurita-vps:debian12 .
```

---

## 💻 Cara Menjalankan Container

### 1. Menjalankan Cepat (Standalone)

Jalankan container dan petakan port SSH (contoh: port host `2222` ke port container `22`):

```bash
docker run -d \
  --name debian-vps \
  -p 2222:22 \
  --restart unless-stopped \
  gurita-vps:debian12
```

### 2. Menggunakan Docker Compose (Direkomendasikan)

Buat file `docker-compose.yml`:

```yaml
version: '3.8'

services:
  debian-vps:
    image: gurita-vps:debian12
    container_name: gurita-debian-vps
    restart: unless-stopped
    ports:
      - "2222:22"
    environment:
      - TZ=Asia/Jakarta
    volumes:
      - vps_root_data:/root

volumes:
  vps_root_data:
```

Jalankan layanan:

```bash
docker compose up -d
```

---

## 🔑 Akses SSH

Hubungkan terminal lokal kamu ke container:

```bash
ssh root@localhost -p 2222
```

Masukkan password default: `root`.

Saat berhasil login, kamu akan disambut oleh banner interaktif:

```text
==========================================
          G U R I T A   V P S
==========================================

    ____  _   _ ____ ___ _____  _
   / ___|| | | |  _ \_ _|_   _|/ \
  | |  _ | | | | |_) | |  | | / _ \
  | |_| || |_| |  _ <| |  | |/ ___ \
   \____| \___/|_| \_\_|  |_|_/   \_\


==========================================
 User     : root
 Uptime   : up 2 minutes
 Date     : Mon Oct 12 18:09:10 WIB 2026
==========================================
```

---

## 🔒 Tips Keamanan & Kustomisasi

### Mengganti Password Root

Setelah masuk ke dalam container, ganti password root:

```bash
passwd
```

Atau set password baru saat runtime dari luar container:

```bash
docker exec -it debian-vps bash -c "echo 'root:PasswordBaru123!' | chpasswd"
```

### Menggunakan SSH Key (Tanpa Password)

Untuk keamanan ekstra, nonaktifkan password auth dan gunakan SSH Key:

1. Salin SSH public key kamu:
   ```bash
   docker exec -it debian-vps mkdir -p /root/.ssh
   docker cp ~/.ssh/id_rsa.pub debian-vps:/root/.ssh/authorized_keys
   docker exec -it debian-vps chmod 600 /root/.ssh/authorized_keys
   ```
2. Nonaktifkan otentikasi password di konfigurasi SSH:
   ```bash
   docker exec -it debian-vps sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
   docker restart debian-vps
   ```

---

## 📦 Lisensi

Dibuat untuk kebutuhan lab server, testing container, dan virtual private environment.
