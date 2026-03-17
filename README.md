# ENGSE207 Software Architecture

# Final Lab - Set 1: Microservices + HTTPS + Lightweight Logging


## สมาชิกในกลุ่ม


67543210055-9 | ณัฐกิตต์ แก้วคำยศ   | Backend

67543210064-1 | วรวิทย์ สุวรรณ   | Frontend

---

# ภาพรวมของระบบ

### Final Lab Set 1 เป็นการพัฒนาระบบ Task Board แบบ Microservices โดยเน้นหัวข้อสำคัญดังนี้

- การทำงานแบบแยก service

- การใช้ Nginx เป็น API Gateway

- การเปิดใช้งาน HTTPS ด้วย Self-Signed Certificate

- การจัดเก็บ log แบบ Lightweight Logging ผ่าน Log Service

- การเชื่อมต่อ Frontend กับ Backend ผ่าน HTTPS

- งานชุดนี้ ไม่มี Register และใช้เฉพาะ Seed Users ที่กำหนดไว้ในฐานข้อมูล

---

## วัตถุประสงค์

- เพื่อเรียนรู้การออกแบบระบบแบบแยกส่วน (modular architecture)

- เพื่อเข้าใจ flow การทำงานของ security (HTTPS + JWT)

- เพื่อสามารถ deploy ระบบที่ใช้งานได้จริงใน environment จำลอง

## Architecture Diagram

```
Browser / Postman
       │
       │ HTTPS :443  (HTTP :80 redirect → HTTPS)
       ▼
┌─────────────────────────────────────────────────────────────┐
│  Nginx (API Gateway + TLS Termination + Rate Limiter)       │
│                                                             │
│  /api/auth/*   → auth-service:3001                          │
│  /api/tasks/*  → task-service:3002   [JWT required]         │
│  /api/logs/*   → log-service:3003    [JWT required]         │
│  /             → frontend:80                                │
└───────┬────────────────┬──────────────────┬─────────────────┘
        │                │                  │
        ▼                ▼                  ▼
┌──────────────┐ ┌───────────────┐ ┌──────────────────┐
│ Auth Service │ │ Task Service  │ │ Log Service      │
│   :3001      │ │   :3002       │ │   :3003          │
└──────┬───────┘ └───────┬───────┘ └──────────────────┘
       └────────┬─────────┘
                ▼
     ┌─────────────────────┐
     │  PostgreSQL          │
     │  • users table       │
     │  • tasks table       │
     │  • logs  table       │
     └─────────────────────┘
```

---

## โครงสร้าง Repository

```
final-lab-set1/
├── README.md
├── docker-compose.yml
├── TEAM_SPLIT.md
├──INDIVIDUAL_REPORT_67543210055-9.md
├──INDIVIDUAL_REPORT_66543210064-1.md
├── .env.example
├── .gitignore
│
├── nginx/
│   ├── nginx.conf              ← HTTPS + reverse proxy config
│   ├── Dockerfile
│   └── certs/                  ← Self-signed cert (generate ด้วย script)
│       ├── cert.pem
│       └── key.pem
│
├── frontend/
│   ├── Dockerfile
│   ├── index.html              ← Task Board UI (Login + CRUD Tasks + JWT inspector)
│   └── logs.html               ← Log Dashboard (ดึงจาก /api/logs)
│
├── auth-service/
│   ├── Dockerfile
│   ├── package.json
│   └── src/
│       ├── index.js
│       ├── routes/auth.js
│       ├── middleware/jwtUtils.js
│       └── db/db.js
│
├── task-service/
│   ├── Dockerfile
│   ├── package.json
│   └── src/
│       ├── index.js
│       ├── routes/tasks.js
│       ├── middleware/
│       │   ├── authMiddleware.js
│       │   └── jwtUtils.js
│       └── db/db.js
│
├── log-service/
│   ├── Dockerfile
│   ├── package.json
│   └── src/
│       └── index.js
│
├── db/
│   └── init.sql                ← Schema + Seed Users ทั้งหมด
│
├── scripts/
│   └── gen-certs.sh            ← สร้าง self-signed cert
│
└── screenshots/
    ├── 01_docker_running.png
    ├── 02_https_browser.png
    ├── 03_login_success.png
    ├── 04_login_fail.png
    ├── 05_create_task.png
    ├── 06_get_tasks.png
    ├── 07_update_task.png
    ├── 08_delete_task.png
    ├── 09_no_jwt_401.png
    ├── 10_logs_api.png
    ├── 10_logs_api_Admin.png
    ├── 11_rate_limit.png
    └── 12_frontend_screenshot.png
```

---

## วิธีสร้าง Certificate และรันระบบด้วย Docker Compose

### 1. Clone Repository

```bash
git clone <https://github.com/WoravitSuwan/final-lab-set1>
cd final-lab-set1
```

### 2. สร้าง .env

```bash
cp .env.example .env
```

### 3. สร้าง Self-Signed Certificate

```bash
chmod +x scripts/gen-certs.sh
./scripts/gen-certs.sh
```

### 4. รันระบบด้วย Docker

```bash
docker compose up --build
```

### 5. Reset ฐานข้อมูล (ถ้าต้องการเริ่มใหม่)

```bash
docker compose down -v
docker compose up --build
```

---

## Users สำหรับทดสอบ

| Username | Email           | Password  | Role   |
| -------- | --------------- | --------- | ------ |
| alice    | alice@lab.local | alice123  | member |
| bob      | bob@lab.local   | bob456    | member |
| admin    | admin@lab.local | adminpass | admin  |

**วิธีสร้าง bcrypt hash:**

```bash
npm install bcryptjs
node -e "const b=require('bcryptjs'); console.log(b.hashSync('alice123',10))"
node -e "const b=require('bcryptjs'); console.log(b.hashSync('bob456',10))"
node -e "const b=require('bcryptjs'); console.log(b.hashSync('adminpass',10))"
```

จากนั้นนำค่าที่ได้ไปแทนในไฟล์ db/init.sql

docker compose down -v

docker compose up --build

---

## วิธีทดสอบ API
วิธีทดสอบ API (T1 - T11) ด้วย Postman
ก่อนเริ่มทดสอบ ให้ตั้งค่า Postman ดังนี้:

ไปที่ Settings > แถบ General

ตั้งค่า SSL certificate verification เป็น OFF 🔴

### ส่วนที่ 1: Authentication & Security
T1 & T2: HTTPS & Redirect
Test: เข้าใช้งานผ่าน https://localhost

Expected: หน้าเว็บต้องโหลดได้ (กด Advanced > Proceed) และหากเข้าผ่าน http ระบบต้อง Redirect มาที่ https อัตโนมัติ

T3: Login Success (Member)
Method: POST | URL: https://localhost/api/auth/login

Body (JSON):
```bash
JSON
{ "email": "alice@lab.local", "password": "alice123" }
Note: ก๊อปปี้ค่า token ที่ได้ เพื่อนำไปใช้ในแถบ Auth > Bearer Token สำหรับข้อถัดไป
```
T4: Login Failed
Body (JSON): { "email": "alice@lab.local", "password": "wrong" }

Expected Status: 401 Unauthorized

### ส่วนที่ 2: Task Management (CRUD)
ทุกข้อต้องใส่ Bearer Token ของ Alice ในแถบ Authorization

T5: Create Task
Method: POST | URL: https://localhost/api/tasks/

Body (JSON):
```bash
JSON
{
  "title": "ทดสอบระบบ Final Lab",
  "description": "ส่งงานจาก Postman",
  "priority": "high"
}
```
Expected Status: 201 Created (จดจำเลข id ที่ได้ไว้)

T6: Get Tasks
Method: GET | URL: https://localhost/api/tasks/

Expected Status: 200 OK (เห็นรายการงานที่สร้างไว้)

T7: Update Task Status
Method: PUT | URL: https://localhost/api/tasks/{id}

Body (JSON): 
```bash
{ "status": "IN_PROGRESS" }
```

Expected Status: 200 OK

T8: Delete Task
Method: DELETE | URL: https://localhost/api/tasks/{id}

Expected Status: 200 OK หรือ 204 No Content

### ส่วนที่ 3: Authorization & Rate Limiting
T9: Access Without Token
Method: GET | URL: https://localhost/api/tasks/

Auth: No Auth

Expected Status: 401 Unauthorized

T10: Role-Based Access (Logs)
T10A (Member): ใช้ Token ของ Alice เรียก GET /api/logs/ ➡️ Status: 403 Forbidden

T10B (Admin): Login ด้วย admin@lab.local / adminpass แล้วใช้ Token ใหม่เรียก GET /api/logs/ ➡️ Status: 200 OK

T11: Rate Limiting
Test: กดปุ่ม Send ใน Request Login ซ้ำๆ มากกว่า 5 ครั้งภายใน 1 นาที

Expected Status: 429 Too Many Requests

## อธิบาย HTTPS, JWT และ Logging

### HTTPS (ความปลอดภัยในการรับ-ส่งข้อมูล)
เปรียบเสมือนการส่งจดหมายที่ "ใส่ซองปิดผนึก" แทนการส่งกระดาษแผ่นเดียว

- Privacy: ข้อมูลที่ส่งระหว่าง Browser และ Nginx (เช่น รหัสผ่าน) จะถูกเข้ารหัสไว้ คนกลางที่แอบดักดูจะอ่านไม่ออก
- Redirection: ระบบถูกตั้งค่าให้ Port 80 (HTTP) ส่งต่อไปยัง Port 443 (HTTPS) เสมอ เพื่อบังคับให้ใช้ช่องทางที่ปลอดภัย
- Self-Signed Certificate: ในโปรเจกต์นี้เราสร้างใบรับรองขึ้นมาเองด้วย openssl เพื่อใช้ในการพัฒนา (Development) ทำให้ Browser ขึ้นเตือนว่าไม่รู้จักผู้ออกใบรับรอง แต่ในแง่การเข้ารหัสข้อมูลนั้นปลอดภัยตามมาตรฐาน
- Certificate สร้างด้วย `openssl` ผ่าน `scripts/gen-certs.sh`

### JWT (ระบบบัตรผ่านประตูอัจฉริยะ)
เปรียบเสมือน "สายรัดข้อมือ" ที่เราได้รับหลังจากลงทะเบียนหน้างานสำเร็จ

- Stateless Authentication: เมื่อ Login สำเร็จ Auth Service จะออก Token ให้ Server ไม่ต้องจำว่าใคร Login อยู่บ้าง แต่จะดูจาก "บัตรผ่าน" ที่ผู้ใช้ถือมา
- Data Inside: ภายใน Token มีข้อมูล (Payload) เช่น User ID และ Role (Admin/Member) บอกให้รู้ว่าผู้ถือบัตรนี้คือใครและมีสิทธิ์ทำอะไร
- Verification: ทุกครั้งที่เรียกใช้ Task Service หรือ Log Service ระบบจะใช้ authMiddleware ตรวจสอบความถูกต้องของ Token ก่อนอนุญาตให้ใช้งาน


### Logging (การบันทึกเหตุการณ์ของระบบ)
เปรียบเสมือน "กล้องวงจรปิด" หรือ "สมุดบันทึกหน้าประตู" ที่จดบันทึกทุกความเคลื่อนไหว

- Auth Service และ Task Service ส่ง log ไปที่ Log Service ผ่าน `POST /api/logs/internal` ภายใน Docker network
- Log Service เก็บลง PostgreSQL ตาราง `logs`
- `GET /api/logs/` เปิดให้เฉพาะ role `admin` เท่านั้น
- Log events ที่จะบันทึก: `LOGIN_SUCCESS`, `LOGIN_FAILED`, `JWT_INVALID`, `TASK_CREATED`, `TASK_DELETED`

## สรุป
- HTTPS ป้องกันการดักฟังข้อมูลระหว่างทาง

- JWT ใช้ยืนยันตัวตนและสิทธิ์การเข้าถึงโดยไม่ต้องส่งรหัสผ่านบ่อย ๆ

- Logging ช่วยให้ผู้ดูแลระบบรู้ว่าเกิดอะไรขึ้นบ้างใน Microservices ต่าง ๆ

---

## Known Limitations

- การเชื่อมต่อระหว่าง Service (เช่น การส่ง Log) ทำผ่าน Docker Network ภายในเครื่องเท่านั้น ไม่รองรับการเชื่อมต่อจากภายนอกโดยตรง
- ไม่มีระบบ Register ใช้ Seed Users เท่านั้น
- ใช้ Shared Database 1 ฐานข้อมูลสำหรับทุก Service
ในทางปฏิบัติ Microservices ควรแยกฐานข้อมูลกัน แต่ในโปรเจกต์นี้มีการใช้ PostgreSQL ร่วมกันหนึ่งตัวเพื่อความสะดวกในการจัดการทรัพยากร
