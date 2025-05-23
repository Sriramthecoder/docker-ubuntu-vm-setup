# Ubuntu VM with Docker Compose

Create a lightweight Ubuntu virtual machine alternative using Docker with SSH access, persistent storage, and GUI support.

## 🚀 Quick Start

### Prerequisites
- Docker Engine 20.10+
- Docker Compose 2.2+

```bash
# 1. Create project
mkdir ubuntu-vm && cd ubuntu-vm

# 2. Initialize files
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  ubuntu-vm:
    image: ubuntu:22.04
    container_name: ubuntu-vm
    hostname: ubuntu-vm
    restart: unless-stopped
    ports:
      - "2222:22"    # SSH
      - "8080:80"    # Web server
    volumes:
      - ./storage:/root/storage
    environment:
      - TZ=UTC
    tty: true
    stdin_open: true
EOF

# 3. Start container
docker-compose up -d

# 4. Setup SSH (run inside container)
docker exec -it ubuntu-vm bash -c "
apt update && apt upgrade -y &&
apt install -y openssh-server sudo nano &&
useradd -m -s /bin/bash developer &&
echo 'developer:ubuntu' | chpasswd &&
usermod -aG sudo developer &&
mkdir -p /var/run/sshd &&
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config &&
sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config &&
service ssh restart
"

# 5. Connect via SSH
ssh developer@localhost -p 2222
# Password: ubuntu

