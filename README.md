
# 🚀 คู่มือปฏิบัติการ ENGCE301 - สัปดาห์ที่ 7
## Deploy TaskBoard to Cloud: 3-Tier Architecture บน Railway

**สัปดาห์:** 7 | **ระยะเวลา:** 3 ชั่วโมง | **ระดับความยาก:** ⭐⭐⭐

---

## 📋 สารบัญ

1. [วัตถุประสงค์การเรียนรู้](#วัตถุประสงค์การเรียนรู้)
2. [ภาพรวม: Week 6 (Docker) → Week 7 (Cloud)](#ภาพรวม-week-6-docker--week-7-cloud)
3. [Railway คืออะไร](#railway-คืออะไร)
4. [สิ่งที่ต้องเตรียม](#สิ่งที่ต้องเตรียม)
5. [Part 1: สมัคร Railway](#part-1-สมัคร-railway-และเตรียม-project)
6. [Part 2: Deploy PostgreSQL](#part-2-deploy-postgresql-database)
7. [Part 3: Deploy Backend API](#part-3-deploy-backend-api)
8. [Part 4: Deploy Frontend](#part-4-deploy-frontend)
9. [ใบงาน & Cleanup](#ใบงาน-cloud-deployment-analysis)

---

## 🎯 วัตถุประสงค์การเรียนรู้

เมื่อจบ Lab นี้ นักศึกษาสามารถ:

- ✅ Deploy 3-Tier App ขึ้น Cloud PaaS
- ✅ อธิบายความแตกต่าง Docker Local vs Cloud
- ✅ ตั้งค่า Environment Variables และ connect Services
- ✅ ใช้ Cloud Dashboard (Logs, Metrics, Deployments)
- ✅ เชื่อมโยง 12-Factor App กับการปฏิบัติ

---

## 🔄 ภาพรวม: Week 6 → Week 7

```
WEEK 6: Docker Local          WEEK 7: Railway Cloud
localhost → docker-compose    GitHub → Auto Deploy
localhost:443                 xxx.railway.app (HTTPS ✓)
```

---

## 🚂 Railway = PaaS (Platform as a Service)

**ข้อดี:**
- Deploy จาก GitHub ทันที
- ไม่ต้อง Credit Card (free $5/เดือน)
- Managed Database, Auto SSL, Auto Deploy
- Real-time Logs & Metrics built-in

**Railway vs IaaS:** Railway จัดการ Runtime/OS/SSL ให้ คุณแค่อัพโหลด Code

---

## 📚 สิ่งที่ต้องเตรียม

- ☐ GitHub Account (login ได้)
- ☐ Week 6 Project push บน GitHub
- ☐ Web Browser
- ☐ Internet connection

---

## Part 1: สมัคร Railway (30 นาที)

### Step 1: ไปที่ https://railway.app
1. คลิก **Login** → **Login with GitHub**
2. Authorize Railway
3. ตรวจสอบ $5 Free Credits ใน Settings → Billing

### Step 2: สร้าง Project
1. **+ New Project**
2. เลือก **Empty Project**
3. ตั้งชื่อ: **"taskboard-cloud"**

---

## Part 2: Deploy PostgreSQL (20 นาที)

### Step 1: เพิ่ม PostgreSQL Service
1. **+ New** → **Database** → **PostgreSQL**
2. รอ auto-create

### Step 2: สร้างตาราง
1. ไปที่แท็บ **Data** → **Query**
2. รัน SQL:

```sql
CREATE TABLE tasks (
    id SERIAL PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    description TEXT,
    status VARCHAR(20) DEFAULT 'TODO',
    priority VARCHAR(10) DEFAULT 'MEDIUM',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO tasks (title, description) VALUES
('Setup Cloud', 'Deploy to Railway', 'HIGH'),
('Test API', 'Verify CRUD', 'HIGH');
```

---

## Part 3: Deploy Backend API (45 นาที)

### Step 1: แก้ไข `api/src/config/database.js`

ตรวจสอบให้รองรับ `DATABASE_URL`:

```javascript
const pool = process.env.DATABASE_URL 
    ? new Pool({
        connectionString: process.env.DATABASE_URL,
        ssl: { rejectUnauthorized: false }
      })
    : new Pool({...});
```

### Step 2: Deploy จาก GitHub
1. **+ New** → **GitHub Repo**
2. เลือก repository
3. **Root Directory: `/api`**

### Step 3: ตั้งค่า Variables
ไปที่ **Variables** tab:
- `DATABASE_URL` = `${{Postgres.DATABASE_URL}}`
- `NODE_ENV` = `production`
- `PORT` = `3000`

### Step 4: Generate Domain
Settings → Networking → **Generate Domain**

### Step 5: ทดสอบ
```bash
curl https://YOUR-API-URL/api/health
curl https://YOUR-API-URL/api/tasks
```

---

## Part 4: Deploy Frontend (45 นาที)

### Step 1: แก้ไข `frontend/js/app.js`

```javascript
// ⚠️ เปลี่ยน URL นี้
const API_BASE = 'https://YOUR-API-URL.railway.app/api';
```

Commit & Push

### Step 2: สร้าง `frontend/package.json`

```json
{
  "scripts": {
    "start": "npx serve -s . -l $PORT"
  }
}
```

### Step 3: Deploy Frontend
1. **+ New** → **GitHub Repo**
2. **Root Directory: `/frontend`**
3. Generate Domain

### Step 4: ทดสอบ
เปิด Frontend URL → ควรเห็น TaskBoard + Tasks

---

## 🧹 Cleanup - สำคัญ!

⚠️ **ต้องลบ Project หลังเสร็จ!**

- 3 Services รันตลอด 24/7 = หมด $5 ใน ~3 วัน
- วิธี: Project Settings → Delete Project → พิมพ์ชื่อยืนยัน

---

## 📝 ใบงาน: Cloud Deployment Analysis

สร้าง `CLOUD_DEPLOYMENT.md` ตอบคำถาม:

1. **URLs ของระบบ** (Frontend, API, Database)
2. **เปรียบเทียบ Docker vs Cloud** (5 ด้าน)
3. **Railway = PaaS เพราะ?**
4. **ใช้ 12-Factor App ตรงไหน?** (5 Factors)
5. **Reflection** (สิ่งที่เรียนรู้, ปัญหา, เลือกใช้เมื่อไหร่)
6. **5 Screenshots** (Dashboard, Frontend, API, Logs, Metrics)

ส่งเป็น GitHub repository
