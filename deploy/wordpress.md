To set up **WordPress** with a special domain using a **PHP-FPM `.sock` file** and Nginx, follow these steps:

---

## Prerequisites

- WordPress files uploaded to the server.
- Nginx and PHP-FPM installed.
- Domain pointed to your server's IP address.
- PHP-FPM version (e.g., **PHP 8.4** in this case).

---

### Step 1: Create a Dedicated PHP-FPM Pool

1. **Copy the default PHP-FPM pool configuration**:
   ```bash
   sudo cp /etc/php/8.4/fpm/pool.d/www.conf /etc/php/8.4/fpm/pool.d/yourdomain.conf
   ```

2. **Edit the new pool configuration**:
   ```bash
   sudo nano /etc/php/8.4/fpm/pool.d/yourdomain.conf
   ```

   Replace the contents with:

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

   - `listen` specifies the socket file for your domain.

3. **Restart PHP-FPM**:
   ```bash
   sudo systemctl restart php8.4-fpm
   ```

4. **Verify the `.sock` file**:
   ```bash
   ls -l /run/php/yourdomain.sock
   ```

   Output:
   ```
   srw-rw---- 1 www-data www-data 0 Dec 30 12:00 yourdomain.sock
   ```

---

### Step 2: Configure Nginx for WordPress with `.sock`

1. **Create a new Nginx server block**:
   ```bash
   sudo nano /etc/nginx/sites-available/yourdomain.com
   ```

2. Add the following configuration:

   ```nginx
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;
       root /var/www/yourdomain.com;

       index index.php index.html index.htm;

       # WordPress permalinks
       location / {
           try_files $uri $uri/ /index.php?$args;
       }

       # PHP-FPM handling with dedicated socket
       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/run/php/yourdomain.sock;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       # Deny access to hidden files
       location ~ /\. {
           deny all;
       }
   }
   ```

3. **Enable the site** by creating a symlink:
   ```bash
   sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/
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

### Step 3: Set Up WordPress

1. **Download WordPress** (if not already uploaded):
   ```bash
   cd /var/www
   sudo wget https://wordpress.org/latest.tar.gz
   sudo tar -xzvf latest.tar.gz
   sudo mv wordpress yourdomain.com
   ```

2. **Set Permissions** for WordPress files:
   ```bash
   sudo chown -R www-data:www-data /var/www/yourdomain.com
   sudo chmod -R 755 /var/www/yourdomain.com
   ```

3. **Create a database** for WordPress:
   ```bash
   sudo mysql -u root -p
   ```
   Run the following SQL commands:
   ```sql
   CREATE DATABASE wordpress_db;
   CREATE USER 'wordpress_user'@'localhost' IDENTIFIED BY 'your_password';
   GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wordpress_user'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

4. **Configure `wp-config.php`**:
   ```bash
   sudo cp /var/www/yourdomain.com/wp-config-sample.php /var/www/yourdomain.com/wp-config.php
   sudo nano /var/www/yourdomain.com/wp-config.php
   ```
   Update the database information:
   ```php
   define('DB_NAME', 'wordpress_db');
   define('DB_USER', 'wordpress_user');
   define('DB_PASSWORD', 'your_password');
   define('DB_HOST', 'localhost');
   ```

---

### Step 4: Test Your Setup

1. Open your browser and visit:
   ```
   http://yourdomain.com
   ```

2. Complete the WordPress installation by following the on-screen setup wizard.

---

### Troubleshooting

1. **502 Bad Gateway**:  
   - Check PHP-FPM status:
     ```bash
     sudo systemctl status php8.4-fpm
     ```
   - Verify the `.sock` file exists:
     ```bash
     ls -l /run/php/yourdomain.sock
     ```

2. **Permission Denied Errors**:  
   - Ensure ownership and permissions are correct:
     ```bash
     sudo chown -R www-data:www-data /var/www/yourdomain.com
     ```

3. **Nginx Errors**:  
   - Check Nginx error logs:
     ```bash
     sudo tail -f /var/log/nginx/error.log
     ```

4. **Database Connection Issues**:  
   - Verify `wp-config.php` has correct database credentials.

---

### Summary

You have successfully:
- Configured a **dedicated PHP-FPM `.sock` file** for WordPress.
- Set up a new Nginx server block to use this `.sock` file.
- Installed and configured WordPress with database integration.

This setup provides better isolation and improved performance for your WordPress site. 🚀 Let me know if you need further assistance!