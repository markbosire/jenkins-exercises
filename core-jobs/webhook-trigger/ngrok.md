# **NGROK Guide: Exposing Local Jenkins Server to the Web**

## **Overview**
This guide explains how to securely expose a Jenkins server running on `localhost` (or Vagrant VM) to the internet using **ngrok**, enabling:
- Remote access to Jenkins without complex networking
- Webhook testing from GitHub/GitLab/Bitbucket
- Temporary public URLs for demos/testing

---

## **Prerequisites**
- Jenkins running locally (`http://localhost:8080` or similar)
- [ngrok account](https://ngrok.com/) (free tier available)
- [ngrok installed](https://ngrok.com/download) on your machine/Vagrant host

---

## **Step 1: Install ngrok**
### **Linux/macOS**
```bash
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null
echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok
```

### **Windows**
Download from [ngrok.com/download](https://ngrok.com/download) and add to PATH.

### **Verify Installation**
```bash
ngrok --version
# Should show version (e.g., ngrok 3.4.1)
```

---

## **Step 2: Authenticate ngrok**
1. Get your auth token from [ngrok dashboard](https://dashboard.ngrok.com/get-started/your-authtoken)
2. Run:
```bash
ngrok config add-authtoken YOUR_AUTH_TOKEN
```

---

## **Step 3: Expose Jenkins**
### **Basic Tunnel (HTTP)**
```bash
ngrok http 8080
```
- Replace `8080` with your Jenkins port if different.

### **Secure Tunnel (HTTPS)**
```bash
ngrok http https://localhost:8080
```

### **With Custom Subdomain (Paid Plan)**
```bash
ngrok http 8080 --subdomain=your-custom-name
```

---

## **Expected Output**
```
Forwarding  https://a1b2-34-56-78-90.ngrok.io -> http://localhost:8080
```
- Copy the `https://*.ngrok.io` URL to access Jenkins remotely.

---

## **Step 4: Configure Jenkins (Optional)**
1. Go to **Manage Jenkins > Configure System**
2. Set **Jenkins URL** to your ngrok URL (e.g., `https://a1b2-34-56-78-90.ngrok.io`)
3. Save to ensure webhooks/redirects work correctly.

---

## **Vagrant-Specific Notes**
If Jenkins runs in a Vagrant VM:
1. Forward the port in `Vagrantfile`:
   ```ruby
   config.vm.network "forwarded_port", guest: 8080, host: 8080
   ```
2. Run ngrok on the **host machine** (not inside Vagrant):
   ```bash
   ngrok http 8080
   ```

---

## **Security Considerations**
⚠️ **Temporary Use Only**: Free ngrok URLs expire after 2 hours.  
🔒 **For Production**:  
- Use `--basic-auth="user:pass"` for password protection:
  ```bash
  ngrok http 8080 --basic-auth="admin:yourpassword"
  ```
- Upgrade to a paid plan for fixed domains and IP whitelisting.

---

## **Troubleshooting**
| Issue | Solution |
|-------|----------|
| `502 Bad Gateway` | Ensure Jenkins is running locally on the forwarded port. |
| `ngrok not found` | Verify installation and PATH configuration. |
| `Tunnel session expired` | Re-run `ngrok http 8080` or upgrade for persistent URLs. |



