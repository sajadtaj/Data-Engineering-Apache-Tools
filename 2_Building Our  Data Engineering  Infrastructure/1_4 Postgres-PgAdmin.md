## ۱. ساختار پروژه

ابتدا ساختار دایرکتوری را مشخص می‌کنیم:

```
project-directory/
│── docker-compose.yml
│── .env
├── README.md
├── .gitignore
├── db1/
├── db2/
└── db3/
```

### **۲. ایجاد فایل `.env`**

یک فایل **`.env`** در مسیر **`project-directory`** ایجاد کنید و مقادیر زیر را در آن قرار دهید:

```ini
# اطلاعات دیتابیس اول
POSTGRES_DB_1=database1
POSTGRES_USER_1=ali
POSTGRES_PASSWORD_1=ila

# اطلاعات دیتابیس دوم
POSTGRES_DB_2=database2
POSTGRES_USER_2=sara
POSTGRES_PASSWORD_2=aras

# اطلاعات دیتابیس سوم
POSTGRES_DB_3=database3
POSTGRES_USER_3=dara
POSTGRES_PASSWORD_3=arad

# پورت‌های PostgreSQL
POSTGRES_PORT_1=5433
POSTGRES_PORT_2=5434
POSTGRES_PORT_3=5435

# تنظیمات pgAdmin
PGADMIN_PORT=5050
PGADMIN_DEFAULT_EMAIL=sajadtajco@gmail.com
PGADMIN_DEFAULT_PASSWORD=adminpass

```

### **۳. ایجاد فایل `docker-compose.yml`**

حالا فایل **`docker-compose.yml`** را در همان مسیر ایجاد کنید:

```yaml
version: '3.8'

services:

  postgres1:
    image: postgres:13
    container_name: postgres_db1
    restart: always
    environment:
      POSTGRES_DB: ${POSTGRES_DB_1}
      POSTGRES_USER: ${POSTGRES_USER_1}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD_1}
    ports:
      - "${POSTGRES_PORT_1}:5432"
    volumes:
      - ./db1:/var/lib/postgresql/data
      - Pg_data1:/var/lib/postgresql/data

    healthcheck:
      test: ["CMD", "pg_isready", "-U", "${POSTGRES_USER_1}"]
      interval: 10s
      retries: 5
      start_period: 5s
    networks:
      - main_network

  postgres2:
    image: postgres:13
    container_name: postgres_db2
    restart: always
    environment:
      POSTGRES_DB: ${POSTGRES_DB_2}
      POSTGRES_USER: ${POSTGRES_USER_2}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD_2}
    ports:
      - "${POSTGRES_PORT_2}:5432"
    volumes:
      - ./db2:/var/lib/postgresql/data
      - Pg_data2:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "${POSTGRES_USER_2}"]
      interval: 10s
      retries: 5
      start_period: 5s
    networks:
      - main_network
    
  postgres3:
    image: postgres:13
    container_name: postgres_db3
    restart: always
    environment:
      POSTGRES_DB: ${POSTGRES_DB_3}
      POSTGRES_USER: ${POSTGRES_USER_3}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD_3}
    ports:
      - "${POSTGRES_PORT_3}:5432"
    volumes:
      - ./db3:/var/lib/postgresql/data
      - Pg_data3:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "${POSTGRES_USER_3}"]
      interval: 10s
      retries: 5
      start_period: 5s
    networks:
      - main_network

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: ${PGADMIN_DEFAULT_EMAIL}
      PGADMIN_DEFAULT_PASSWORD: ${PGADMIN_DEFAULT_PASSWORD}
    ports:
      - "${PGADMIN_PORT}:80"
    depends_on:
      postgres1:
        condition: service_healthy
      postgres2:
        condition: service_healthy
      postgres3:
        condition: service_healthy
    networks:
      - main_network

volumes:
  Pg_data1:
  Pg_data2:
  Pg_data3:
  
networks:
  main_network:
  
```

### **۴. اجرای کانتینرها**

پس از ایجاد فایل‌ها، در مسیر پروژه، دستور زیر را اجرا کنید:

```sh
docker compose up -d
```

### **۵. بررسی اجرای سرویس‌ها**

برای مشاهده وضعیت کانتینرها:

```sh
docker ps
```

output:

    sajad@TAJ:~/All Project/Docker PostgreSQL$ docker ps
    CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS                    PORTS                           NAMES
    309ac265f59a   dpage/pgadmin4   "/entrypoint.sh"         29 seconds ago   Up 17 seconds             443/tcp, 0.0.0.0:5050->80/tcp   pgadmin
    9f71b4aa9e5d   postgres:13      "docker-entrypoint.s…"   29 seconds ago   Up 28 seconds (healthy)   0.0.0.0:5434->5432/tcp          postgres_db2
    c69db088f932   postgres:13      "docker-entrypoint.s…"   29 seconds ago   Up 28 seconds (healthy)   0.0.0.0:5435->5432/tcp          postgres_db3
    acda1711d35f   postgres:13      "docker-entrypoint.s…"   29 seconds ago   Up 28 seconds (healthy)   0.0.0.0:5433->5432/tcp          postgres_db1
    sajad@TAJ:~/All Project/Docker PostgreSQL$

اگر همه سرویس‌ها بالا باشند، می‌توانید **pgAdmin** را با باز کردن این آدرس در مرورگر مشاهده کنید:

```
http://localhost:5050
```

### ۶. دسترسی به دیتابیس اول

```sql
docker exec -it postgres_db1 psql -U ali -d database1
```

output:

    sajad@TAJ:~/All Project/Docker PostgreSQL$ docker exec -it postgres_db1 psql -U ali -d database1
    psql (13.16 (Debian 13.16-1.pgdg120+1))
    Type "help" for help.

    database1=#

### 7. افزودن .gitignore

+ برای عدم ارسال فایل `.env`

```ssh
touch .gitignore
```

محتوای آن:

    db*
    .env
    .gitignore
    .vscode
postgres

    postgres@Amir:~/test/project$ docker images |grep postgres
    postgres                                        16             b781f3a53e61   5 months ago    432MB

    postgres@Amir:~/test/project$ docker ps
    CONTAINER ID   IMAGE            COMMAND                  CREATED              STATUS                        PORTS                           NAMES
    01986bea8f23   postgres:16      "docker-entrypoint.s…"   About a minute ago   Up About a minute (healthy)   0.0.0.0:5432->5432/tcp          PDb1
    bc4d5eb5eeca   postgres:16      "docker-entrypoint.s…"   About a minute ago   Up About a minute (healthy)   0.0.0.0:5433->5432/tcp          PDb2
    a34d5tyb5lcv   postgres:16      "docker-entrypoint.s…"   About a minute ago   Up About a minute (healthy)   0.0.0.0:5434->5432/tcp          PDb2
