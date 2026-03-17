# TEAM_SPLIT.md

## ข้อมูลกลุ่ม
- กลุ่มที่: [Sec2-3]
- รายวิชา: ENGSE207 Software Architecture

## รายชื่อสมาชิก
- [67543210055-9] [ณัฐกิตต์-แก้วคำยศ]
- [67543210064-1] [วรวิทย์-สุวรรณ]

---

## การแบ่งงานหลัก

### สมาชิกคนที่ 1: [ณัฐกิตต์-แก้วคำยศ]
รับผิดชอบงานหลัก: Backend, Security & Infrastructure Engineer
- Backend Services: พัฒนาและจัดการโครงสร้างของ Auth Service และ Task Service

- API Integration: เชื่อมต่อ Frontend เข้ากับ Backend Services ผ่าน HTTPS และจัดการการจัดเก็บ JWT Token ใน Client-side

- Security & Scripts: * จัดทำและแก้ไข gen-certs.sh เพื่อจัดการ Self-Signed Certificate สำหรับ HTTPS

- จัดการระบบ Authentication ด้วย JWT (JSON Web Token) และ Role-based Access Control

- Infrastructure: จัดทำไฟล์ docker-compose.yml, Dockerfiles และไฟล์ Environment (.env) เพื่อให้ทุก Microservices ทำงานร่วมกันได้

- Testing: รับผิดชอบการทดสอบ API ทั้งหมด (T1 - T11) ผ่าน Postman และ Curl

### สมาชิกคนที่ 2: [วรวิทย์-สุวรรณ]
รับผิดชอบงานหลัก: Frontend & Integration Developer
- Frontend Development: พัฒนาส่วนแสดงผลผู้ใช้งาน (User Interface) ด้วย React/Vue ในโฟลเดอร์ frontend

- Database & Logging: ออกแบบและจัดการฐานข้อมูล PostgreSQL และพัฒนาระบบ Log Service เพื่อบันทึกเหตุการณ์สำคัญ (Audit Logs)

- State Management: จัดการสถานะของแอปพลิเคชัน (เช่น การแสดงรายการ Task, การแสดงสถานะ Login)

- UI Testing: ทดสอบการทำงานในส่วนของ User Journey (เช่น การกดปุ่ม Login, การสร้าง Task ผ่านหน้าเว็บ)

- Documentation: ร่วมจัดทำคู่มือการใช้งานและการสรุปผลใน README.md

## งานที่ดำเนินการร่วมกัน
- ออกแบบ architecture diagram
- ทดสอบAPIผ่าน postman
- จัดทำ README และ screenshots
- Database & Log
- UI ความสวยงามหน้าเว็บ

## เหตุผลในการแบ่งงาน
แบ่งงานตามความสามารถที่ตนเองถนัด และ ในส่วนที่ไม่ถนัดจะนำมาช่วยกันแก้ไข

ซึ่งถือว่าแบ่งงานกันได้อย่างดีเยี่ยมเพราะถนัดต่างกันอย่างชัดเจน Backend/Frontend

## สรุปการเชื่อมโยงงานของสมาชิก
1. การเชื่อมต่อผ่าน API และ Security (จุดประสานงานหลัก)
ณัฐกิตต์ (Backend): จัดเตรียม API Endpoints และกำหนดโครงสร้าง JSON ของข้อมูล (เช่น ข้อมูล Task หรือ Log) รวมถึงการตั้งค่า HTTPS และ JWT เพื่อความปลอดภัย

วรวิทย์ (Frontend): นำโครงสร้าง API นั้นมาเขียนโค้ดฝั่งหน้าบ้าน เพื่อรับ-ส่งข้อมูลให้ตรงกัน หาก Backend เปลี่ยนโครงสร้าง (เช่น เปลี่ยนชื่อ Field ใน Database) ทั้งคู่ต้องประสานงานกันเพื่อให้ระบบไม่พัง

2. การจัดการ Authentication (JWT Token)
ฝั่งส่ง (Backend): เมื่อผู้ใช้ Login ผ่านระบบของณัฐกิตต์ ตัว Auth Service จะสร้าง Token ส่งกลับไปให้

ฝั่งรับ (Frontend): วรวิทย์ต้องเขียนระบบให้เก็บ Token นี้ไว้ (localStorage) และส่งกลับมาใน Header ทุกครั้งที่เรียกใช้หน้า Tasks หรือ Logs เพื่อยืนยันตัวตน

3. การแสดงผลและการบันทึก Log
การบันทึก: เมื่อวรวิทย์กดปุ่ม "สร้างงาน" บนหน้าเว็บ ระบบของณัฐกิตต์จะทำการบันทึกข้อมูลลงฐานข้อมูล และส่งสัญญาณไปที่ Log Service อัตโนมัติ

การแสดงผล: เมื่อ Admin (วรวิทย์) ต้องการดูประวัติการใช้งาน ระบบหน้าบ้านจะเรียกข้อมูลจาก Log Service ที่ณัฐกิตต์จัดการไว้มาแสดงผลเป็นตารางที่อ่านง่าย

4. การทดสอบระบบ (End-to-End Testing)
ทั้งสองคนต้องประสานงานกันในขั้นตอนสุดท้ายเพื่อเช็คว่า "สิ่งที่ Backend ทำ" ตรงกับ "สิ่งที่ Frontend แสดงผล" * ตัวอย่าง: หากณัฐกิตต์ตั้งค่า Rate Limit ไว้ที่ 5 ครั้ง/นาที วรวิทย์ต้องเตรียมหน้าเว็บให้แสดง Error Message ที่เหมาะสมเมื่อผู้ใช้กดรัวเกินไป

🤝 ส่วนที่ต้องประสานงานกันเป็นพิเศษ (Key Collaboration)
API Specification: การตกลงเรื่อง Path ของ URL (เช่น /api/tasks หรือ /api/task) และรูปแบบข้อมูล JSON

Environment Variables: การใช้ไฟล์ .env ร่วมกันเพื่อให้ทั้งคู่เชื่อมต่อกับ Nginx และ Docker Network เดียวกันได้อย่างถูกต้อง

Error Handling: การตกลงว่าถ้าเกิด Error (เช่น 401 หรือ 403) Backend จะส่ง Code อะไรมา และ Frontend จะแจ้งเตือนผู้ใช้อย่างไร