Here’s a basic example of an **Nginx master configuration** file that can be customized for different web applications. This configuration includes the setup for a basic HTTP server, reverse proxy, and SSL for secure connections.

---

### **1. Nginx Master Configuration**

Nginx's master configuration is typically found in `/etc/nginx/nginx.conf`, but this can vary based on your Linux distribution.

```nginx
# Main Nginx configuration file

# Number of worker processes to handle requests
worker_processes auto;

# The maximum number of open files per worker
worker_rlimit_nofile 10000;

# Global error log and pid file location
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

# User and group that Nginx worker processes run as
user nginx;

# File containing configuration for Nginx worker connections
events {
    worker_connections 1024;  # Maximum number of simultaneous connections per worker
}

http {
    # MIME types, logging, and other global configurations
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    # Logs format
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    # Compression settings
    gzip on;
    gzip_comp_level 6;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Configure server and upstream blocks
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;

    # SSL settings for HTTPS
    server {
        listen 443 ssl;
        server_name example.com;

        ssl_certificate /etc/ssl/certs/example.com.crt;
        ssl_certificate_key /etc/ssl/private/example.com.key;

        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
        ssl_prefer_server_ciphers on;

        # Root directory for the website
        root /var/www/html;

        # Default index file
        index index.html index.htm index.php;

        # Location block for static files
        location / {
            try_files $uri $uri/ =404;
        }

        # Location block for PHP-FPM (if using PHP)
        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }

        # Error handling
        error_page 404 /404.html;
        location = /404.html {
            root /var/www/html;
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root /var/www/html;
        }
    }

    # HTTP server configuration for redirecting to HTTPS
    server {
        listen 80;
        server_name example.com www.example.com;

        # Redirect to HTTPS
        return 301 https://$server_name$request_uri;
    }
}
```

### **2. Breakdown of the Configuration**

1. **Global Settings:**
   - `worker_processes auto;`: Automatically adjusts the number of worker processes based on available CPU cores.
   - `worker_rlimit_nofile 10000;`: Limits the number of open files per worker process.
   - `error_log` and `pid`: Locations of Nginx’s error log and PID file.
   - `user nginx;`: Defines the user under which Nginx worker processes run.

2. **Events Block:**
   - Specifies the number of simultaneous connections a worker can handle (`worker_connections`).

3. **HTTP Block:**
   - Includes basic settings like MIME types, logging, and compression.
   - The `log_format` directive specifies how logs are formatted.
   - The `gzip` directive enables compression for better performance.
   - Includes additional configuration files (`/etc/nginx/conf.d/*.conf` and `/etc/nginx/sites-enabled/*`).

4. **Server Blocks:**
   - The first server block listens on port 443 (HTTPS) and configures SSL.
   - It handles serving static files, redirects HTTP traffic to HTTPS, and processes PHP requests through PHP-FPM.
   - It defines how errors should be handled and custom error pages.

5. **Redirect HTTP to HTTPS:**
   - The second server block listens on port 80 (HTTP) and forces a redirect to HTTPS.

### **3. Include Additional Configurations**

You can further configure Nginx for specific applications (like WordPress, Laravel, etc.) by creating separate configuration files in `/etc/nginx/sites-available/` and linking them to `/etc/nginx/sites-enabled/`. For example:

```bash
ln -s /etc/nginx/sites-available/mywebsite.conf /etc/nginx/sites-enabled/
```

### **4. Testing and Reloading Nginx**

Once you’ve made changes to the configuration, it’s important to test and reload Nginx to apply the changes:

#### Test the configuration for syntax errors:
```bash
sudo nginx -t
```

#### Reload Nginx to apply the changes:
```bash
sudo systemctl reload nginx
```

---

### **5. Additional Recommendations**
- **Enable SSL:** Make sure SSL certificates are correctly configured and consider using Let's Encrypt for free certificates.
- **Optimize Performance:** Adjust buffer sizes, caching, and gzip settings for performance improvements.
- **Security:** Restrict access to sensitive areas of your site and use `deny` or `allow` directives where appropriate to limit IP access.

---

This master configuration is a starting point for setting up Nginx. Depending on your specific application (e.g., Laravel, WordPress), you may need to adjust the configuration further for things like URL rewriting, reverse proxying, or caching.

---

### **Nginx Configuration for Laravel**

Laravel requires some specific configuration to handle routing and URL rewriting. You'll also need to ensure that the appropriate permissions are set for directories that need write access (such as `storage` and `bootstrap/cache`).

Here’s a simplified configuration for **Laravel**:

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/laravel/public;

    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;  # Adjust PHP version as needed
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }

    # Optional: Enable caching for static files
    location ~* \.(jpg|jpeg|png|gif|css|js|woff|woff2|ttf|eot|svg|otf)$ {
        expires 365d;
        access_log off;
    }

    error_page 404 /index.php;
}
```

#### Key Configurations:
- **Root Directory:** The root directory should be the `public` folder of Laravel.
- **Try Files:** The `try_files` directive ensures that Nginx tries to serve static files first. If the file doesn’t exist, it falls back to `index.php` for routing.
- **PHP Handling:** Ensure that PHP is processed by `php-fpm`.
- **Access Restriction:** The `.ht` files (which are specific to Apache) are blocked for security.
- **Caching:** You can configure caching for static assets to improve performance.

---

### **Nginx Configuration for WordPress**

WordPress requires certain rules to properly handle permalinks and redirects. Here’s a basic Nginx configuration for **WordPress**:

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/wordpress;

    index index.php index.html index.htm;

    # Permalink structure for WordPress
    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    # PHP processing for WordPress
    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;  # Adjust PHP version as needed
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # Restrict access to hidden files and directories (e.g., .htaccess)
    location ~ /\. {
        deny all;
    }

    # Optional: Enable caching for static files
    location ~* \.(jpg|jpeg|png|gif|css|js|woff|woff2|ttf|eot|svg|otf)$ {
        expires 365d;
        access_log off;
    }

    # Error pages
    error_page 404 /index.php;
    error_page 500 502 503 504 /index.php;

    # Redirect www to non-www (optional)
    server_name www.example.com;
    return 301 http://example.com$request_uri;
}
```

#### Key Configurations:
- **Root Directory:** The root should be the folder where WordPress is installed.
- **Permalinks:** The `try_files` directive is set up to handle WordPress’s pretty permalinks by falling back to `index.php`.
- **PHP Handling:** WordPress needs PHP to process dynamic content. The PHP handler configuration is similar to Laravel.
- **Access Restrictions:** Deny access to hidden files (like `.htaccess`) for security.
- **Caching:** Static files like images, CSS, and JavaScript can be cached for performance.

---

### **Additional Considerations**

#### **1. URL Rewriting:**
Both Laravel and WordPress use URL rewriting to handle clean URLs (permalinks). Nginx uses `try_files` for this, as shown in the examples above, to ensure proper routing without using `.htaccess` files.

#### **2. Reverse Proxying:**
If you want to use Nginx as a reverse proxy (for example, to forward traffic to an application server or to another Nginx server), you can use the `proxy_pass` directive. Here's an example for proxying to a Node.js application:

```nginx
location /app/ {
    proxy_pass http://127.0.0.1:3000;  # Forward to Node.js server on port 3000
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```

#### **3. Caching:**
To improve performance, you can configure caching in Nginx for static assets (images, CSS, JS). This can significantly reduce server load and speed up page load times for users.

```nginx
location ~* \.(jpg|jpeg|png|gif|css|js|woff|woff2|ttf|eot|svg|otf)$ {
    expires 365d;
    access_log off;
}
```

#### **4. Security Headers:**
Consider adding HTTP security headers for added protection:

```nginx
add_header X-Content-Type-Options "nosniff";
add_header X-XSS-Protection "1; mode=block";
add_header X-Frame-Options "SAMEORIGIN";
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
```

#### **5. SSL/TLS Configuration:**
If you’re using SSL (recommended for production environments), ensure that the SSL certificates are correctly configured, and you use the right SSL settings for secure connections.

---

By adjusting these configurations for your specific application (Laravel, WordPress, etc.), you can optimize your Nginx setup to meet both security and performance requirements.