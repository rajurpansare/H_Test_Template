Architecture (simple understanding)
EC2 setup + installation
Project structure + Dockerfiles
Docker Compose setup + run
GitHub push from EC2
Flowchart
🌐 1. Architecture (What you're building)

Think of this like a tiny restaurant:

🧑‍🍳 User Service → handles users
📦 Product Service → handles products
🚪 Gateway Service → entry point (like receptionist)

All are inside containers, talking over a private Docker network.

🖥️ 2. EC2 Setup (Step-by-step commands)
🔹 Connect to EC2
ssh -i your-key.pem ubuntu@your-ec2-public-ip
🔹 Update system
sudo apt update -y
sudo apt upgrade -y
🔹 Install Docker
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
🔹 Give permission to ubuntu user
sudo usermod -aG docker ubuntu
newgrp docker
🔹 Install Docker Compose
sudo apt install docker-compose -y
🔹 Install Git
sudo apt install git -y
📁 3. Project Structure (Create on EC2)
mkdir microservices-app
cd microservices-app

mkdir user-service product-service gateway-service
🧑‍💻 4. Sample Node.js Code (Minimal Working)
🔹 user-service/app.js
nano user-service/app.js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('User Service Running');
});

app.listen(3000, () => console.log('User Service on 3000'));
🔹 product-service/app.js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Product Service Running');
});

app.listen(3001, () => console.log('Product Service on 3001'));
🔹 gateway-service/app.js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Gateway Service Running');
});

app.listen(3003, () => console.log('Gateway Service on 3003'));
🔹 package.json (copy in each service)
nano user-service/package.json
{
  "name": "service",
  "version": "1.0.0",
  "main": "app.js",
  "dependencies": {
    "express": "^4.18.2"
  }
}

👉 Copy same file to all services:

cp user-service/package.json product-service/
cp user-service/package.json gateway-service/
🐳 5. Dockerfiles (Important)
🔹 user-service/Dockerfile
FROM node:18

WORKDIR /app

COPY package.json .
RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "app.js"]
🔹 product-service/Dockerfile

(Change port only)

EXPOSE 3001
🔹 gateway-service/Dockerfile
EXPOSE 3003
🧩 6. docker-compose.yml
nano docker-compose.yml
version: '3'

services:
  user-service:
    build: ./user-service
    ports:
      - "3000:3000"
    networks:
      - app-network

  product-service:
    build: ./product-service
    ports:
      - "3001:3001"
    networks:
      - app-network

  gateway-service:
    build: ./gateway-service
    ports:
      - "3003:3003"
    networks:
      - app-network

networks:
  app-network:
▶️ 7. Run Application
docker-compose up --build -d
🔹 Verify running containers
docker ps
🔹 Test in browser
http://<EC2-IP>:3000
http://<EC2-IP>:3001
http://<EC2-IP>:3003

👉 Don’t forget EC2 Security Group:

Allow ports: 3000, 3001, 3003
📘 8. README.md
nano README.md
# Microservices Docker App

## Setup
- Install Docker & Docker Compose
- Run:
  docker-compose up --build

## Services
- User: localhost:3000
- Product: localhost:3001
- Gateway: localhost:3003

## Troubleshooting
- Check logs:
  docker-compose logs
- Restart:
  docker-compose down && docker-compose up --build
📦 9. Push to GitHub from EC2
🔹 Configure Git
git config --global user.name "YourName"
git config --global user.email "your-email@example.com"
🔹 Initialize repo
git init
git add .
git commit -m "Initial commit - microservices docker app"
🔹 Create repo on GitHub

Then:

git remote add origin https://github.com/your-username/microservices-app.git
git branch -M main
git push -u origin main

👉 If password fails → use Personal Access Token

🔁 10. ZIP Submission
cd ..
zip -r submission.zip microservices-app
🔄 11. Flowchart (Architecture Flow)
            User Request
                 |
                 v
        +------------------+
        |  Gateway Service |
        |     (3003)       |
        +------------------+
           |            |
           v            v
+----------------+  +------------------+
| User Service   |  | Product Service  |
|   (3000)       |  |    (3001)        |
+----------------+  +------------------+

         All services connected via
             Docker Network
✅ Final Checklist

✔ Dockerfiles created
✔ docker-compose working
✔ Services accessible
✔ GitHub repo pushed
✔ README added
✔ ZIP ready
