# MERN TODO List Application

This is a Cloud-Based TODO List Application developed using the MERN Stack (MongoDB, Express.js, React.js, Node.js). The application allows users to efficiently manage daily tasks by adding, updating, completing, and deleting tasks. The project is deployed on AWS EC2 and uses MongoDB Atlas as the cloud database.

---

# Features

- Add new tasks
- Update existing tasks
- Mark tasks as completed
- Delete tasks
- Store task data in MongoDB Atlas
- Cloud deployment using AWS EC2
- Backend process management using PM2

---

# Technologies Used

| Technology | Purpose |
|---|---|
| React.js | Frontend UI |
| Node.js | Backend Runtime |
| Express.js | Backend Framework |
| MongoDB Atlas | Cloud Database |
| Mongoose | MongoDB ODM |
| AWS EC2 | Cloud Hosting |
| PM2 | Node.js Process Manager |
| Git & GitHub | Version Control |

---

# Project Architecture

```text
Client Browser
      ↓
React Frontend
      ↓
Express Backend API
      ↓
MongoDB Atlas Database
```

---

# Prerequisites

Before running the application, ensure the following are installed:

- Node.js
- npm
- Git
- AWS Account
- MongoDB Atlas Account

---

# Clone the Repository

```bash
git clone https://github.com/AtharvaKulkarniIT/mern-todo-app.git
```

---

# Open the Project

```bash
cd mern-todo-app/TODO
```

---

# Push Project to Your GitHub Repository

## Initialize Git

```bash
git init
```

## Add Remote Repository

```bash
git remote add origin YOUR_GITHUB_REPOSITORY_URL
```

Example:

```bash
git remote add origin https://github.com/USERNAME/todo-mern.git
```

## Add Files

```bash
git add .
```

## Commit Changes

```bash
git commit -m "Initial Commit"
```

## Push to GitHub

```bash
git branch -M main
git push -u origin main
```

---

# Backend Setup

## Navigate to Backend

```bash
cd todo_backend
```

## Install Dependencies

```bash
npm install
```

## Install dotenv

```bash
npm install dotenv
```

---

# MongoDB Atlas Setup

## Step 1: Create MongoDB Atlas Account

Open:

https://www.mongodb.com/cloud/atlas

Create account and login.

---

## Step 2: Create Cluster

- Click Create Cluster
- Select Free Tier
- Select AWS Provider
- Create Cluster

---

## Step 3: Create Database User

Go to:

Database Access → Add New Database User

Create:
- Username
- Password

Example:

```text
Username: todo_user
Password: Todo@123
```

IMPORTANT:

Replace special characters in password.

| Character | Replace |
|---|---|
| @ | %40 |
| # | %23 |

Example:

```text
Todo@123
```

becomes:

```text
Todo%40123
```

---

## Step 4: Add Network Access

Go to:

Network Access → Add IP Address

Add:

```text
0.0.0.0/0
```

This allows AWS EC2 and local machine access.

---

## Step 5: Get MongoDB Connection URI

Click:

Connect → Drivers

Copy the URI:

```text
mongodb+srv://USERNAME:PASSWORD@cluster0.xxxxx.mongodb.net/Todo?retryWrites=true&w=majority
```

---

# Environment Variable Setup

Create `.env` file inside `todo_backend`

```env
PORT=5003
MONGO_URI=mongodb+srv://USERNAME:PASSWORD@cluster0.xxxxx.mongodb.net/Todo?retryWrites=true&w=majority
```

Example:

```env
PORT=5003
MONGO_URI=mongodb+srv://todo_user:Todo%40123@cluster0.dm6pjbv.mongodb.net/Todo?retryWrites=true&w=majority
```

---

# Backend Server Configuration

Update `server.js`

```js
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const path = require('path');
const TodoModel = require('./models/Todo');
require('dotenv').config();

const app = express();

app.use(cors());
app.use(express.json());

mongoose.connect(process.env.MONGO_URI)
.then(() => {
    console.log('MongoDB connected');
})
.catch((err) => {
    console.log(err);
});

app.post('/add', (req, res) => {
    const { task } = req.body;

    TodoModel.create({ task })
        .then(result => res.json(result))
        .catch(err => console.log(err));
});

app.get('/get', (req, res) => {
    TodoModel.find()
        .then(result => res.json(result))
        .catch(err => console.log(err));
});

app.put('/edit/:id', (req, res) => {
    const { id } = req.params;

    TodoModel.findByIdAndUpdate(id, { done: true }, { new: true })
        .then(result => res.json(result))
        .catch(err => res.json(err));
});

app.put('/update/:id', (req, res) => {
    const { id } = req.params;
    const { task } = req.body;

    TodoModel.findByIdAndUpdate(id, { task: task })
        .then(result => res.json(result))
        .catch(err => res.json(err));
});

app.delete('/delete/:id', (req, res) => {
    const { id } = req.params;

    TodoModel.findByIdAndDelete({ _id: id })
        .then(result => res.json(result))
        .catch(err => res.json(err));
});

app.use(express.static(path.join(__dirname, '../todo_frontend/build')));

app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname, '../todo_frontend/build/index.html'));
});

app.listen(5003, () => {
    console.log('Server listening on port: 5003');
});
```

---

# Run Backend Locally

```bash
npm start
```

OR

```bash
npm run dev
```

Expected Output:

```text
MongoDB connected
Server listening on port: 5003
```

---

# Frontend Setup

## Navigate to Frontend

```bash
cd ../todo_frontend
```

## Install Dependencies

```bash
npm install
```

---

# Frontend API Configuration

Replace API URL:

```js
http://localhost:5000
```

with:

```js
http://localhost:5003
```

---

# Run Frontend Locally

```bash
npm start
```

Frontend runs at:

```text
http://localhost:3000
```

---

# Frontend Production Build

```bash
npm run build
```

This creates:

```text
build/
```

folder.

---

# AWS EC2 Deployment

## Step 1: Launch EC2 Instance

Open AWS Console:

https://console.aws.amazon.com

Go to:

EC2 → Launch Instance

Choose:

| Configuration | Value |
|---|---|
| AMI | Ubuntu |
| Instance Type | t2.micro |
| Storage | 8GB |
| Key Pair | Create PEM File |

---

## Step 2: Configure Security Group

Add Inbound Rules:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | My IP |
| Custom TCP | 5003 | Anywhere |
| HTTP | 80 | Anywhere |
| HTTPS | 443 | Anywhere |

---

## Step 3: Connect to EC2

```bash
chmod 400 todo-key.pem
```

```bash
ssh -i todo-key.pem ubuntu@YOUR_PUBLIC_IP
```

---

## Step 4: Install NVM

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

```bash
source ~/.bashrc
```

---

## Step 5: Install Node.js

```bash
nvm install 22
```

```bash
nvm alias default 22
```

Verify installation:

```bash
node -v
npm -v
```

---

## Step 6: Clone Repository on EC2

```bash
git clone YOUR_GITHUB_REPOSITORY_URL
```

Example:

```bash
git clone https://github.com/USERNAME/todo-mern.git
```

---

## Step 7: Backend Setup on EC2

```bash
cd todo-mern/TODO/todo_backend
```

Install dependencies:

```bash
npm install
```

Create `.env`

```bash
nano .env
```

Add:

```env
PORT=5003
MONGO_URI=YOUR_MONGODB_ATLAS_URI
```

Save:
- CTRL + X
- Y
- ENTER

---

## Step 8: Frontend Setup on EC2

```bash
cd ../todo_frontend
```

Install dependencies:

```bash
npm install
```

Create frontend build:

```bash
npm run build
```

---

## Step 9: Install PM2

```bash
npm install -g pm2
```

---

## Step 10: Start Backend Server

```bash
cd ../todo_backend
```

```bash
pm2 start server.js --name backend
```

---

# PM2 Commands

## Check Running Processes

```bash
pm2 status
```

## View Logs

```bash
pm2 logs backend
```

## Restart Backend

```bash
pm2 restart backend
```

## Stop Backend

```bash
pm2 stop backend
```

## Delete Backend Process

```bash
pm2 delete backend
```

---

# Save PM2 Process

```bash
pm2 save
```

```bash
pm2 startup
```

Run the generated command.

---

# Final Deployment URL

```text
http://YOUR_PUBLIC_IP:5003
```

Example:

```text
http://13.60.28.168:5003
```

---

# Features of the Application

- Add Tasks
- Update Tasks
- Delete Tasks
- Mark Tasks Completed
- MongoDB Atlas Cloud Storage
- AWS EC2 Deployment
- Full MERN Stack Integration

---

# Common Errors and Solutions

## Cannot GET /

Cause:
Frontend build is not served properly.

Solution:

```js
app.use(express.static(path.join(__dirname, '../todo_frontend/build')));
```

---

## MongoDB Authentication Failed

Cause:
Special characters in password not encoded.

Replace:

```text
@
```

with:

```text
%40
```

---

## MongoDB Atlas IP Whitelist Error

Add:

```text
0.0.0.0/0
```

inside Atlas Network Access.

---

## Port Already in Use

Check running process:

```bash
lsof -i :5003
```

Kill process:

```bash
kill -9 PID
```

---

# Conclusion

Successfully deployed a Cloud-Based MERN TODO List Application using:

- React.js
- Node.js
- Express.js
- MongoDB Atlas
- AWS EC2
- PM2

The application supports complete CRUD operations and cloud-based deployment with secure database connectivity.
