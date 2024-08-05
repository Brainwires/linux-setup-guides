# How To Setup Ubuntu

Setting up a new Ubuntu installation involves several crucial steps to ensure your server runs smoothly and securely. This guide will walk you through the essential steps and considerations when preparing a new Ubuntu operating system.

Click the image below to watch the video on YouTube:

[![Watch the video](https://img.youtube.com/vi/mJlbJO-WDuQ/0.jpg)](https://www.youtube.com/watch?v=mJlbJO-WDuQ)

## Basic Server Setup

### 1. Update and Upgrade Ubuntu

Run updates:
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Set Up Automatic Security Updates

Install the unattended-upgrades package:
```bash
sudo apt install unattended-upgrades
```
Enable automatic updates:
```bash
sudo dpkg-reconfigure -plow unattended-upgrades
```

### 3. Configure Swap Space (Low memory systems)

If your server has limited RAM, setting up swap space can improve performance:
```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### 4. Set Up a Basic Firewall

Install UFW (Uncomplicated Firewall):
```bash
sudo apt install -y ufw
```
Allow SSH connections:
```bash
sudo ufw allow OpenSSH
```
Enable the firewall:
```bash
sudo ufw enable
```
Verify status:
```bash
sudo ufw status
```

### 5. Create a New User

Create a new user and add that user to the `sudo` group:
```bash
adduser [username]
usermod -aG sudo [username]
```

### 6. Secure SSH Access

To enhance security, you can disable root login via SSH and change the default SSH port:

#### Edit the SSH configuration file

```bash
sudo nano /etc/ssh/sshd_config
```
- Change `PermitRootLogin` to `no` 
- Add `AllowUsers` and set to `[username]` (e.g., `AllowUsers [username]`
- Change the default port (e.g., `Port 2200`)
- Exit and Save, `Ctrl-X, Y, Enter`

#### Apply the changes

```bash
sudo systemctl restart ssh
```

### 7. Install Fail2Ban

Fail2Ban is a security tool designed to protect Linux systems from brute force attacks. It works by monitoring log files for patterns of repeated failed login attempts and other suspicious activities. When it detects such behavior, Fail2Ban automatically blocks the offending IP addresses by modifying firewall rules. This helps to prevent unauthorized access to your system.

#### Typical Use Cases

- **SSH Protection**: Prevent brute force attacks on SSH by banning IP addresses that exhibit multiple failed login attempts.
- **Web Server Protection**: Protect web applications from repeated unauthorized access attempts, such as those targeting admin login pages.
- **Email Server Protection**: Guard against repeated failed login attempts on mail servers, helping to prevent email account hijacking.
- **FTP Protection**: Secure FTP services from unauthorized access attempts.

#### Basic Configuration

To set up Fail2Ban for SSH protection, you can follow these steps:

1. **Install Fail2Ban**:
   ```bash
   sudo apt-get install fail2ban
   ```

2. **Create a Local Configuration File**:
   Copy the default configuration file to a local configuration file for customization:
   ```bash
   sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
   ```
   
3. **Enable the service**:
    ```bash
    sudo systemctl enable fail2ban
    sudo systemctl start fail2ban
    ```

3. **Edit the Local Configuration File**:
   Open the `jail.local` file for editing:
   ```bash
   sudo nano /etc/fail2ban/jail.local
   ```

   Find the `[sshd]` section and configure it as needed. For example:
   ```ini
   [sshd]
   enabled  = true
   port     = ssh
   filter   = sshd
   logpath  = /var/log/auth.log
   maxretry = 5
   ```

4. **Restart Fail2Ban**:
   Apply the changes by restarting the Fail2Ban service:
   ```bash
   sudo systemctl restart fail2ban
   ```

5. **Check the Status**:
   Verify that Fail2Ban is running and monitoring your SSH service:
   ```bash
   sudo fail2ban-client status sshd
   ```

#### Monitoring and Managing Fail2Ban

- **View Banned IPs**:
  ```bash
  sudo fail2ban-client status sshd
  ```

- **Unban an IP**:
  ```bash
  sudo fail2ban-client set sshd unbanip <IP_ADDRESS>
  ```

## Verify Everything Is Working

Check system status:
```bash
systemctl status
```
Review UFW status:
```bash
sudo ufw status verbose
```

By following these steps, you will have a secure and efficient Ubuntu server setup, ready for further customization.