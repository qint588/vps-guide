To install **MariaDB** on a VPS, set up a database, and create a user with privileges for **remote access** (`%`) and **tunnel access**, follow these steps:

---

## Step 1: Install MariaDB Server

1. **Update the package list**:
   ```bash
   sudo apt update
   ```

2. **Install MariaDB**:
   ```bash
   sudo apt install mariadb-server -y
   ```

3. **Start and enable MariaDB**:
   ```bash
   sudo systemctl start mariadb
   sudo systemctl enable mariadb
   ```

4. **Verify MariaDB installation**:
   ```bash
   mysql --version
   ```

---

## Step 2: Secure the MariaDB Installation

Run the MariaDB security script to set a root password and remove insecure defaults:

```bash
sudo mysql_secure_installation
```

- You’ll be prompted to:
  - Set a root password (choose a strong one).
  - Remove anonymous users.
  - Disallow remote root login.
  - Remove the test database.
  - Reload privilege tables.

---

## Step 3: Log in to MariaDB

Access the MariaDB shell:

```bash
sudo mysql -u root -p
```

- Enter the root password you set during the secure installation.

---

## Step 4: Create a Database and User

### 1. Create a New Database
Run the following command to create a database:

```sql
CREATE DATABASE my_database;
```

### 2. Create a User with Remote Access (`%`)
To allow a user to connect from any IP address (`%`), create the user and grant privileges:

```sql
CREATE USER 'my_user'@'%' IDENTIFIED BY 'my_password';
GRANT ALL PRIVILEGES ON my_database.* TO 'my_user'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

- Replace `my_user` and `my_password` with your desired username and password.

---

## Step 5: Configure MariaDB for Remote Access

1. Open the MariaDB configuration file:
   ```bash
   sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
   ```

2. **Bind Address**:
   Find the line starting with `bind-address` and change it to:

   ```ini
   bind-address = 0.0.0.0
   ```

   - This allows MariaDB to listen for connections on all network interfaces.

3. **Save and Exit**, then restart MariaDB:
   ```bash
   sudo systemctl restart mariadb
   ```

---

## Step 6: Allow Firewall Access (Optional)

If you are using a firewall (e.g., **UFW**), allow MariaDB's default port (**3306**) for external connections:

```bash
sudo ufw allow 3306
```

---

## Step 7: Test Remote Connection

From a remote machine, use a MySQL client to connect:

```bash
mysql -h your_server_ip -u my_user -p
```

- Replace `your_server_ip` with your VPS IP address.
- Enter the password when prompted.

If successful, you will have access to the `my_database` database.

---

## Step 8: Set Up SSH Tunnel (Optional)

If you want to connect securely via SSH tunneling:

### 1. SSH Command to Create Tunnel
On your local machine, run the following:

```bash
ssh -L 3307:127.0.0.1:3306 user@your_server_ip
```

- `3307` is the local port (you can use a different one).
- `3306` is the MariaDB port on the server.

### 2. Connect to MariaDB via Tunnel
On your local machine, use the MySQL client to connect:

```bash
mysql -h 127.0.0.1 -P 3307 -u my_user -p
```

- `127.0.0.1` and port `3307` will forward the connection securely to the server.

---

## Summary

You have successfully:
1. Installed **MariaDB** on your VPS.
2. Created a database and a user with **remote access** (`%`).
3. Configured MariaDB for external connections and allowed connections through a firewall.
4. Set up an **SSH tunnel** for secure access.

Let me know if you need further clarification or help! 🚀