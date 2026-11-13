# Nan Network · แผนภาพเครือข่ายศูนย์เรียนรู้น่าน

เว็บแอปแสดงเครือข่าย 3 วง (แนวคิดจาก food web → network analysis)

| วง | ประเภท | สี |
|---|---|---|
| ใน | ประเด็น (เช่น ความมั่นคงทางอาหาร ป่าชุมชน ชาวนา เด็กและเยาวชน สุขภาวะ ธุรกิจชุมชน) | ส้มอ่อน |
| กลาง | หน่วยงาน — คลิกเพื่อพิมพ์ "บทบาท" (แสดงเป็นการ์ดในแผงขวา) | เหลือง |
| นอก | บุคคล | เขียว |

- ความหนาเส้น = ระดับความสำคัญ 1–5 (ปรับได้ที่แผงขวา)
- แบ่งข้อมูลเป็นหลายโปรเจค (เมนูด้านบน) · ลิงก์ตรงไปโปรเจคด้วย `#p-<project-id>`
- แท็บ "วิเคราะห์เครือข่าย": strength, betweenness, ความครอบคลุมของประเด็น, หน่วยงานที่เชื่อมหลายประเด็น, โหนดที่ยังไม่เชื่อม
- สลับภาษา ไทย / EN (ปุ่มมุมขวาบน) · แต่ละรายการมีชื่อและบทบาทได้ทั้งไทยและอังกฤษ
- ดาวน์โหลดแยกตามโปรเจค (ใช้ภาษาที่เลือกอยู่):
  - **Excel (.xlsx)** 9 ชีต: สรุป, โหนด (+degree/strength/betweenness/closeness), เส้นเชื่อม (+ระดับความสำคัญ), บทบาทหน่วยงาน, ความครอบคลุมประเด็น, เมทริกซ์ ประเด็น×หน่วยงาน, เมทริกซ์ หน่วยงาน×บุคคล, อันดับศูนย์กลาง, ตัวเชื่อม
  - **รายงานสรุป (.html)** แผนภาพ + ตารางผลวิเคราะห์ → เปิดแล้วกด "พิมพ์ / บันทึกเป็น PDF"
  - **ภาพ PNG / SVG** พร้อมชื่อโปรเจคและคำอธิบายสัญลักษณ์
- ส่งออก/นำเข้า JSON

## โหมดการเก็บข้อมูล

1. **โหมดในเครื่อง** (ค่าเริ่มต้น, `config.js` = null) — โหลดตัวอย่างจาก `data.json`, ข้อมูลที่แก้ไขอยู่ในเบราว์เซอร์ของแต่ละคน
2. **โหมดเรียลไทม์ (Firebase)** — ทุกคนเห็นข้อมูลเดียวกัน, ผู้มีสิทธิ์ลงชื่อเข้าใช้ด้วย Google แล้วแก้ไขพร้อมกันได้
3. **Claude artifact** — ไฟล์เดียวกันเมื่อเปิดใน claude.ai จะใช้ฐานข้อมูลของ Claude อัตโนมัติ

## ตั้งค่าแก้ไขแบบเรียลไทม์ (Firebase, ฟรี ~10 นาที)

1. ไปที่ https://console.firebase.google.com → **Add project** (เช่น `nannetwork`) — ปิด Google Analytics ได้
2. **Build → Firestore Database → Create database** → เลือก location `asia-southeast1` → Production mode
3. แท็บ **Rules** → วางเนื้อหาไฟล์ `firestore.rules` → Publish
4. **Build → Authentication → Get started → Google → Enable**
   แล้วที่ **Settings → Authorized domains** เพิ่ม `tenrm.github.io`
5. ใน Firestore → **Start collection** `config` → document ID `editors` → field `emails` (array) ใส่อีเมลผู้มีสิทธิ์แก้ไข
6. **Project settings (⚙) → Your apps → Web (</>)** → Register app → คัดลอก `firebaseConfig`
7. แก้ไฟล์ `config.js` ใน repo:
   ```js
   window.NAN_FIREBASE_CONFIG = { apiKey: "...", authDomain: "...", projectId: "...", appId: "..." };
   ```
8. เปิดเว็บ → ฐานข้อมูลยังว่าง → ลงชื่อเข้าใช้ → กด **นำเข้าตัวอย่าง** (หรือ "นำเข้า JSON" จากไฟล์ที่ส่งออกจาก Claude)

> ค่า `apiKey` ของ Firebase ไม่ใช่ความลับ (ความปลอดภัยอยู่ที่ `firestore.rules`) จึงใส่ใน repo สาธารณะได้

## โครงสร้างข้อมูล

```
projects/{pid}               { name, name_en, desc, created }
projects/{pid}/nodes/{id}    { name, name_en, type: issue|org|person, role, role_en, note, a (มุมบนวง หรือ null), created }
projects/{pid}/links/{id}    { s, t, w: 1–5, label, created }
```
