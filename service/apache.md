To set up **Apache HTTP Server** (often referred to as `httpd`) on your VPS, follow these steps:

---

## Step 1: Install Apache (httpd)

1. **Update the package list** to ensure you have the latest repository information:
   ```bash
   sudo apt update
   ```

2. **Install Apache** (httpd) on your system:
   ```bash
   sudo apt install -y apache2
   ```

3. **Check Apache version** to verify the installation:
   ```bash
   apache2 -v
   ```

---

## Step 2: Start and Enable Apache

1. **Start the Apache service**:
   ```bash
   sudo systemctl start apache2
   ```

2. **Enable Apache to start on boot**:
   ```bash
   sudo systemctl enable apache2
   ```

3. **Check Apache status** to ensure it is running properly:
   ```bash
   sudo systemctl status apache2
   ```

---

## Step 3: Configure Apache (Optional)

1. **Check the default Apache web page** by opening your server's IP address in a browser:
   ```
   http://your_vps_ip
   ```
   You should see the "Apache2 Ubuntu Default Page," confirming that Apache is working.

2. **Edit the Apache configuration files** for custom settings if needed:
   - Main Apache configuration file: `/etc/apache2/apache2.conf`
   - Site configuration files are typically located in `/etc/apache2/sites-available/`

---

## Step 4: Configure Firewall for Apache

If you're using **UFW** (Uncomplicated Firewall), you'll need to allow traffic on ports 80 (HTTP) and 443 (HTTPS):

1. **Allow HTTP and HTTPS traffic**:
   ```bash
   sudo ufw allow 'Apache Full'
   ```

2. **Check the firewall status** to verify the changes:
   ```bash
   sudo ufw status
   ```

---

## Step 5: Set Up Virtual Hosts (Optional)

Apache supports virtual hosting, allowing you to host multiple sites on the same server. Here's how to set it up:

1. **Create a directory for your website** (replace `yourdomain.com` with your actual domain or site name):
   ```bash
   sudo mkdir -p /var/www/yourdomain.com/public_html
   ```

2. **Set the appropriate permissions** for the website directory:
   ```bash
   sudo chown -R $USER:$USER /var/www/yourdomain.com/public_html
   sudo chmod -R 755 /var/www/yourdomain.com
   ```

3. **Create a sample `index.html` file** in your website's public directory:
   ```bash
   echo "<html><body><h1>Welcome to Your Website</h1></body></html>" | sudo tee /var/www/yourdomain.com/public_html/index.html
   ```

4. **Create a virtual host configuration file** for your site:
   ```bash
   sudo nano /etc/apache2/sites-available/yourdomain.com.conf
   ```

   Example configuration for the virtual host:
   ```apache
   <VirtualHost *:80>
       ServerAdmin webmaster@yourdomain.com
       ServerName yourdomain.com
       DocumentRoot /var/www/yourdomain.com/public_html
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

5. **Enable the new site** by running:
   ```bash
   sudo a2ensite yourdomain.com.conf
   ```

6. **Disable the default Apache site** (optional):
   ```bash
   sudo a2dissite 000-default.conf
   ```

7. **Reload Apache** to apply the changes:
   ```bash
   sudo systemctl reload apache2
   ```

---

## Step 6: Enable SSL (Optional)

To enable SSL (HTTPS) for your Apache site, you can use **Let's Encrypt** with **Certbot**:

1. **Install Certbot and Apache plugin**:
   ```bash
   sudo apt install certbot python3-certbot-apache
   ```

2. **Obtain an SSL certificate**:
   ```bash
   sudo certbot --apache
   ```

   - Follow the interactive prompts to configure SSL for your domain.
   - Certbot will automatically set up SSL on your Apache server.

3. **Verify the SSL certificate**:
   After the certificate is obtained, visit `https://yourdomain.com` to ensure it's secured with HTTPS.

4. **Auto-renewal**: Certbot automatically configures cron jobs for renewal. To test the renewal process:
   ```bash
   sudo certbot renew --dry-run
   ```

---

## Step 7: Check Apache Configuration and Restart

After making configuration changes, it's important to check the Apache configuration for errors before restarting.

1. **Check Apache configuration** for syntax errors:
   ```bash
   sudo apache2ctl configtest
   ```

   If the output is `Syntax OK`, proceed to the next step.

2. **Restart Apache** to apply all changes:
   ```bash
   sudo systemctl restart apache2
   ```

---

## Step 8: Enable Apache Modules (Optional)

Apache comes with many useful modules, such as mod_rewrite for URL rewriting. To enable a module, use the following command:

1. **Enable mod_rewrite** (example):
   ```bash
   sudo a2enmod rewrite
   ```

2. **Restart Apache** after enabling modules:
   ```bash
   sudo systemctl restart apache2
   ```

---

### Summary

You have successfully:
1. Installed **Apache HTTP Server** (httpd) on your VPS.
2. Started and enabled Apache to run on boot.
3. Configured **virtual hosts** to host multiple websites.
4. Optionally set up **SSL** using **Certbot**.
5. Configured your firewall to allow HTTP/HTTPS traffic.

Apache is now ready to serve websites on your VPS. Let me know if you need any further assistance!