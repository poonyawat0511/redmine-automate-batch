# Redmine Auto Sync v1.0 (Manual Run)

เครื่องมือช่วยส่งเวลาการทำงาน (Spent Time) จาก Google Sheet เข้าสู่ระบบ Redmine โดยอัตโนมัติ สำหรับการใช้งานแบบ Manual เพื่อตรวจสอบความถูกต้องผ่านหน้าจอ Terminal ก่อนยืนยันการปิดโปรแกรม

---

## การเตรียมไฟล์ตามแพลตฟอร์ม (Preparation)

#### Downaload file ได้ที่นี้ : [Get File](https://drive.google.com/drive/folders/1hNw0lCtdp3Je35gCRgQr9IrdXJpms95S?usp=sharing)

ตรวจสอบว่าคุณมีไฟล์ Executable ที่ถูกต้องตามระบบปฏิบัติการของคุณ:

| ระบบปฏิบัติการ | ชื่อไฟล์แนะนำ | สถาปัตยกรรม (Target) |
| :--- | :--- | :--- |
| **Ubuntu / Linux** | `RedmineSync-linux-x64` | node18-linux-x64 |
| **Mac OS (ARM/M1/M2/M3)** | `RedmineSync-mac-arm64` | node18-macos-arm64 |

## Google Sheet (Template)

Google Sheet ชุดนี้ถูกออกแบบมาเพื่อเป็น **ศูนย์กลางการบันทึกเวลา (Centralized Time Log)** ที่ทำงานร่วมกับโปรแกรม Redmine Auto Sync โดยเฉพาะ โดยจะช่วยเปลี่ยนการกรอกข้อมูลในระบบ Redmine ที่ยุ่งยากให้กลายเป็นเรื่องง่ายและเป็นระบบมากขึ้น

## การอนุญาตสิทธิ์เข้าถึง Google Sheet (Mandatory)

เพื่อให้โปรแกรมสามารถอ่านและเขียนข้อมูลใน Google Sheet ได้ คุณ **"ต้อง"** ทำการแชร์สิทธิ์การเข้าถึงให้กับ Service Account ของระบบตามขั้นตอนดังนี้:

1. **คัดลอกอีเมล Service Account**: 
   `github-redmine-bot@redmine-487415.iam.gserviceaccount.com`

2. **ตั้งค่าใน Google Sheet**:
   - เปิด Google Sheet ที่คุณต้องการใช้งาน
   - คลิกปุ่ม **Share (แชร์)** ที่มุมขวาบน
   - วางอีเมลที่คัดลอกมาลงในช่อง "Add people and groups"
   - กำหนดสิทธิ์เป็น **Editor (ผู้แก้ไข)** (เพื่อให้บอทสามารถเขียนคำว่า "Synced" ลงในคอลัมน์ Status ได้)
   - กด **Send (ส่ง)** โดยไม่จำเป็นต้องส่งการแจ้งเตือนอีเมล

**หมายเหตุ**: หากไม่ทำขั้นตอนนี้ โปรแกรมจะแจ้งข้อผิดพลาดว่าหาไฟล์ไม่พบ หรือไม่มีสิทธิ์เข้าถึงข้อมูล

### รายละเอียดการทำงานของแต่ละ Tab

| Tab | หน้าที่หลัก | สิ่งที่ทำได้ |
| :--- | :--- | :--- |
| **Settings** | การตั้งค่าระบบ | ใช้ระบุ **Redmine API Key** เพื่อยืนยันตัวตน และกำหนดชื่อ **Target Tab** (เช่น 2026-02) เพื่อให้บอทวิ่งไปรันข้อมูลได้ถูกที่ |
| **Configs** | ข้อมูลอ้างอิง | จัดเก็บค่าพื้นฐาน เช่น รายชื่อโปรเจกต์ หรือ Activity ID เพื่อใช้ทำ Dropdown ในหน้าบันทึกเวลา |
| **Tab ประจำเดือน** | พื้นที่ลงเวลา | บันทึกรายละเอียดงาน: **Issue ID** (เลขงาน), **Comment** (รายละเอียด), **Start-End** (เวลา) โดยระบบจะตรวจสอบเฉพาะแถวที่ยังไม่ขึ้น **Synced** เพื่อส่งเข้า Redmine |

### วิธีการเริ่มต้นใช้งาน
1. เข้าไปที่ [Template Sheet](https://docs.google.com/spreadsheets/d/1jP0yyjM89bizhwfgu_1WBs51ySloqrVnh5qiy9GjoqQ/edit?usp=sharing) แล้วทำการ **Make a copy** เป็นของตนเอง
2. นำ **API Key** จากหน้า My Account ใน Redmine มาใส่ในหน้า **Settings**
3. ระบุชื่อ Tab ที่ต้องการให้โปรแกรมทำงาน (Current Sheet Name)
4. เริ่มกรอกข้อมูลการทำงานใน Tab ประจำเดือน เมื่อต้องการส่งข้อมูลให้รันโปรแกรม `RedmineSync` เพื่อทำการ Sync ข้อมูลอัตโนมัติ

---

## ขั้นตอนการติดตั้งและตั้งค่า (Setup)

### 1. การกำหนดสิทธิ์การใช้งาน (ทุกแพลตฟอร์ม)
เปิด Terminal ในโฟลเดอร์ที่เก็บไฟล์ และใช้คำสั่งเพื่ออนุญาตให้โปรแกรมทำงาน:
```bash
# ตัวอย่างสำหรับ Ubuntu
chmod +x RedmineSync-linux-x64

# ตัวอย่างสำหรับ Mac
chmod +x RedmineSync-mac-arm64
```
### 2. การใช้งาน (ทุกแพลตฟอร์ม)
เปิด Terminal ในโฟลเดอร์ที่เก็บไฟล์ และใช้คำสั่งเพื่อให้โปรแกรมทำงาน:
```bash
# ตัวอย่างสำหรับ Ubuntu
./RedmineSync-linux-x64

# ตัวอย่างสำหรับ Mac
./RedmineSync-mac-arm64
```
### การตั้งค่าสำหรับการใช้งานครั้งแรก (First-time Configuration)

เมื่อคุณรันโปรแกรมครั้งแรก (หรือเมื่อไม่มีไฟล์ `redmine-config.json`) โปรแกรมจะสอบถามที่อยู่ของ Google Sheet เพื่อใช้เป็นฐานข้อมูลในการทำงาน:

#### วิธีหา Google Sheet ID
คุณสามารถดู ID ได้จาก **URL** บน Browser ของคุณ โดยจะอยู่ระหว่าง `/d/` และ `/edit`:
> ตัวอย่าง: `https://docs.google.com/spreadsheets/d/1jP0yyjM89bizhwfgu_1WBs51ySloqrVnh5qiy9GjoqQ/edit`
> **Sheet ID คือ**: `1jP0yyjM89bizhwfgu_1WBs51ySloqrVnh5qiy9GjoqQ`



# Redmine Auto Sync v1.2 (Auto Batch)

เครื่องมือสำหรับส่งเวลาการทำงาน (Spent Time) จาก Google Sheet เข้าสู่ระบบ Redmine แบบอัตโนมัติ ออกแบบมาเพื่อทำงานร่วมกับระบบตั้งเวลา (Scheduler) เช่น Crontab โดยโปรแกรมจะปิดตัวเองทันทีเมื่อทำงานเสร็จสิ้น

---

##การเตรียมไฟล์ตามแพลตฟอร์ม (Preparation)

#### Downaload file ได้ที่นี้ : [Get File](https://drive.google.com/drive/folders/1hNw0lCtdp3Je35gCRgQr9IrdXJpms95S?usp=sharing)

ผู้ใช้งานควรเลือกใช้ไฟล์ Executable ให้ตรงตามระบบปฏิบัติการที่ใช้งาน:

| ระบบปฏิบัติการ | ชื่อไฟล์แนะนำ | สถาปัตยกรรม (Target) |
| :--- | :--- | :--- |
| **Ubuntu / Linux** | `RedmineSync-linux-x64` | node18-linux-x64 |
| **Mac OS (ARM/M1/M2/M3)** | `RedmineSync-mac-arm64` | node18-macos-arm64 |

---

## ขั้นตอนการติดตั้งและตั้งค่า (Setup)

### 1. การกำหนดสิทธิ์การใช้งาน (ทุกแพลตฟอร์ม)
เปิด Terminal ในโฟลเดอร์ที่เก็บไฟล์ และใช้คำสั่งเพื่ออนุญาตให้โปรแกรมทำงาน:
```bash
# ตัวอย่างสำหรับ Ubuntu
chmod +x RedmineSync-linux-x64

# ตัวอย่างสำหรับ Mac
chmod +x RedmineSync-mac-arm64
```
### 2. การใช้งาน (ทุกแพลตฟอร์ม)
เปิด Terminal ในโฟลเดอร์ที่เก็บไฟล์ และใช้คำสั่งเพื่อให้โปรแกรมทำงาน:
```bash
# ตัวอย่างสำหรับ Ubuntu
./RedmineSync-linux-x64

# ตัวอย่างสำหรับ Mac
./RedmineSync-mac-arm64
```
### การตั้งค่าสำหรับการใช้งานครั้งแรก (First-time Configuration)

เมื่อคุณรันโปรแกรมครั้งแรก (หรือเมื่อไม่มีไฟล์ `redmine-config.json`) โปรแกรมจะสอบถามที่อยู่ของ Google Sheet เพื่อใช้เป็นฐานข้อมูลในการทำงาน:

#### วิธีหา Google Sheet ID
คุณสามารถดู ID ได้จาก **URL** บน Browser ของคุณ โดยจะอยู่ระหว่าง `/d/` และ `/edit`:
> ตัวอย่าง: `https://docs.google.com/spreadsheets/d/1jP0yyjM89bizhwfgu_1WBs51ySloqrVnh5qiy9GjoqQ/edit`
> **Sheet ID คือ**: `1jP0yyjM89bizhwfgu_1WBs51ySloqrVnh5qiy9GjoqQ`

#### Require Setup Method For Auto Run Batch version (**Ubuntu / Linux**)
เพื่อให้โปรแกรมทำงานได้เองในทุกๆ วันตามเวลาที่กำหนด ให้ทำตามขั้นตอนดังนี้:
#### 1. ให้สิทธิ์การรันไฟล์ (Permission)
ตรวจสอบให้แน่ใจว่าไฟล์โปรแกรมได้รับสิทธิ์ในการทำงานแล้ว:
```bash
chmod +x RedmineSync-linux-x64
```
##### 2. รันเพื่อตั้งค่าครั้งแรก (Initial Setup)
คุณต้องรันโปรแกรมแบบ Manual 1 ครั้งเพื่อกรอก Sheet ID และสร้างไฟล์ redmine-config.json:
```bash
./RedmineSync-linux-x64
```
#### 3. ตั้งค่าตารางเวลาด้วย Crontab
ใช้คำสั่งเพื่อเปิดหน้าต่างแก้ไขตารางเวลาของระบบ:
```bash
crontab -e
```
#### 4. เพิ่มคำสั่งสำหรับการรัน Batch
คัดลอกบรรทัดด้านล่างนี้ไปวางไว้ที่ บรรทัดสุดท้าย ของไฟล์ (แก้ไข Path ให้ตรงกับที่อยู่จริงในเครื่องของคุณ):
```bash
00 18 * * * cd "/home/user/Documents/redmine-folder" && ./RedmineSync-linux-x64 --auto >> log.txt 2>&1
```
รายละเอียดของคำสั่ง:

00 18 * * *: ตั้งเวลาให้รันตอน 18:00 น. ของทุกวัน.

cd "/path/to/folder": สำคัญมาก ต้องสั่งให้ระบบเข้าไปยังโฟลเดอร์ที่เก็บโปรแกรมก่อน เพื่อให้บอทมองเห็นไฟล์ redmine-config.json.

&&: เชื่อมคำสั่งให้ทำงานต่อกันเมื่อเข้าโฟลเดอร์สำเร็จ.

--auto: คำสั่งบังคับให้โปรแกรมปิดตัวเองทันทีเมื่อทำงานเสร็จ (ไม่ค้างรอการกด Enter).

>> log.txt 2>&1: บันทึกประวัติการรันและข้อผิดพลาดลงในไฟล์ log.txt.

#### 5. ตรวจสอบและบันทึก
กฎสำคัญ: ต้องกด Enter ต่อท้ายบรรทัดคำสั่งใน Crontab เพื่อให้มีบรรทัดว่าง 1 บรรทัดเสมอ มิฉะนั้นคำสั่งอาจไม่ทำงาน.

กด Ctrl + O เพื่อบันทึก และ Ctrl + X เพื่อออกจากหน้าจอแก้ไข.

Developed by: Poonyawat (poonyawat.kh@ocean.co.th)

