To set up a **Node.js application** on a VPS with **PM2 (cluster mode)** and Nginx as a reverse proxy, follow these steps:

---

## Prerequisites
- A VPS with SSH access.
- Node.js and NPM installed.
- Nginx installed.
- PM2 installed globally.

---

### Step 1: Install Node.js and PM2

1. **Install Node.js** using NodeSource (replace with your desired version):
   ```bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt-get install -y nodejs
   ```

   Verify installation:
   ```bash
   node -v
   npm -v
   ```

2. **Install PM2** globally:
   ```bash
   sudo npm install -g pm2
   ```

3. Verify PM2 installation:
   ```bash
   pm2 --version
   ```

---

### Step 2: Prepare the Node.js Application

1. **Upload your Node.js application** to the VPS:
   - Assume your app is located in `/var/www/yourapp`.

2. Navigate to the application directory:
   ```bash
   cd /var/www/yourapp
   ```

3. Install dependencies:
   ```bash
   npm install
   ```

4. **Run the app with PM2 in cluster mode**:
   ```bash
   pm2 start app.js -i max
   ```

   - `-i max` tells PM2 to spawn as many processes as CPU cores.
   - Replace `app.js` with your main application file.

5. Save the PM2 process list to start automatically on reboot:
   ```bash
   pm2 save
   ```

6. Set up PM2 to start on system boot:
   ```bash
   pm2 startup
   ```

   Follow the output instructions to run the suggested command.

---

### Step 3: Configure Nginx as a Reverse Proxy

1. **Create a new Nginx configuration** file for your Node.js app:
   ```bash
   sudo nano /etc/nginx/sites-available/yourapp
   ```

2. Add the following configuration:

   ```nginx
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;

       location / {
           proxy_pass http://127.0.0.1:3000;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```

   - Replace `yourdomain.com` with your domain.
   - Replace `http://127.0.0.1:3000` with the address your Node.js app listens on.

3. **Enable the site** by creating a symlink:
   ```bash
   sudo ln -s /etc/nginx/sites-available/yourapp /etc/nginx/sites-enabled/
   ```

4. **Test the Nginx configuration**:
   ```bash
   sudo nginx -t
   ```

5. **Reload Nginx**:
   ```bash
   sudo systemctl reload nginx
   ```

---

### Step 4: Verify the Setup

1. Visit your domain in a browser:
   ```
   http://yourdomain.com
   ```

   You should see your Node.js application running.

2. **Check PM2 processes**:
   ```bash
   pm2 status
   ```

   You should see your application running in **cluster mode**.

---

### Step 5: Monitor and Manage the Application

- View logs:
   ```bash
   pm2 logs
   ```

- Restart the app:
   ```bash
   pm2 restart app
   ```

- Stop the app:
   ```bash
   pm2 stop app
   ```

- Delete the app from PM2:
   ```bash
   pm2 delete app
   ```

---

### Summary

You have successfully:
1. Installed **Node.js** and **PM2**.
2. Configured PM2 to run the Node.js app in **cluster mode**.
3. Set up Nginx as a **reverse proxy** to forward traffic to your app.

This setup ensures **load balancing** using PM2 and improves performance with Nginx handling static files and traffic routing. 🚀

Let me know if you need further assistance!