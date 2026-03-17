# 📑 INDIVIDUAL REPORT [67543210055-9]

## 👤 ข้อมูลผู้จัดทำ
- **ชื่อ-นามสกุล:** ณัฐกิตต์ แก้วคำยศ
- **รหัสนักศึกษา:** 67543210055-9
- **กลุ่ม:** Sec2-3
- **หน้าที่:** Backend Developer & Infrastructure Engineer

---

## 🛠️ 2. ส่วนที่รับผิดชอบ (Scopes of Responsibility)
รับผิดชอบการวางโครงสร้างพื้นฐาน (Infrastructure) และระบบหลังบ้าน (Backend) ทั้งหมด:
- **Nginx & Security:** ตั้งค่า HTTPS (SSL/TLS), Reverse Proxy และระบบ Rate Limiting
- **Authentication Service:** ระบบ Login, การสร้างและตรวจสอบ JWT Token
- **Task Management Service:** ระบบจัดการงาน (CRUD Operations) และระบบสิทธิ์เข้าถึงข้อมูล
- **Log Service:** ระบบรวมศูนย์บันทึกเหตุการณ์ (Centralized Logging) จาก Service อื่นๆ (ช่วยกันออกแบบและทำงานร่วมกัน)
- **Database Administration:** ออกแบบ Database Schema และการทำ Database Seeding (ช่วยกันทำ)
- **Containerization:** จัดการ Docker Compose, Dockerfiles และ Environment Configuration

---

## 💻 3. สิ่งที่ได้ดำเนินการด้วยตนเอง
- พัฒนา Shell Script (`gen-certs.sh`) สำหรับสร้าง Self-Signed Certificate โดยอัตโนมัติ
- พัฒนา `Auth Service` โดยใช้เทคนิค **Timing-safe comparison** เพื่อความปลอดภัยในการตรวจสอบรหัสผ่าน
- สร้าง Middleware สำหรับตรวจสอบสิทธิ์ **(RBAC)** ทำให้ Admin สามารถเห็นงานทั้งหมด แต่ Member เห็นเฉพาะงานของตนเอง
- ออกแบบระบบ **Internal Logging API** เพื่อให้ Service ต่างๆ ส่งข้อมูลมาเก็บที่ Log Service ผ่าน Docker Internal Network
- ตั้งค่า **Docker Healthcheck** เพื่อให้มั่นใจว่า Services จะเริ่มทำงานต่อเมื่อ Database พร้อมใช้งานเท่านั้น

---

## ⚠️ 4. ปัญหาที่พบและวิธีการแก้ไข
- **ปัญหาที่ 1: ความผิดพลาดในการสร้าง SSL Certificate บนสภาพแวดล้อม Windows**
  - *สาเหตุ:* การรันคำสั่ง openssl ผ่าน Git Bash บน Windows ประสบปัญหา "Path Conversion" โดย Git Bash พยายามแปลงค่าใน -subj (เช่น /C=TH) ให้กลายเป็น Path ของไฟล์ใน Windows ทำให้คำสั่งล้มเหลวและไม่สามารถสร้างไฟล์ cert.pem ได้
  - *แก้ไข:* ใช้ Environment Variable MSYS_NO_PATHCONV=1 นำหน้าคำสั่งรันสคริปต์ เพื่อปิดการทำงานของระบบแปลง Path อัตโนมัติ ทำให้ค่า Subject ถูกส่งไปยัง OpenSSL ในรูปแบบที่ถูกต้อง และสามารถสร้างใบรับรองได้สำเร็จ
- **ปัญหา 2: ระบบล้มเหลวเนื่องจากลำดับการเริ่มทำงานของ Containers (Race Condition)**
  - *สาเหตุ:* Microservices ต่างๆ พยายามเชื่อมต่อฐานข้อมูลทันทีที่เริ่มทำงาน แต่ในขณะนั้น PostgreSQL ยังตั้งค่าระบบไม่เสร็จสิ้น (Ready for connections) ส่งผลให้ Services อื่นๆ Crash และหยุดทำงาน
  - *แก้ไข:* เพิ่มการตั้งค่า healthcheck ในส่วนของฐานข้อมูลบนไฟล์ docker-compose.yml และใช้เงื่อนไข depends_on: condition: service_healthy เพื่อบังคับให้ Services อื่นๆ รอจนกว่าฐานข้อมูลจะพร้อมใช้งานจริง พร้อมทั้งเพิ่ม Retry Logic ภายในโค้ดเพื่อความคงทน (Resilience)

---

## 💡 5. สิ่งที่ได้เรียนรู้จากงานนี้
- **Microservices Separation:** ได้เรียนรู้การแยกส่วนการทำงานออกจากกันอย่างเด็ดขาด (Decoupling) ซึ่งช่วยให้สามารถพัฒนาระบบแต่ละส่วน (Auth, Task, Log) ได้อย่างอิสระ และจัดการทรัพยากรได้ดีขึ้น 
- **Stateless Authentication:** เข้าใจกลไกของ JWT ในการเป็นบัตรผ่านที่เก็บข้อมูลสิทธิ์ไว้ในตัว (Self-contained) ทำให้ Server ไม่ต้องแบกรับภาระในการจำสถานะการ Login (State) ซึ่งเหมาะสมมากกับระบบที่เป็น Microservices
- **HTTPS Termination:** ได้ลงมือตั้งค่า Nginx ให้ทำหน้าที่เป็นจุดรับส่งข้อมูลที่ปลอดภัย (Reverse Proxy & SSL Termination) และเรียนรู้การทำ TLS Hardening เบื้องต้น
- **Access Control Logic:** ได้ฝึกการเขียน Middleware เพื่อควบคุมสิทธิ์เข้าถึงข้อมูล (Authorization) โดยใช้ Role เป็นตัวกำหนด ซึ่งเป็นรากฐานสำคัญของระบบ Enterprise Application

---

## 🚀 6. แนวทางที่ต้องการพัฒนาต่อใน Set 2
- **Advanced Authentication Security :** พัฒนาระบบ Refresh Token เพื่อให้ผู้ใช้ไม่ต้อง Login บ่อยเกินไป และเพิ่มระบบ Token Blacklisting (โดยใช้ Redis) สำหรับการ Logout เพื่อทำลาย Token ทันที เพิ่มความปลอดภัยในกรณีที่ Token รั่วไหล
- **Infrastructure & Deployment Hardening:**  ประยุกต์ใช้ CI/CD Pipeline (เช่น GitHub Actions) เพื่อทำ Automated Testing และ Linting ทุกครั้งที่มีการแก้ไขโค้ด
ศึกษาการทำ Container Orchestration เพื่อ Auto-recovery ของ Services
