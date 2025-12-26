# 🛡️ AdGuard Home Server on Ubuntu (VirtualBox)

โปรเจกต์นี้เป็นการจำลอง Server เพื่อทำระบบ **Network-wide Ad Blocking** (บล็อกโฆษณาทั้งเครือข่าย) ด้วย **AdGuard Home** รันบน **Ubuntu Server** ผ่าน **VirtualBox**
ช่วยกรองโฆษณา, เว็บพนัน, และสคริปต์ติดตามตัว (Trackers) ให้กับทุกอุปกรณ์ในบ้าน (คอมพิวเตอร์, มือถือ, แท็บเล็ต)

## 🚀 Features (ความสามารถ)
* 🚫 **Block Ads & Trackers:** บล็อกโฆษณาและตัวติดตามความเป็นส่วนตัว
* 🔞 **Parental Control:** กรองเนื้อหาผู้ใหญ่และเว็บพนัน
* ⚡ **Speed Up Browsing:** ช่วยให้โหลดหน้าเว็บไวขึ้นเพราะไม่ต้องโหลดโฆษณา
* 📊 **Network Monitoring:** มีหน้า Dashboard ดูกราฟการใช้งานแบบ Real-time

---

## 🛠️ Installation Steps (ขั้นตอนการติดตั้ง)

### 1. Prepare Server (เตรียมเครื่อง)
* **OS:** Ubuntu Server
* **Network:** Bridged Adapter (ใน VirtualBox ต้องเลือกโหมดนี้เพื่อให้ได้ IP วงเดียวกับบ้าน)

### 2. System Configuration (ตั้งค่าระบบก่อนติดตั้ง)
รันคำสั่งเหล่านี้เพื่อแก้ปัญหา Port 53 ชนกับ Systemd-resolved และเตรียม DNS ให้พร้อมโหลดโปรแกรม:

```bash
# ตรวจสอบ IP Address ของเครื่อง (จดเลขนี้ไว้ใช้งาน)
hostname -I

# หยุดการทำงานของ DNS เดิมใน Ubuntu (เพื่อคืนค่า Port 53 ให้ AdGuard)
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved

# ลบไฟล์ Config DNS เก่าและสร้างใหม่ (เพื่อให้เครื่องออกเน็ตไปโหลดโปรแกรมได้)
sudo rm /etc/resolv.conf
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
3. Install AdGuard Home (ติดตั้งโปรแกรม)
รันคำสั่งติดตั้งอัตโนมัติ:

Bash

curl -s -S -L [https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh](https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh) | sh -s -- -v
⚙️ Setup & Configuration (การตั้งค่าครั้งแรก)
เปิด Browser เข้าไปที่ http://[เลขIP_จากข้อ2]:3000

คลิก Get Started

ตั้งค่า Admin Web Interface เป็น Port 8080

ตั้งค่า DNS Server เป็น Port 53

สร้าง Username และ Password

🖥️ การเข้าใช้งาน Dashboard
เข้าผ่าน URL: http://[เลขIP_จากข้อ2]:8080

♻️ How to Use Config (วิธีนำการตั้งค่าไปใช้)
โปรเจกต์นี้ได้แนบไฟล์ AdGuardHome.yaml.example ซึ่งรวม Blocklists (OISD, StevenBlack) และการตั้งค่าที่ปรับแต่งแล้วไว้ หากต้องการนำไปใช้งาน ให้ทำดังนี้:

เปลี่ยนชื่อไฟล์ จากตัวอย่างเป็นไฟล์จริง:

Bash

mv AdGuardHome.yaml.example AdGuardHome.yaml
แก้ไขไฟล์ AdGuardHome.yaml:

ค้นหาบรรทัด password: CHANGE_THIS_PASSWORD_BEFORE_USE

ลบบรรทัดนี้ทิ้ง (เพื่อให้ระบบบังคับตั้งรหัสใหม่ หรือใช้รหัสเดิมของเครื่องนั้น)

ตรวจสอบค่า bind_hosts ให้ตรงกับ Network ของคุณ (ถ้าไม่แน่ใจให้ลบหัวข้อนี้)

ย้ายไฟล์และรีสตาร์ท AdGuard:

Bash

sudo systemctl stop AdGuardHome
sudo cp AdGuardHome.yaml /opt/AdGuardHome/AdGuardHome.yaml
sudo systemctl start AdGuardHome
📱 Client Setup (การตั้งค่าอุปกรณ์เพื่อใช้งาน)
นำเลข IP Address ของ Server ไปใส่ในช่อง DNS ของอุปกรณ์:

Windows:

Network Properties > IPv4 > เลือก Use the following DNS server addresses

iOS / Android:

Wi-Fi Settings > DNS > เลือก Manual

❓ Troubleshooting (การแก้ปัญหาเบื้องต้น)
หากตั้งค่าแล้วโฆษณายังไม่หาย หรือเข้าเว็บไม่ได้ ให้รันคำสั่งนี้ใน Command Prompt (Windows) เพื่อล้างค่าเก่า:

DOS

ipconfig /flushdns

---

### 💡 สิ่งที่ผมปรับให้ (เพื่อความสมบูรณ์):
1.  **เรียงลำดับใหม่:** ย้ายส่วน `sudo systemctl stop...` และ `resolv.conf` ขึ้นไปอยู่ **ก่อนติดตั้ง** (ข้อ 2) เพราะถ้าไม่ทำก่อน การติดตั้งอาจจะ Error หรือแย่ง Port กันครับ
2.  **จัด Code Block:** ใส่กรอบ ` ```bash ` ครอบคำสั่งทั้งหมด เพื่อให้ใน GitHub มันขึ้นสีสวยๆ และก๊อปปี้ง่าย
3.  **แก้คำสั่ง hostname:** เปลี่ยน `hostname -i` (ตัวเล็ก) เป็น `hostname -I` (ตัวใหญ่) เพราะตัวใหญ่จะโชว์ IP ของวงแลนที่ถูกต้องแม่นยำกว่าครับ
4.  **รวม Troubleshooting:** เอาส่วนแก้ปัญหา `flushdns` มารวมไว้ที่เดียวกันตอนท้าย ไม่ให้สับสน

เอาไปแปะปุ๊บ หล่อปั๊บแน่นอนครับ! 😎
