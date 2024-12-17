To set up **Docker** on your VPS, follow these steps:

---

## Step 1: Install Docker

1. **Update the system’s package index**:
   ```bash
   sudo apt update
   ```

2. **Install dependencies** to allow apt to use repositories over HTTPS:
   ```bash
   sudo apt install -y \
     apt-transport-https \
     ca-certificates \
     curl \
     software-properties-common
   ```

3. **Add Docker's official GPG key**:
   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. **Add Docker's APT repository**:
   ```bash
   sudo add-apt-repository \
     "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) \
     stable"
   ```

5. **Update the package index again**:
   ```bash
   sudo apt update
   ```

6. **Install Docker**:
   ```bash
   sudo apt install -y docker-ce
   ```

7. **Verify Docker installation**:
   ```bash
   sudo docker --version
   ```

---

## Step 2: Start and Enable Docker

1. **Start Docker service**:
   ```bash
   sudo systemctl start docker
   ```

2. **Enable Docker to start on boot**:
   ```bash
   sudo systemctl enable docker
   ```

3. **Check Docker service status**:
   ```bash
   sudo systemctl status docker
   ```

---

## Step 3: Manage Docker as a Non-Root User (Optional)

By default, Docker requires `sudo` to run commands. If you want to run Docker commands without `sudo`, follow these steps:

1. **Create the Docker group**:
   ```bash
   sudo groupadd docker
   ```

2. **Add your user to the Docker group**:
   ```bash
   sudo usermod -aG docker $USER
   ```

3. **Log out and log back in** to apply the group changes.

4. **Verify Docker command without sudo**:
   ```bash
   docker --version
   ```

---

## Step 4: Test Docker Installation

Run a test container to verify that Docker is working properly:

1. **Run the `hello-world` container**:
   ```bash
   docker run hello-world
   ```

2. If the installation is successful, you should see a message stating that Docker is working correctly.

---

## Step 5: Configure Docker (Optional)

1. **Configure Docker to use a different storage driver** (if needed) by editing the Docker daemon configuration file (`/etc/docker/daemon.json`). For example:
   ```json
   {
     "storage-driver": "overlay2"
   }
   ```

2. **Restart Docker** to apply the changes:
   ```bash
   sudo systemctl restart docker
   ```

---

## Step 6: Docker Compose Installation (Optional)

Docker Compose allows you to define and run multi-container Docker applications. To install Docker Compose, follow these steps:

1. **Download the latest version of Docker Compose**:
   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | jq -r .tag_name)/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

2. **Set the permissions to make Docker Compose executable**:
   ```bash
   sudo chmod +x /usr/local/bin/docker-compose
   ```

3. **Verify Docker Compose installation**:
   ```bash
   docker-compose --version
   ```

---

## Step 7: Test Docker Compose

To test Docker Compose, you can run a simple example:

1. **Create a `docker-compose.yml` file**:
   ```yaml
   version: '3'
   services:
     web:
       image: nginx
       ports:
         - "8080:80"
   ```

2. **Run Docker Compose**:
   ```bash
   docker-compose up
   ```

3. Visit `http://your_vps_ip:8080` in your browser, and you should see the default Nginx page.

---

### Step 8: Enable Docker to Start on Boot (Optional)

If you want Docker to start automatically when your VPS reboots:

```bash
sudo systemctl enable docker
```

---

## Summary

You have successfully:
1. Installed **Docker** on your VPS.
2. Started and enabled **Docker** to run on boot.
3. Optionally configured Docker for non-root user access.
4. Installed **Docker Compose** (optional).
5. Verified the installation by running test containers and services.

Docker is now set up on your VPS and ready for use. Let me know if you need further help!