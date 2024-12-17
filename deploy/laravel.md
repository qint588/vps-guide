To set up a **Laravel application** with a special domain and PHP-FPM using a dedicated **.sock file**, follow these steps:

---

## Prerequisites:
- **Laravel Project** installed (e.g., in `/var/www/yourdomain.com`).
- Nginx and PHP-FPM are installed.
- A domain name pointing to your server's IP address.

---

### Step 1: Create a Dedicated PHP-FPM Pool for the Domain

1. **Create a new PHP-FPM pool** for your domain:
   ```bash
   sudo cp /etc/php/8.4/fpm/pool.d/www.conf /etc/php/8.4/fpm/pool.d/yourdomain.conf
   ```

2. **Edit the new pool file**:
   ```bash
   sudo nano /etc/php/8.4/fpm/pool.d/yourdomain.conf
   ```

   Update the configuration with the following:

   ```ini
   [yourdomain]
   user = www-data
   group = www-data

   listen = /run/php/yourdomain.sock
   listen.owner = www-data
   listen.group = www-data
   listen.mode = 0660

   pm = dynamic
   pm.max_children = 10
   pm.start_servers = 2
   pm.min_spare_servers = 2
   pm.max_spare_servers = 5

   chdir = /
   ```

   - **`listen`**: Defines the socket file for your domain.

3. **Restart PHP-FPM** to apply changes:
   ```bash
   sudo systemctl restart php8.4-fpm
   ```

4. Verify the `.sock` file is created:
   ```bash
   ls -l /run/php/
   ```

   You should see:
   ```
   srw-rw---- 1 www-data www-data 0 Dec 30 12:00 yourdomain.sock
   ```

---

### Step 2: Configure Nginx for the Laravel Project

1. **Create a new Nginx site configuration** for your domain:
   ```bash
   sudo nano /etc/nginx/sites-available/yourdomain.com
   ```

2. Add the following Nginx configuration:

   ```nginx
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;
       root /var/www/yourdomain.com/public;

       index index.php index.html;

       # Laravel 404 fallback
       location / {
           try_files $uri $uri/ /index.php?$query_string;
       }

       # PHP-FPM with dedicated socket
       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/run/php/yourdomain.sock;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       # Deny access to .htaccess and other hidden files
       location ~ /\.ht {
           deny all;
       }
   }
   ```

3. **Enable the site** by creating a symlink:
   ```bash
   sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/
   ```

4. **Test Nginx configuration**:
   ```bash
   sudo nginx -t
   ```

5. **Reload Nginx**:
   ```bash
   sudo systemctl reload nginx
   ```

---

### Step 3: Set Permissions for the Laravel Directory

Ensure the Laravel project root and `storage` directory are accessible:

1. Set ownership to `www-data` (Nginx and PHP-FPM user):
   ```bash
   sudo chown -R www-data:www-data /var/www/yourdomain.com
   ```

2. Set proper permissions:
   ```bash
   sudo chmod -R 775 /var/www/yourdomain.com/storage
   sudo chmod -R 775 /var/www/yourdomain.com/bootstrap/cache
   ```

---

### Step 4: Test Your Laravel Setup

1. Open your browser and visit:
   ```
   http://yourdomain.com
   ```

2. You should see the Laravel welcome page.

---

### Troubleshooting

1. **502 Bad Gateway**:
   - Check if PHP-FPM is running:
     ```bash
     sudo systemctl status php8.4-fpm
     ```
   - Verify the `.sock` file:
     ```bash
     ls -l /run/php/yourdomain.sock
     ```

2. **Permission Issues**:
   - Ensure the `listen.owner` and `listen.group` in the PHP-FPM pool file match the Nginx user (`www-data`).

3. **Laravel Logs**:
   - Check Laravel logs for errors:
     ```bash
     sudo tail -f /var/www/yourdomain.com/storage/logs/laravel.log
     ```

4. **Nginx Logs**:
   - Check Nginx error logs:
     ```bash
     sudo tail -f /var/log/nginx/error.log
     ```

---

### Summary

You have:
1. Created a **dedicated PHP-FPM pool** with a `.sock` file for your domain.
2. Configured Nginx to use this `.sock` file for handling PHP requests.
3. Set up Laravel with proper permissions and Nginx rules.

This approach ensures **performance isolation** and makes debugging easier for multiple Laravel projects on the same server. 🚀 Let me know if you need further help!