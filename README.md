# Full Stack Flask Student Attendance App - Dockerized Deployment on AWS EC2

## **Prerequisites**
- **AWS EC2 Instance** (Ubuntu)
- **Docker & Docker Compose installed**
- **Security Group configured** (allow ports: `22`, `80`, `5000`, and `3306` if needed)

---

## **Step 1: Launch an EC2 Instance**
1. **Go to AWS EC2 Dashboard** → Click **Launch Instance**.
2. **Choose Ubuntu (Latest LTS version)**.
3. **Select an Instance Type** (e.g., `t2.micro` for free tier).
4. **Configure Security Group:**
    - Allow **SSH (22)** from **your IP**.
    - Allow **HTTP (80)** for web access.
    - Allow **Custom TCP (5000)** if accessing the Flask app directly.
    - Allow **MySQL (3306)** only if you need remote database access (optional).
5. **Launch & Connect to the instance** using SSH:
   ```sh
   ssh -i your-key.pem ubuntu@your-ec2-public-ip
   ```

---

## **Step 2: Install Docker & Docker Compose**
1. **Update the system & install dependencies:**
   ```sh
   sudo apt update && sudo apt upgrade -y
   sudo apt install -y docker.io docker-compose
   ```
2. **Enable Docker to start on boot:**
   ```sh
   sudo systemctl enable docker
   sudo systemctl start docker
   ```
3. **Add current user to the Docker group (optional, to run without sudo):**
   ```sh
   sudo usermod -aG docker $USER
   newgrp docker
   ```

---

## **Step 3: Project Clone**
### **Option 1: Clone from GitHub**
```sh
git clone https://github.com/mamunurrashid1010/fullstack-flask-student-attendance-app-dockerized.git
cd fullstack-flask-student-attendance-app-dockerized
```

---

## **Step 4: Build & Run Docker Containers**
1. **Navigate to the project directory:**
   ```sh
   cd fullstack-flask-student-attendance-app-dockerized
   ```
2. **Run Docker Compose:**
   ```sh
   docker-compose up -d --build
   ```
3. **Verify running containers:**
   ```sh
   docker ps
   ```

---

## **Step 5: Set Up Reverse Proxy (Optional, for Production)**
### **Using Nginx as a Reverse Proxy**
If you want to expose Flask on **port 80** (without `:5000` in the URL), install and configure **Nginx**:
1. **Install Nginx:**
   ```sh
   sudo apt install nginx -y
   ```
2. **Edit Nginx config:**
   ```sh
   sudo nano /etc/nginx/sites-available/flask
   ```
3. **Add this configuration:**
   ```nginx
   server {
       listen 80;
       server_name your-ec2-public-ip;

       location / {
           proxy_pass http://localhost:5000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       }
   }
   ```
4. **Enable the config and restart Nginx:**
   ```sh
   sudo ln -s /etc/nginx/sites-available/flask /etc/nginx/sites-enabled/
   sudo systemctl restart nginx
   ```

---

## **Step 6: Access the Application**
- **Check running logs:**
  ```sh
  docker-compose logs -f
  ```
- Open your browser and visit:
    - `http://your-ec2-public-ip:5000` (if using Flask directly)
    - `http://your-ec2-public-ip` (if using Nginx)

---

## **Step 7: Enable HTTPS (Optional)**
To secure your app, install **Certbot** and get an SSL certificate:
```sh
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com
```

---

## ** Done **
