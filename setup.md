Here’s a revised version of the provided steps, formatted according to the structure we’ve been following:

---

### 1. **Configure SSH**

SSH config is a convenient way to save SSH settings on your local machine. The config file is located at `C:\Users\Name\.ssh\config` on Windows.

#### Steps:

1. Open the file with your preferred text editor.
2. Add the following configuration lines:
   ```text
   Host Name
       HostName IP
       Port 22
       User root
       IdentityFile C:\Users\Name\.ssh\vps_rsa
   ```
3. Replace the placeholders (`Name`, `IP`, etc.) with your actual values.
4. Save the file and exit the editor.

Now, you can connect to your VPS using:
```bash
ssh Name
```

---

### 2. **Create a New User**

It is not recommended to use the root user for regular operations. To increase security, we will create a new user and later disable root login.

#### Steps:

1. Create a new user:
   ```bash
   adduser Name
   ```
   - Enter a password for the new user.
   - Optionally, provide additional user information (you can skip by pressing `Enter`).
   - Confirm the information by typing `Y` and pressing `Enter`.

2. Add the new user to the `sudo` group:
   ```bash
   usermod -aG sudo Name
   ```

3. Switch to the new user:
   ```bash
   su Name
   ```

4. Navigate to your home directory:
   ```bash
   cd ~
   ```

5. Create an `.ssh` directory:
   ```bash
   mkdir .ssh
   ```

6. Navigate into the `.ssh` directory:
   ```bash
   cd .ssh
   ```

7. Create the `authorized_keys` file:
   ```bash
   touch authorized_keys
   ```

8. Open the file with your preferred text editor and paste your public key into it. Save and close the editor.

9. Set the correct permissions for the file:
   ```bash
   chmod 600 authorized_keys
   ```

Now, you can SSH into your VPS using your SSH key instead of a password.

---

### 3. **Disable Root Password Login (Optional)**

For added security, you can disable password login for the root user.

#### Steps:

1. Open the SSH configuration file:
   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. Find the `PasswordAuthentication` directive and set it to `no`:
   ```text
   PasswordAuthentication no
   ```

3. Restart SSH to apply the changes:
   ```bash
   sudo systemctl restart ssh
   ```

---

This setup enhances security by using SSH keys and disabling password-based root logins.