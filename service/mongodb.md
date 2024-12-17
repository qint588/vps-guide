To install and configure **MongoDB** on your VPS and make it accessible publicly, follow these steps:

---

## Step 1: Install MongoDB

1. **Update package list**:
   ```bash
   sudo apt update
   ```

2. **Install MongoDB**:
   ```bash
   sudo apt install -y mongodb
   ```

3. **Start and enable MongoDB**:
   ```bash
   sudo systemctl start mongodb
   sudo systemctl enable mongodb
   ```

4. **Check the status of MongoDB**:
   ```bash
   sudo systemctl status mongodb
   ```

5. **Verify MongoDB installation**:
   ```bash
   mongo --version
   ```

---

## Step 2: Configure MongoDB for Remote Access

By default, MongoDB binds only to `localhost` (127.0.0.1), so it won't accept remote connections. To allow remote connections, follow these steps:

1. **Open MongoDB configuration file**:
   ```bash
   sudo nano /etc/mongodb.conf
   ```

2. **Find the `bindIp` setting** (under the `net` section):
   - Change the value from `127.0.0.1` to `0.0.0.0` to allow MongoDB to listen on all interfaces.
   ```ini
   net:
     bindIp: 0.0.0.0
   ```

3. **Save and exit** the file (`Ctrl + O`, `Enter`, `Ctrl + X`).

4. **Restart MongoDB to apply changes**:
   ```bash
   sudo systemctl restart mongodb
   ```

---

## Step 3: Set Up Firewall for MongoDB

If you're using **UFW** (Uncomplicated Firewall), allow access to MongoDB's default port (27017):

1. **Allow MongoDB port** (27017):
   ```bash
   sudo ufw allow 27017
   ```

2. **Check the status of the firewall**:
   ```bash
   sudo ufw status
   ```

---

## Step 4: Configure MongoDB Authentication (Optional)

To enhance security, you can enable **authentication** for MongoDB.

1. **Create an admin user** for MongoDB:
   - First, connect to the MongoDB shell as a superuser (without authentication):
   ```bash
   mongo
   ```

   - Switch to the `admin` database:
   ```js
   use admin
   ```

   - Create an admin user:
   ```js
   db.createUser({
     user: "admin",
     pwd: "your_secure_password",
     roles: [{ role: "root", db: "admin" }]
   });
   ```

   - Exit the MongoDB shell:
   ```js
   exit
   ```

2. **Enable authentication** by editing the configuration file again (`/etc/mongodb.conf`):

   ```bash
   sudo nano /etc/mongodb.conf
   ```

   - Under the `security` section, add:
   ```ini
   security:
     authorization: "enabled"
   ```

3. **Restart MongoDB** to apply the changes:
   ```bash
   sudo systemctl restart mongodb
   ```

4. **Verify authentication**:
   - Connect to MongoDB with the admin user credentials:
   ```bash
   mongo -u admin -p --authenticationDatabase admin
   ```

---

## Step 5: Test Remote Connection

1. From a **remote machine**, use the following command to connect to your MongoDB instance:
   ```bash
   mongo -u admin -p --authenticationDatabase admin --host your_vps_ip
   ```

2. Replace `your_vps_ip` with the IP address of your VPS and provide the admin password when prompted.

---

## Step 6: Secure MongoDB (Optional but Recommended)

To secure MongoDB further, consider these additional steps:

1. **Limit access by IP**: If you know the IP addresses that need to access MongoDB, you can restrict access to just those addresses. For example, to allow access only from `192.168.1.100`, change the bind address in `/etc/mongodb.conf`:
   ```ini
   net:
     bindIp: 192.168.1.100,127.0.0.1
   ```

2. **Use a VPN**: If possible, connect to MongoDB through a VPN to limit access to a private network.

3. **Use TLS/SSL encryption**: You can configure MongoDB to use TLS/SSL for secure connections, especially if you are connecting over the internet. For more information, refer to the [MongoDB TLS documentation](https://docs.mongodb.com/manual/tutorial/configure-ssl/).

---

## Step 7: Enable MongoDB Autostart (Optional)

MongoDB should start automatically on reboot. If it doesn't, you can enable it with:

```bash
sudo systemctl enable mongodb
```

---

### Summary

You have successfully:
1. Installed **MongoDB** on your VPS.
2. Configured **remote access** to MongoDB.
3. Set up **firewall rules** for MongoDB access.
4. Optionally enabled **authentication** for enhanced security.

Your MongoDB instance is now accessible remotely. Let me know if you need further assistance!