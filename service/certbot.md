To install **Certbot** on your VPS and set up **SSL/TLS certificates** for your domains, follow these steps:

---

## Step 1: Install Certbot

### For Ubuntu/Debian:

1. **Add the Certbot repository**:
   ```bash
   sudo apt update
   sudo apt install software-properties-common -y
   sudo add-apt-repository ppa:certbot/certbot
   sudo apt update
   ```

2. **Install Certbot and Nginx plugin** (if you are using Nginx):
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   ```

   If you're using **Apache**:
   ```bash
   sudo apt install certbot python3-certbot-apache -y
   ```

3. **Verify Certbot installation**:
   ```bash
   certbot --version
   ```

---

## Step 2: Configure Nginx (if using Nginx)

1. **Create a server block** for your domain if you haven't already done so (in `/etc/nginx/sites-available/yourdomain.com`):
   
   Example:
   ```nginx
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;

       root /var/www/yourdomain.com;
       index index.html index.htm;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

2. **Enable your site**:
   ```bash
   sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/
   ```

3. **Test the Nginx configuration**:
   ```bash
   sudo nginx -t
   ```

4. **Reload Nginx**:
   ```bash
   sudo systemctl reload nginx
   ```

---

## Step 3: Obtain an SSL Certificate

1. **Run Certbot to obtain an SSL certificate** for your domain:
   ```bash
   sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
   ```

   - `--nginx` tells Certbot to automatically configure SSL for Nginx.
   - Replace `yourdomain.com` and `www.yourdomain.com` with your actual domain names.

2. Certbot will automatically:
   - Obtain an SSL certificate from **Let’s Encrypt**.
   - Configure Nginx to use the SSL certificate.
   - Set up HTTP to HTTPS redirection.

3. You will be asked to provide an email address for notifications and agree to the terms of service.

4. **Verify SSL setup**:
   After completion, Certbot will automatically reload Nginx. You can test the setup by visiting your website via `https://yourdomain.com`.

---

## Step 4: Automate Certificate Renewal

Let's Encrypt certificates are valid for 90 days. To automatically renew the certificates, you can set up a cron job to run **Certbot's renewal command**.

1. **Edit the cron file**:
   ```bash
   sudo crontab -e
   ```

2. Add the following line to renew certificates daily:
   ```bash
   0 0 * * * certbot renew --quiet && systemctl reload nginx
   ```

   - This will attempt to renew the certificates every day at midnight and reload Nginx if the certificate is successfully renewed.

3. Save and close the file.

---

## Step 5: Verify SSL Certificate Renewal

To manually test the renewal process, you can run:
```bash
sudo certbot renew --dry-run
```

This will simulate the renewal process without actually renewing the certificate.

---

### Troubleshooting

- **Certificate not working?** Check Nginx logs for errors:
   ```bash
   sudo tail -f /var/log/nginx/error.log
   ```

- **Firewall?** Ensure that ports `80` (HTTP) and `443` (HTTPS) are open in your firewall:
   ```bash
   sudo ufw allow 'Nginx Full'
   ```

---

### Summary

You have successfully:
1. Installed **Certbot** and the required plugin for **Nginx**.
2. Obtained and installed a **Let's Encrypt SSL certificate** for your domain.
3. Configured automatic **SSL certificate renewal** with a cron job.

Your site is now served securely with **HTTPS**! Let me know if you need further assistance.