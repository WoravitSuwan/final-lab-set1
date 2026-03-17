# 📑 INDIVIDUAL REPORT [67543210064-1]

## 👤 ข้อมูลผู้จัดทำ
- **ชื่อ-นามสกุล:** วรวิทย์ สุวรรณ
- **รหัสนักศึกษา:** 67543210064-1
- **กลุ่ม:** Sec2-3
- **หน้าที่:** Frontend Developer & UI Integration

---

## 🛠️ 2. ส่วนที่รับผิดชอบ (Scopes of Responsibility)
รับผิดชอบส่วนการพัฒนาหน้าจอผู้ใช้งาน (User Interface) และการเชื่อมต่อระบบหน้าบ้านกับ Backend ทั้งหมด:
- **Frontend Development:** พัฒนาส่วนแสดงผลผู้ใช้งาน (Layout, Components) และจัดการ Flow การใช้งาน
- **API Integration:** เชื่อมต่อหน้าบ้านเข้ากับ Microservices ผ่านโปรโตคอล HTTPS
- **State Management:** จัดการสถานะการ Login และการจัดเก็บ JWT Token ไว้ใน LocalStorage
- **User Experience (UX):** ออกแบบหน้าจอให้เหมาะสมกับระดับสิทธิ์ (Role) ของผู้ใช้งาน เช่น Member และ Admin
- **Collaborative Work:** ร่วมออกแบบโครงสร้าง JSON ของ API และระบบฐานข้อมูลร่วมกับทีม Backend

---

## 💻 3. สิ่งที่ได้ดำเนินการด้วยตนเอง
- พัฒนาหน้า **Login Page** เพื่อรองรับการรับ Token จาก Auth Service และจัดการกรณีรหัสผ่านผิด
- พัฒนาหน้า **Task Management** ที่สามารถจัดการงาน (CRUD) ได้แบบ Dynamic ผ่านหน้าเว็บ
- เขียนระบบ **Authorization Header** เพื่อส่ง JWT Token แนบไปกับ Request ของ Task Service และ Log Service
- พัฒนาหน้า **Admin Dashboard** สำหรับแสดงข้อมูล Logs ทั้งหมด ซึ่งจะเข้าถึงได้เฉพาะผู้ที่มี Role เป็น Admin เท่านั้น
- พัฒนา **Protected Routes** เพื่อป้องกันไม่ให้ผู้ใช้เข้าถึงหน้าจัดการข้อมูลโดยไม่ได้ผ่านการยืนยันตัวตน

---

## ⚠️ 4. ปัญหาที่พบและวิธีการแก้ไข
- **ปัญหาที่ 1: ปัญหาเรื่อง CORS (Cross-Origin Resource Sharing)**
  - *สาเหตุ:* Browser ปฏิเสธการดึงข้อมูลจาก Backend เนื่องจาก Port และ Domain ไม่ตรงกับหน้าเว็บ Frontend ในช่วงเริ่มต้น
  - *แก้ไข:* ประสานงานกับฝ่าย Backend เพื่อตั้งค่าอนุญาต (Whitelist) ในส่วนของ Nginx Header ทำให้หน้าบ้านสามารถรับข้อมูลได้ปกติ
- **ปัญหาที่ 2: การจัดการ Token หลังการ Refresh หน้าจอ**
  - *สาเหตุ:* เมื่อผู้ใช้กด Refresh หน้าจอ ข้อมูลการ Login ในตัวแปรหายไป ทำให้ระบบเด้งกลับไปหน้า Login
  - *แก้ไข:* เปลี่ยนมาใช้การเก็บ Token ใน **LocalStorage** และเขียนฟังก์ชันตรวจสอบ Token ทันทีเมื่อแอปพลิเคชันเริ่มทำงาน (App Initialization)

---

## 💡 5. สิ่งที่ได้เรียนรู้จากงานนี้
- **Frontend-Backend Integration:** ได้เรียนรู้วิธีการเชื่อมต่อระบบที่แยกส่วนกัน (Decoupling) ผ่าน REST API และการจัดการ Data Flow
- **Security Logic:** เข้าใจการใช้ JWT ในการคัดกรองเนื้อหาหน้าเว็บ (Content Filtering) ตามสิทธิ์ของผู้ใช้งานแต่ละประเภท
- **Asynchronous Operations:** พัฒนาทักษะการดึงข้อมูลแบบ Asynchronous เพื่อให้หน้าเว็บทำงานได้ต่อเนื่องโดยไม่ต้องโหลดหน้าใหม่ทั้งหมด
- **HTTPS & SSL:** เรียนรู้วิธีการทำงานร่วมกับระบบที่มีการเข้ารหัสข้อมูล และการจัดการกับ Self-signed Certificate

---

## 🚀 6. แนวทางที่ต้องการพัฒนาต่อใน Set 2
- **Unified State Management:** นำเครื่องมืออย่าง Redux หรือ Pinia มาใช้จัดการข้อมูลในระดับ Global ของแอปพลิเคชัน
- **Unit Testing for UI:** ศึกษาการเขียน Test สำหรับ UI Components เพื่อลดข้อผิดพลาดก่อนการส่งมอบงาน
- **Responsive Web Design:** ปรับปรุงส่วนแสดงผลให้รองรับการทำงานบนอุปกรณ์พกพา (Mobile Friendly) อย่างเต็มรูปแบบ