🐳 Docker Compose

Docker Compose is used to define and run applications that consist of one or more Docker containers.

📌 What is Docker Compose?

Docker Compose allows you to define your application's services in a YAML file, usually:

docker-compose.yml


or:

compose.yml


Instead of running each container manually with multiple docker run commands, you can define everything in one file and start the entire application with:

docker compose up

🆚 Dockerfile vs Docker Compose
Dockerfile

A Dockerfile describes how to build a Docker image.

Dockerfile
    ↓
docker build
    ↓
Docker Image
    ↓
docker run
    ↓
Container

Docker Compose

A Compose file describes how to run multiple services together.

compose.yml
     ↓
┌───────────────┐
│   Frontend    │
│   Backend     │
│   Database    │
└───────────────┘
     ↓
docker compose up

Easy way to remember

Dockerfile = Build the image
Docker Compose = Run and manage the application/services

📁 Example Project

Suppose we have a full-stack application:

my-project/
│
├── docker-compose.yml
│
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   └── ...
│
└── backend/
    ├── Dockerfile
    ├── requirements.txt
    ├── main.py
    └── ...


Our application contains:

Next.js Frontend
       ↓
FastAPI Backend
       ↓
PostgreSQL Database


Instead of starting each container manually, Docker Compose can manage all three.

📝 Step 1: Create docker-compose.yml

Create this file in the root of your project:

docker-compose.yml


Example:

services:

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"

  backend:
    build: ./backend
    ports:
      - "8000:8000"

  database:
    image: postgres:16
    ports:
      - "5432:5432"

🔍 Understanding the File
services
services:


This defines the different services/containers that make up your application.

In our example:

frontend
backend
database

🌐 Frontend Service
frontend:
  build: ./frontend
  ports:
    - "3000:3000"

frontend

This is the service name.

frontend:


You can choose the name yourself.

build
build: ./frontend


This tells Docker Compose:

Build the image using the Dockerfile inside ./frontend.

So:

./frontend
     ↓
Dockerfile
     ↓
Docker Image
     ↓
Frontend Container

ports
ports:
  - "3000:3000"


This maps:

Host Port : Container Port
     ↓            ↓
    3000    :     3000


So you can access the Next.js application at:

http://localhost:3000

⚡ Backend Service
backend:
  build: ./backend
  ports:
    - "8000:8000"


Again:

build: ./backend


means:

Use the Dockerfile inside the backend directory to build the backend image.

The port mapping:

- "8000:8000"


allows you to access FastAPI through:

http://localhost:8000

🗄️ Database Service
database:
  image: postgres:16
  ports:
    - "5432:5432"


Notice something important:

We are using:

image: postgres:16


instead of:

build: ./database


Why?

Because PostgreSQL already has an official Docker image.

Docker can simply download it:

Docker Hub
    ↓
postgres:16
    ↓
PostgreSQL Container


We don't need to create our own Dockerfile for PostgreSQL in this example.

🚀 Step 2: Start the Application

From the directory containing docker-compose.yml:

docker compose up


Docker Compose will:

Read docker-compose.yml
        ↓
Build frontend
        ↓
Build backend
        ↓
Pull PostgreSQL image
        ↓
Create containers
        ↓
Start services

🚀 Run in Background

Use:

docker compose up -d


The -d means:

detached mode


Your terminal becomes available again while the containers continue running.

🔎 Step 3: Check Running Containers
docker compose ps


You should see your services:

frontend
backend
database


You can also use:

docker ps

📜 Step 4: View Logs

View logs for all services:

docker compose logs


Follow logs continuously:

docker compose logs -f


View logs for only the backend:

docker compose logs backend

🛑 Step 5: Stop the Application
docker compose down


This stops and removes the containers created by Compose.

🔄 Rebuild Containers

If you change your Dockerfile or dependencies, rebuild:

docker compose up --build


Or:

docker compose build
docker compose up

📦 Build Without Starting
docker compose build


This builds the images defined by your Compose file.

▶️ Start Existing Containers
docker compose start

⏹️ Stop Existing Containers
docker compose stop

🗑️ Remove Containers
docker compose down

🧹 Remove Containers and Volumes

Be careful with this command because volumes may contain persistent database data.

docker compose down -v

🔗 Service-to-Service Communication

One of the biggest benefits of Docker Compose is that services can communicate with each other.

For example:

frontend
    ↓
backend
    ↓
database


Inside the Docker Compose network, the backend can connect to PostgreSQL using the service name:

database


For example:

postgresql://user:password@database:5432/mydb


You generally do not use localhost to refer to another container.

Important

Inside the backend container:

localhost


means:

The backend container itself.

Whereas:

database


means:

The PostgreSQL service/container defined in Compose.

🔐 Environment Variables

You can also define environment variables:

services:

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://user:password@database:5432/mydb

  database:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb


For real projects, avoid committing sensitive passwords or API keys directly into the Compose file.

A .env file is commonly used instead.

💾 Volumes

Databases usually need persistent storage.

Without a volume, removing a database container can also remove the data stored inside it.

Example:

services:

  database:
    image: postgres:16
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:


Now Docker manages a persistent volume:

PostgreSQL Container
        ↓
postgres_data
        ↓
Persistent Database Data

🌐 Complete Example

A more realistic Compose file:

services:

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://user:password@database:5432/mydb
    depends_on:
      - database

  database:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:


The architecture becomes:

                    Docker Compose
                         │
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
   ┌─────────┐      ┌─────────┐     ┌──────────┐
   │ Next.js │ ───→ │ FastAPI │ ──→ │PostgreSQL│
   │  :3000  │      │  :8000  │     │  :5432   │
   └─────────┘      └─────────┘     └──────────┘
                         │
                         ↓
                    postgres_data


Start everything:

docker compose up -d


Stop everything:

docker compose down

⚡ Docker Compose Quick Reference
Task	Command
Start services	docker compose up
Start in background	docker compose up -d
Build images	docker compose build
Build + start	docker compose up --build
Stop services	docker compose stop
Stop + remove containers	docker compose down
Show services	docker compose ps
Show logs	docker compose logs
Follow logs	docker compose logs -f
Service logs	docker compose logs backend
Restart services	docker compose restart
Remove volumes	docker compose down -v
🧠 Docker Compose in One Picture
                   docker-compose.yml
                          │
                          ↓
              Defines application services
                          │
             ┌────────────┼────────────┐
             ↓            ↓            ↓
         Frontend      Backend      Database
             │            │            │
             ↓            ↓            ↓
        Dockerfile     Dockerfile    postgres:16
             │            │            │
             ↓            ↓            ↓
        Container      Container    Container
             └────────────┼────────────┘
                          ↓
                   Docker Network

🎯 Key Takeaways
Dockerfile → describes how to build an image.
docker-compose.yml → describes your application's services.
build: → build an image using a Dockerfile.
image: → use an existing Docker image.
ports: → map host ports to container ports.
environment: → provide environment variables.
volumes: → persist data.
depends_on: → define service startup dependencies.
Service names can be used for container-to-container communication.
docker compose up → start the application.
docker compose down → stop and remove the Compose application.
⭐ Remember

Dockerfile = How to build a container image.

Docker Compose = How multiple containers/services work together.

Dockerfile
    ↓
Build Image

docker-compose.yml
    ↓
Define Services
    ↓
Build / Pull Images
    ↓
Create Containers
    ↓
Network Services Together
    ↓
Run Application
