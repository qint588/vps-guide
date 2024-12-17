

To set up **Nginx** with **PHP-FPM** using a `.sock` file on a **VPS**, follow these steps. This setup will allow Nginx to communicate with PHP-FPM via a UNIX socket file instead of a TCP port.

---

### Step 1: Install Nginx and PHP-FPM
1. **Update packages**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install Nginx and PHP-FPM**:
   ```bash
   sudo apt install nginx php-fpm -y
   ```

3. **Verify installation**:
   - Check Nginx status:
     ```bash
     sudo systemctl status nginx
     ```
   - Check PHP-FPM status:
     ```bash
     sudo systemctl status php-fpm
     ```

---

### Step 2: Configure PHP-FPM to Use a Socket File
1. Open the PHP-FPM configuration file. Replace `php7.4` with your installed PHP version (e.g., `php8.1`):
   ```bash
   sudo nano /etc/php/7.4/fpm/pool.d/www.conf
   ```

2. Look for the following lines and ensure they are set to use a UNIX socket:
   ```ini
   listen = /run/php/php7.4-fpm.sock
   listen.owner = www-data
   listen.group = www-data
   listen.mode = 0660
   ```

   If the `listen` directive points to `127.0.0.1:9000`, replace it with `/run/php/php7.4-fpm.sock`.

3. Save the file and restart PHP-FPM:
   ```bash
   sudo systemctl restart php7.4-fpm
   ```

4. Verify that the `.sock` file exists:
   ```bash
   ls -l /run/php/
   ```
   You should see `php7.4-fpm.sock`.

---

### Step 3: Configure Nginx to Use the Socket File
1. Edit your Nginx server block configuration file. Replace `example.com` with your domain or use the default Nginx config:
   ```bash
   sudo nano /etc/nginx/sites-available/example.com
   ```

2. Modify the `server` block to include the PHP-FPM `.sock` file:
   ```nginx
   server {
       listen 80;
       server_name example.com;
       root /var/www/html;

       index index.php index.html index.htm;

       location / {
           try_files $uri $uri/ =404;
       }

       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/run/php/php7.4-fpm.sock;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       location ~ /\.ht {
           deny all;
       }
   }
   ```

3. Test the Nginx configuration for syntax errors:
   ```bash
   sudo nginx -t
   ```

4. If the test passes, reload Nginx:
   ```bash
   sudo systemctl reload nginx
   ```

---

### Step 4: Test the Setup
1. **Create a test PHP file**:
   ```bash
   sudo nano /var/www/html/info.php
   ```
   Add the following content:
   ```php
   <?php
   phpinfo();
   ?>
   ```

2. Open your browser and visit your VPS IP address or domain:
   ```
   http://your-server-ip/info.php
   ```

3. If everything is set up correctly, you should see the PHP information page.

---

### Step 5: Secure and Clean Up
1. **Delete the test file** to prevent exposing server information:
   ```bash
   sudo rm /var/www/html/info.php
   ```

2. Enable Nginx and PHP-FPM to start on boot:
   ```bash
   sudo systemctl enable nginx
   sudo systemctl enable php7.4-fpm
   ```

---

### Troubleshooting Tips:
- **If Nginx throws a "502 Bad Gateway" error**:
  - Check if PHP-FPM is running:
    ```bash
    sudo systemctl status php7.4-fpm
    ```
  - Verify the socket file exists:
    ```bash
    ls -l /run/php/
    ```
- **Permissions issue**: Ensure Nginx has permission to access the `.sock` file:
   ```bash
   sudo chown www-data:www-data /run/php/php7.4-fpm.sock
   sudo chmod 660 /run/php/php7.4-fpm.sock
   ```

---

Your Nginx server is now configured to work with PHP-FPM using a `.sock` file. Let me know if you encounter any issues! 🚀

Here's an updated guide that includes support for **PHP 8.4** alongside other versions.

---

### Step 1: Install Nginx and PHP 8.4-FPM
1. **Update packages**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install Nginx and PHP 8.4**:

   - First, add the **PPA for PHP 8.4** (if available):
     ```bash
     sudo add-apt-repository ppa:ondrej/php -y
     sudo apt update
     ```

   - Install PHP 8.4 with FPM and necessary extensions:
     ```bash
     sudo apt install nginx php8.4-fpm php8.4-mbstring php8.4-xml php8.4-mysql php8.4-curl -y
     ```

3. **Verify PHP and Nginx installation**:
   - Check PHP version:
     ```bash
     php -v
     ```
   - Check PHP-FPM service:
     ```bash
     sudo systemctl status php8.4-fpm
     ```

   - Check Nginx status:
     ```bash
     sudo systemctl status nginx
     ```

---

### Step 2: Configure PHP 8.4-FPM to Use a Socket File
1. Open the PHP-FPM configuration file for **PHP 8.4**:
   ```bash
   sudo nano /etc/php/8.4/fpm/pool.d/www.conf
   ```

2. Ensure the following lines use a UNIX socket:
   ```ini
   listen = /run/php/php8.4-fpm.sock
   listen.owner = www-data
   listen.group = www-data
   listen.mode = 0660
   ```

   - If it uses `127.0.0.1:9000`, change it to `/run/php/php8.4-fpm.sock`.

3. Restart the PHP 8.4-FPM service:
   ```bash
   sudo systemctl restart php8.4-fpm
   ```

4. Verify the socket file exists:
   ```bash
   ls -l /run/php/
   ```
   Look for `php8.4-fpm.sock`.

---

### Step 3: Configure Nginx to Use PHP 8.4 Socket File
1. Open your Nginx configuration file. Replace `example.com` with your domain name:
   ```bash
   sudo nano /etc/nginx/sites-available/example.com
   ```

2. Modify the server block to use PHP 8.4 socket:
   ```nginx
   server {
       listen 80;
       server_name example.com;
       root /var/www/html;

       index index.php index.html index.htm;

       location / {
           try_files $uri $uri/ =404;
       }

       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/run/php/php8.4-fpm.sock;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       location ~ /\.ht {
           deny all;
       }
   }
   ```

3. Test the Nginx configuration:
   ```bash
   sudo nginx -t
   ```

4. If successful, reload Nginx:
   ```bash
   sudo systemctl reload nginx
   ```

---

### Step 4: Test PHP 8.4 Setup
1. **Create a test file**:
   ```bash
   sudo nano /var/www/html/info.php
   ```
   Add the following content:
   ```php
   <?php
   phpinfo();
   ?>
   ```

2. Visit the server's IP address or domain in your browser:
   ```
   http://your-server-ip/info.php
   ```

3. Confirm that PHP 8.4 is displayed as the active PHP version.

---

### Step 5: Multi-PHP Version Support (Optional)
If you want multiple PHP versions (e.g., PHP 8.4 alongside PHP 7.4), you can configure Nginx to use different PHP versions for different sites:

1. Create separate server blocks for each version:

- **For PHP 8.4**:
   ```nginx
   location ~ \.php$ {
       include snippets/fastcgi-php.conf;
       fastcgi_pass unix:/run/php/php8.4-fpm.sock;
   }
   ```

- **For PHP 7.4**:
   ```nginx
   location ~ \.php$ {
       include snippets/fastcgi-php.conf;
       fastcgi_pass unix:/run/php/php7.4-fpm.sock;
   }
   ```

2. Link each Nginx site configuration to the appropriate PHP version.

---

### Step 6: Cleanup and Finalize
1. **Remove the test file**:
   ```bash
   sudo rm /var/www/html/info.php
   ```

2. Ensure PHP-FPM services are enabled on boot:
   ```bash
   sudo systemctl enable php8.4-fpm
   sudo systemctl enable nginx
   ```

---

### Troubleshooting
- **502 Bad Gateway**:
   - Check PHP 8.4-FPM service:
     ```bash
     sudo systemctl status php8.4-fpm
     ```
   - Verify the `.sock` file exists:
     ```bash
     ls -l /run/php/
     ```

- **Permission Issues**:
   - Fix permissions for the socket file:
     ```bash
     sudo chown www-data:www-data /run/php/php8.4-fpm.sock
     sudo chmod 660 /run/php/php8.4-fpm.sock
     ```

---

Your server is now configured to use **PHP 8.4** with Nginx and UNIX sockets! Let me know if you need further assistance. 🚀

To configure **Nginx** to use **PHP-FPM** with a `.sock` file and have it work with a specific **domain name**, follow the steps below:

---

### Step 1: Verify PHP-FPM Socket File
1. Ensure that the PHP-FPM `.sock` file exists for your PHP version (e.g., `php8.4-fpm`):
   ```bash
   ls -l /run/php/
   ```
   You should see something like:
   ```
   srw-rw---- 1 www-data www-data 0 Dec 30 12:00 php8.4-fpm.sock
   ```

If it doesn't exist:
- Ensure PHP-FPM is running:
   ```bash
   sudo systemctl restart php8.4-fpm
   sudo systemctl status php8.4-fpm
   ```

---

### Step 2: Create a Server Block for Your Domain

1. Open a new Nginx configuration file for your domain:
   ```bash
   sudo nano /etc/nginx/sites-available/yourdomain.com
   ```

2. Add the following configuration block, specifying the **socket file** for PHP-FPM:

   ```nginx
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;
       root /var/www/yourdomain.com/html;

       index index.php index.html index.htm;

       # Main location block
       location / {
           try_files $uri $uri/ =404;
       }

       # PHP-FPM via Unix socket
       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/run/php/php8.4-fpm.sock;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       # Deny access to hidden files
       location ~ /\.ht {
           deny all;
       }
   }
   ```

3. Save the file and exit (`Ctrl + X`, then `Y`).

---

### Step 3: Create a Symbolic Link to Enable the Site
1. Enable the new configuration file by creating a symlink:
   ```bash
   sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/
   ```

2. Test Nginx configuration:
   ```bash
   sudo nginx -t
   ```

3. If the test is successful, reload Nginx:
   ```bash
   sudo systemctl reload nginx
   ```

---

### Step 4: Set Up Your Website Files
1. Create the root directory for your domain:
   ```bash
   sudo mkdir -p /var/www/yourdomain.com/html
   ```

2. Change ownership to your user (or `www-data` if using Nginx default user):
   ```bash
   sudo chown -R www-data:www-data /var/www/yourdomain.com
   sudo chmod -R 755 /var/www/yourdomain.com
   ```

3. Add a test PHP file to verify:
   ```bash
   sudo nano /var/www/yourdomain.com/html/info.php
   ```
   Add the following content:
   ```php
   <?php
   phpinfo();
   ?>
   ```

---

### Step 5: Update DNS (Optional for Real Domains)
1. Point your **domain's A record** to the server's IP address using your DNS provider.

2. Verify DNS propagation using tools like:
   ```bash
   dig yourdomain.com
   ```

---

### Step 6: Test the Configuration
1. Open your browser and visit:
   ```
   http://yourdomain.com/info.php
   ```

2. If the setup is successful, you will see the PHP information page.

3. Remove the test file for security:
   ```bash
   sudo rm /var/www/yourdomain.com/html/info.php
   ```

---

### Troubleshooting
- **502 Bad Gateway**:
   - Verify the PHP-FPM service:
     ```bash
     sudo systemctl status php8.4-fpm
     ```
   - Check if the `.sock` file exists in `/run/php/`.

- **Permission Issues**:
   - Ensure the `.sock` file permissions allow Nginx to access it:
     ```bash
     sudo chown www-data:www-data /run/php/php8.4-fpm.sock
     sudo chmod 660 /run/php/php8.4-fpm.sock
     ```

- **Nginx Errors**:
   - Verify Nginx logs for detailed error messages:
     ```bash
     sudo tail -f /var/log/nginx/error.log
     ```

---

Now your domain **`yourdomain.com`** is configured to use **Nginx** and **PHP-FPM** via a UNIX socket (`.sock` file). Let me know if you need further assistance! 🚀

The `upload_max_filesize` setting in PHP is **not directly related to the `.sock` file** but is a configuration within PHP-FPM (or PHP itself) to control the maximum size of files that can be uploaded via a form submission.

When using Nginx with PHP-FPM via a **UNIX socket file (`.sock`)**, Nginx communicates with PHP-FPM for executing PHP scripts, including handling file uploads. File upload limits must be configured properly to avoid errors.

---

### Why Is `upload_max_filesize` Important?

1. **File Upload Limits**: If `upload_max_filesize` is too small, users will not be able to upload large files. They may see errors like:
   ```
   PHP Warning: File size exceeds the maximum allowed size
   ```
2. **Nginx Proxy Settings**: Nginx also has its own `client_max_body_size` directive, which controls the maximum request size. If this is smaller than `upload_max_filesize`, uploads will fail at the Nginx level.

3. **Balanced Configuration**: For file uploads to work properly:
   - `upload_max_filesize` in PHP-FPM must be large enough.
   - `post_max_size` (for handling form POST data) must also be adjusted.
   - Nginx `client_max_body_size` must be equal to or larger than the PHP limits.

---

### How to Configure `upload_max_filesize` with PHP-FPM

1. **Edit the PHP-FPM `php.ini` File**:
   Locate the `php.ini` file for your PHP version (e.g., PHP 8.4):
   ```bash
   sudo nano /etc/php/8.4/fpm/php.ini
   ```

2. Adjust these values:
   ```ini
   upload_max_filesize = 100M
   post_max_size = 100M
   ```

   - `upload_max_filesize`: Maximum size of an uploaded file.
   - `post_max_size`: Maximum size of POST data (includes file and form data).

3. **Restart PHP-FPM** to apply the changes:
   ```bash
   sudo systemctl restart php8.4-fpm
   ```

---

### Configure Nginx for Large File Uploads

1. Open your Nginx configuration file for the site:
   ```bash
   sudo nano /etc/nginx/sites-available/yourdomain.com
   ```

2. Add or adjust the `client_max_body_size` directive inside the `server` block:
   ```nginx
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;
       root /var/www/yourdomain.com/html;

       client_max_body_size 100M;

       location / {
           try_files $uri $uri/ =404;
       }

       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/run/php/php8.4-fpm.sock;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }
   }
   ```

3. Test the Nginx configuration and reload:
   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```

---

### Why Consider the `.sock` File?

The `.sock` file itself doesn't directly affect upload file size limits. It serves as the communication channel between Nginx and PHP-FPM. However, misconfigurations in PHP settings (e.g., `upload_max_filesize`) or Nginx settings (e.g., `client_max_body_size`) can cause the following issues:

- File uploads get blocked or fail silently.
- Errors like **413: Request Entity Too Large** in Nginx.

---

### Summary

- **`upload_max_filesize`** and **`post_max_size`** are PHP-FPM settings for upload limits.
- **`client_max_body_size`** is the Nginx counterpart.
- Both must be properly configured to avoid errors when using PHP-FPM with a `.sock` file.

Let me know if you need further clarification or assistance! 🚀

If you want to create a **dedicated PHP-FPM `.sock` file for a specific domain** instead of using the default PHP-FPM configuration, you need to set up a custom **PHP-FPM pool**. This is useful when you have multiple domains and want each to have its own PHP-FPM configuration, including separate `.sock` files for better isolation and performance.

---

### Step 1: Create a Custom PHP-FPM Pool for the Domain

1. **Copy the default pool configuration**:
   ```bash
   sudo cp /etc/php/8.4/fpm/pool.d/www.conf /etc/php/8.4/fpm/pool.d/yourdomain.conf
   ```

2. **Edit the new pool configuration**:
   ```bash
   sudo nano /etc/php/8.4/fpm/pool.d/yourdomain.conf
   ```

3. Update the following settings in the file:

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

   - `listen` specifies a **unique socket file** for the domain (e.g., `/run/php/yourdomain.sock`).
   - The pool name `[yourdomain]` must be unique.

---

### Step 2: Restart PHP-FPM to Enable the New Pool

1. Restart the PHP-FPM service:
   ```bash
   sudo systemctl restart php8.4-fpm
   ```

2. Verify that the new `.sock` file has been created:
   ```bash
   ls -l /run/php/
   ```

   You should see:
   ```
   srw-rw---- 1 www-data www-data 0 Dec 30 12:00 yourdomain.sock
   ```

---

### Step 3: Configure Nginx to Use the Custom Socket File

1. Open the Nginx configuration file for your domain:
   ```bash
   sudo nano /etc/nginx/sites-available/yourdomain.com
   ```

2. Update the PHP location block to use the new socket file:

   ```nginx
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;
       root /var/www/yourdomain.com/html;

       index index.php index.html;

       location / {
           try_files $uri $uri/ =404;
       }

       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/run/php/yourdomain.sock;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       location ~ /\.ht {
           deny all;
       }
   }
   ```

3. Test the Nginx configuration:
   ```bash
   sudo nginx -t
   ```

4. Reload Nginx:
   ```bash
   sudo systemctl reload nginx
   ```

---

### Step 4: Verify the Setup

1. **Create a test PHP file** in your domain root:
   ```bash
   sudo nano /var/www/yourdomain.com/html/info.php
   ```
   Add the following:
   ```php
   <?php
   phpinfo();
   ?>
   ```

2. Visit:
   ```
   http://yourdomain.com/info.php
   ```

3. On the PHP info page, confirm:
   - The socket file is being used.
   - The correct PHP-FPM pool (`yourdomain`) is active.

---

### Step 5: Security and Cleanup

1. **Set proper permissions** for the domain root:
   ```bash
   sudo chown -R www-data:www-data /var/www/yourdomain.com
   sudo chmod -R 755 /var/www/yourdomain.com
   ```

2. **Remove the test file** after verification:
   ```bash
   sudo rm /var/www/yourdomain.com/html/info.php
   ```

---

### Why Use a Dedicated Socket for a Domain?

- **Isolation**: Each domain has its own PHP-FPM pool, avoiding interference with others.
- **Performance**: Separate pools allow better tuning (e.g., `pm.max_children`, memory usage).
- **Security**: Dedicated sockets reduce the risk of one site impacting another.

By configuring **domain-specific socket files**, you can fine-tune PHP-FPM for each domain while maintaining cleaner and more organized settings. 🚀