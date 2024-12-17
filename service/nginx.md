To install and configure **Nginx** on your VPS, follow these steps:

---

## Step 1: Install Nginx

1. **Update your package list**:
   ```bash
   sudo apt update
   ```

2. **Install Nginx**:
   ```bash
   sudo apt install nginx -y
   ```

3. **Verify the installation**:
   ```bash
   nginx -v
   ```

---

## Step 2: Start and Enable Nginx

1. **Start Nginx**:
   ```bash
   sudo systemctl start nginx
   ```

2. **Enable Nginx to start on boot**:
   ```bash
   sudo systemctl enable nginx
   ```

3. **Check the status of Nginx**:
   ```bash
   sudo systemctl status nginx
   ```

---

## Step 3: Adjust Firewall (if necessary)

If you're using **UFW** (Uncomplicated Firewall), allow Nginx to communicate through the firewall:

1. Allow HTTP and HTTPS traffic:
   ```bash
   sudo ufw allow 'Nginx Full'
   ```

2. To check the status of the firewall:
   ```bash
   sudo ufw status
   ```

---

## Step 4: Configure Nginx

1. **Default Configuration**:
   After installation, Nginx comes with a default configuration file located at:
   ```bash
   /etc/nginx/sites-available/default
   ```

   You can edit this file to modify the default settings for your website:
   ```bash
   sudo nano /etc/nginx/sites-available/default
   ```

   Example server block (replace with your domain or IP):

   ```nginx
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;

       root /var/www/yourdomain;
       index index.html index.htm;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

2. **Save the file** and exit (`Ctrl + O`, `Enter`, `Ctrl + X`).

---

## Step 5: Test Nginx Configuration

Before reloading Nginx, it’s a good practice to test if the configuration files are correct:

```bash
sudo nginx -t
```

If the test is successful, it will output:

```
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

---

## Step 6: Reload Nginx

1. **Reload Nginx to apply the changes**:
   ```bash
   sudo systemctl reload nginx
   ```

---

## Step 7: Set Up Your Website

1. **Create a directory for your website**:
   ```bash
   sudo mkdir -p /var/www/yourdomain.com
   ```

2. **Upload or create your website files** in this directory.

3. **Change ownership of the directory** to the Nginx user (`www-data`):
   ```bash
   sudo chown -R www-data:www-data /var/www/yourdomain.com
   ```

---

## Step 8: Test Your Website

1. If you have a domain, point it to your VPS's IP address.
2. Visit your domain (e.g., `http://yourdomain.com`) in a browser.
   - You should see your website.

If you're using the VPS IP directly (e.g., `http://your_vps_ip`), you should see the content of `/var/www/yourdomain.com`.

---

## Step 9: Set Up Additional Websites (Virtual Hosts)

1. **Create a new configuration file** for another website under `/etc/nginx/sites-available`:
   ```bash
   sudo nano /etc/nginx/sites-available/anotherdomain.com
   ```

2. Add the configuration (replace `anotherdomain.com` with your actual domain or IP):

   ```nginx
   server {
       listen 80;
       server_name anotherdomain.com www.anotherdomain.com;

       root /var/www/anotherdomain.com;
       index index.html index.htm;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

3. **Create the website directory**:
   ```bash
   sudo mkdir -p /var/www/anotherdomain.com
   ```

4. **Create a symbolic link** to `sites-enabled`:
   ```bash
   sudo ln -s /etc/nginx/sites-available/anotherdomain.com /etc/nginx/sites-enabled/
   ```

5. **Test and reload Nginx**:
   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```

---

## Step 10: Troubleshoot (if necessary)

If Nginx isn't serving your website as expected, check the following:

1. **Nginx Error Logs**:
   ```bash
   sudo tail -f /var/log/nginx/error.log
   ```

2. **Nginx Access Logs**:
   ```bash
   sudo tail -f /var/log/nginx/access.log
   ```

3. **Ensure the server block for your domain is properly configured** and no other conflicting configurations exist.

---

### Summary

You have successfully:
1. Installed **Nginx** on your VPS.
2. Configured Nginx to serve a basic website.
3. Tested and reloaded Nginx with the new configuration.
4. Set up multiple websites on your VPS using **virtual hosts**.

If you need further help with configuration or troubleshooting, feel free to ask!