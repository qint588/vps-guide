To install **PostgreSQL** on your VPS and make it accessible publicly, follow these steps:

---

## Step 1: Install PostgreSQL

1. **Update package list**:
   ```bash
   sudo apt update
   ```

2. **Install PostgreSQL**:
   ```bash
   sudo apt install -y postgresql postgresql-contrib
   ```

3. **Check PostgreSQL version**:
   ```bash
   psql --version
   ```

---

## Step 2: Start and Enable PostgreSQL

1. **Start PostgreSQL service**:
   ```bash
   sudo systemctl start postgresql
   ```

2. **Enable PostgreSQL to start on boot**:
   ```bash
   sudo systemctl enable postgresql
   ```

3. **Check PostgreSQL status**:
   ```bash
   sudo systemctl status postgresql
   ```

---

## Step 3: Configure PostgreSQL for Remote Access

By default, PostgreSQL binds only to localhost (127.0.0.1). To allow remote access, follow these steps:

1. **Edit PostgreSQL configuration file**:
   Open the `postgresql.conf` file to change the `listen_addresses` setting:
   ```bash
   sudo nano /etc/postgresql/12/main/postgresql.conf
   ```

   - Find the `listen_addresses` directive and change it to `*` (this allows PostgreSQL to listen on all interfaces).
   ```ini
   listen_addresses = '*'
   ```

2. **Configure client authentication**:
   Edit the `pg_hba.conf` file to allow access from remote IP addresses:

   ```bash
   sudo nano /etc/postgresql/12/main/pg_hba.conf
   ```

   - Add a line at the end of the file to allow remote connections. Replace `your_vps_ip_range` with the IP range you want to allow (or use `0.0.0.0/0` for all IPs, though it's not recommended for production).
   ```ini
   host    all             all             0.0.0.0/0            md5
   ```

   - If you know the specific IP address of the remote machine, you can specify that instead of `0.0.0.0/0`.

3. **Save and close the files** (`Ctrl + O`, `Enter`, `Ctrl + X`).

4. **Restart PostgreSQL to apply the changes**:
   ```bash
   sudo systemctl restart postgresql
   ```

---

## Step 4: Set Up Firewall for PostgreSQL

If you're using **UFW** (Uncomplicated Firewall), you'll need to allow access to PostgreSQL's default port (5432):

1. **Allow PostgreSQL port**:
   ```bash
   sudo ufw allow 5432
   ```

2. **Check the status of the firewall**:
   ```bash
   sudo ufw status
   ```

---

## Step 5: Set Up PostgreSQL User and Database

1. **Switch to the `postgres` user** to run PostgreSQL commands:
   ```bash
   sudo -i -u postgres
   ```

2. **Create a new PostgreSQL user**:
   ```bash
   createuser --interactive --pwprompt newuser
   ```

   - This will prompt you to enter a password for the new user.
   - Follow the prompts to set the username and password for the new user.

3. **Create a new database** (replace `newdatabase` with your desired name):
   ```bash
   createdb newdatabase
   ```

4. **Grant privileges** to the new user for the database:
   ```bash
   psql
   ```

   Inside the PostgreSQL shell, grant privileges:
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE newdatabase TO newuser;
   ```

   - Exit the PostgreSQL shell:
   ```sql
   \q
   ```

---

## Step 6: Test Remote Connection

1. **Test PostgreSQL connection from a remote machine**:
   Use the `psql` command from a remote machine to test the connection to your PostgreSQL server:
   ```bash
   psql -h your_vps_ip -U newuser -d newdatabase
   ```

   - Replace `your_vps_ip` with the IP address of your VPS.
   - Enter the password for the `newuser` when prompted.

2. **Ensure PostgreSQL is accessible** and the firewall is not blocking the connection.

---

## Step 7: Secure PostgreSQL (Optional but Recommended)

To improve security, consider these additional steps:

1. **Limit access by IP**: If possible, restrict remote access to a specific IP or range by modifying the `pg_hba.conf` file.
   
   Example:
   ```ini
   host    all             all             your_remote_ip/32        md5
   ```

2. **Use a VPN**: If you have a private network, consider setting up a VPN to connect to your PostgreSQL server securely.

3. **Disable password authentication for local access** (optional but secure):
   - Edit the `pg_hba.conf` file to use `peer` authentication for local users, which uses the system user credentials:
   ```ini
   local   all             all                                     peer
   ```

4. **Enable SSL connections**: You can configure PostgreSQL to accept SSL connections for encrypted communication. For more information, refer to the [PostgreSQL SSL documentation](https://www.postgresql.org/docs/current/ssl-tcp.html).

---

### Step 8: Enable PostgreSQL Autostart (Optional)

To ensure PostgreSQL starts automatically on boot, enable it:

```bash
sudo systemctl enable postgresql
```

---

### Summary

You have successfully:
1. Installed **PostgreSQL** on your VPS.
2. Configured PostgreSQL to allow **remote access**.
3. Set up a **new PostgreSQL user and database**.
4. Configured the **firewall** to allow access to PostgreSQL.
5. Optionally secured your PostgreSQL installation.

Your PostgreSQL instance is now publicly accessible. Let me know if you need further assistance!