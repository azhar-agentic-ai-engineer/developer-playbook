# 🚀 Next.js & FastAPI Applications → Docker Image → Docker Hub (Step by Step)

In this Guide / Video you will learn:

1. Create a Next.js application
2. Dockerize a Next.js application
3. Create a FastAPI backend
4. Dockerize a FastAPI application
5. Push Docker images to Docker Hub
6. Run both applications using Docker

---

## ✅ Prerequisites

- Node.js installed
- Python 3.9+ installed
- Docker Desktop installed and running
- Docker Hub account

---

# 🌐 PART 1: Next.js Application with Docker

## 📁 Step 1: Create a Next.js Application

```bash
npx create-next-app@latest nextjs-app
cd nextjs-app
```

Run locally:

```bash
npm run dev
```

Open in browser:

```
http://localhost:3000
```

---

## 🐳 Step 2: Create Dockerfile for Next.js

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
```

---

## 🔐 Step 3: Login to Docker Hub

```bash
docker login
```

---

## 🏗️ Step 4: Build Next.js Docker Image

```bash
docker build -t azharagenticai/todo_frontend .
```

---

## ▶️ Step 5: Run Next.js Container

```bash
docker run -p 3000:3000 azharagenticai/todo_frontend
```

---

## ⬆️ Step 6: Push Next.js Image to Docker Hub

```bash
docker push azharagenticai/todo_frontend
```

---

# ⚡ PART 2: FastAPI Application with Docker

## 📁 Step 1: Create FastAPI Project

```bash
mkdir fastapi-app
cd fastapi-app
```

Create `main.py`:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello from FastAPI with Docker"}
```

Create `requirements.txt`:

```txt
fastapi
uvicorn
```

---

## ▶️ Step 2: Run FastAPI Locally (Optional)

```bash
pip install -r requirements.txt
uvicorn main:app --reload
```

Open in browser:

```
http://localhost:8000
```

---

## 🐳 Step 3: Create Dockerfile for FastAPI

```dockerfile
# Use Python 3.12 slim image as base
FROM python:3.12-slim

# Set working directory
WORKDIR /app

# Copy requirements file
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy environment file
COPY .env .env

# Copy the application code
COPY app/ ./app/

# Expose port 8000
EXPOSE 8000

# Run the application
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## 🏗️ Step 4: Build FastAPI Docker Image

```bash
docker build -t azharagenticai/todo_backend .
```

---

## ▶️ Step 5: Run FastAPI Container

```bash
docker run -p 8000:8000 azharagenticai/todo_backend
```

---

## ⬆️ Step 6: Push FastAPI Image to Docker Hub

```bash
docker push azharagenticai/todo_backend
```

---

# 🔗 OPTIONAL: Run Both Containers Together

```bash
docker run -d -p 3000:3000 azharagenticai/todo_website
docker run -d -p 8000:8000 azharagenticai/todo_backend
```

---

## 🎉 Done!

Your **Next.js Frontend & FastAPI Backend** are now running using Docker 🚀

---

Learn • Build • Deploy
