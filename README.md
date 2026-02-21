# WA-Log: EXIM Automated Logistics Middleware (Node.js + AI Vision)

An autonomous, end-to-end WhatsApp bot designed to streamline Import/Export (EXIM) supply chain communications. 

Built to handle high-volume logistics operations—such as those managing daily container traffic for PT XXX within the Kendal Special Economic Zone (KEK)—this middleware sits directly between external trucking vendors, factory security gates, and internal receiving operations. It parses unstructured text, runs AI vision on physical delivery photos, filters out spam, and builds automated audit databases in real-time.

## ✨ Key Features

* **Dual-Format Regex Parsing:** Automatically identifies and extracts critical data from two distinct communication formats: Port Dispatches (Trucking Vendors) and Factory Gate Arrivals (Security). It intelligently ignores typos, emojis, and extra formatting.
* **Interactive Manifest Management:** Admins can securely interact with the bot via WhatsApp commands to track expected shipments, check pending containers, and clear memory caches on the fly.
* **AI Optical Character Recognition (OCR):** Integrates `Tesseract.js` to automatically download security photos of arriving containers, read the physical text on the metal doors, and cross-reference it with the guard's typed caption.
* **Smart Duplicate Filtering (14-Day Memory):** Features a JSON-based cooldown algorithm that drops accidental vendor double-posts, keeping internal chats spam-free.
* **Transparency & Audit Trail:** Detects when vendors edit or delete messages and instantly alerts the internal operations team with the original text to prevent hidden cancellations.
* **Automated CSV Data Pipeline:** Every verified container is appended to a local `shipping_log.csv` file with precise timestamps, AI verification statuses, and Anomaly tags for end-of-month reporting.
* **Automated Daily Reporting:** Uses `node-cron` to automatically read the daily database and send a consolidated summary report to the operations group every day at 5:00 PM.

---

## 🎛️ Interactive Admin Commands

The bot features a built-in control panel via WhatsApp, allowing the Internal Ops team to manage the container manifest and system memory directly from their phones:

| Command | Action | Bot Response Example |
| :--- | :--- | :--- |
| `!add [number]` | Adds containers to the tracking manifest. | "✅ Added 3 containers to manifest." |
| `!pending` | Checks which expected containers haven't arrived. | "⏳ PENDING (2 remaining): WHSU2990300" |
| `!remove [number]` | Deletes a specific typo/container from the manifest. | "🗑️ Removed 1 container(s) from the manifest." |
| `!clear` | Wipes the entire manifest for the next shipment. | "🗑️ Manifest cleared. Ready for next shipment." |
| `!forget [number]` | Deletes a container's spam cooldown memory. | "🧠 Amnesia successful! Forgot history..." |

---

## 📈 Proof of Results

Here is how the bot transforms messy, manual vendor communications into clean, automated logistics data (Note: Sensitive driver and container data has been masked for portfolio demonstration):

### 1. Vendor Dispatch (Port Departure)
When a trucking vendor sends an unstructured update, the bot instantly parses and formats it:
> 🚛*[CONTAINER ON THE WAY]*
> 
> No cont : XXXX1234567
> Nama sopir : J*** D**
> No armada : B 1234 XXX
> No tlp : 0812********

### 2. Factory Arrival + AI Vision Verification
When security sends a photo of the truck arriving at the Factory gates, the bot reads the caption AND scans the photo pixels:
> ✅*[CONTAINER ARRIVED]*
> 
> IN armada Container import ke 1.
> ✅Driver : R******
> ✅Nopol : H 5678 XX
> ✅No.Cont : XXXX9876543
>
> **AI Vision Scan:** ✅ Verified Match

### 3. Ghost Arrival (Anomaly Detection)
If a container arrives at the factory but the vendor forgot to send the dispatch update from the port earlier:
> ✅*[CONTAINER ARRIVED]*
> 
> IN armada Container import...
> *(Data fields omitted for brevity)*
>
> 🚨 **ANOMALY DETECTED:**
> Vendor missed dispatch report. Container arrived unannounced!
>
> **AI Vision Scan:** ✅ Verified Match

### 4. Automated 5:00 PM Daily Summary
At exactly 5:00 PM, the bot tallies the day's CSV logs and sends this to the management group:
> 📊 **[DAILY EXIM LOGISTICS REPORT]**
> 📅 **Date:** 21/02/2026
> 
> **Total Dispatched (Port):** 2
> **Total Arrived (Factory):** 1
> 
> **Activity Today:**
> 🚛 XXXX1234567 (On the way)
> 🚛 YYYY1122334 (On the way)
> ✅ XXXX9876543 (Arrived)

---

## 🛠️ Tech Stack

* **Runtime:** Node.js
* **API Wrapper:** whatsapp-web.js
* **Enterprise AI:** Google Cloud Vision API
* **Data Handling:** JSON (State Memory) & CSV (Audit Logs)
* **Automation:** node-cron & pm2

---

## 🚀 Installation & Setup

**1. Clone the repository:**
```bash
git clone [https://github.com/triasahoy/wa-log.git](https://github.com/triasahoy/wa-log.git)
cd wa-log

```

**2. Install all dependencies:**

```bash
npm install whatsapp-web.js qrcode-terminal tesseract.js node-cron

```

**3. Configure your WhatsApp Group IDs:**
Open `index.js` and input your specific WhatsApp Group IDs (ending in `@g.us`) and your Admin Number (ending in `@c.us`).

```javascript
const SOURCE_GROUP_ID = '1234567890@g.us';      // Trucking Vendor Group
const SECURITY_GROUP_ID = '0987654321@g.us';    // Factory Security Group
const DESTINATION_GROUP_ID = '1122334455@g.us'; // Internal Operations Group
const ADMIN_ID = '6281234567890@c.us';          // Admin Control Number

```

**4. Run the application:**

```bash
node index.js

```

Scan the generated QR code using your WhatsApp "Linked Devices" feature.

**5. Deploy for 24/7 Background Execution:**

```bash
npm install -g pm2
pm2 start index.js --name "EximBot"
pm2 save

```
### 🚛 SOP for Trucking Vendor Group

> **[PENGUMUMAN: PROSEDUR BARU PELAPORAN CONTAINER]**

> Mohon gunakan salah satu format di bawah ini saat mengirim update armada agar tercatat otomatis oleh sistem:

> **Opsi 1: Format Teks (Terbaik)**
> 
> No cont : [Nomor Container]
> Nama sopir : [Nama]
> No armada : [Plat Nomor]
> No tlp : [Nomor HP]
> 
> **Opsi 2: Format Foto & Kontak**

> Kirim **Foto Container** (Nomor harus jelas) diikuti dengan **Kartu Kontak (VCard)** sopir.
> *Catatan: Sistem akan otomatis meneruskan detail ini ke tim Operasional kami.*

---

### 🛡️ SOP for Factory Security Group

*Copy and paste this to your security team:*

> **[SOP KEAMANAN: LAPORAN KEDATANGAN CONTAINER]**

> Setiap container yang sampai di gerbang KEK wajib dilaporkan dengan langkah berikut:

> 1. **Ambil Foto:** Pastikan nomor container terlihat jelas (tidak terhalang bayangan atau blur).

> 2. **Kirim dengan Caption:** Wajib menyertakan teks berikut pada foto tersebut:
> 
> Driver : [Nama]
> Nopol : [Plat Nomor]
> No.Cont : [Nomor Container]
> *Penting: Tanpa format teks di atas, sistem AI tidak akan memverifikasi kedatangan unit.*

---

### 📊 Project Architecture Overview

### 💡 Final Bot Management Tips (For You)

* **Pre-Planning**: Always use **`!add`** in your private chat *before* the vendor sends their update so the bot knows to look for that specific container.
* **Private Oversight**: Use **`!pending`** in your private chat to see which containers are still on the road without alerting the staff.
* **Error Correction**: If a vendor makes a typo, use **`!forget [Number]`** and have them resend the correct format to reset the history.

## ⚠️ Disclaimer

This project utilizes the unofficial `whatsapp-web.js` library. It is designed strictly for internal operational efficiency and B2B logistics routing. Using this for mass-marketing or spam violates WhatsApp's Terms of Service. Always test with internal numbers before deploying to live vendor groups.
