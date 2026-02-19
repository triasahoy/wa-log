# 🚢 WhatsApp Logistics Auto-Forwarder & Data Logger

An automated, Node.js-based middleware designed to streamline Import/Export (EXIM) logistics communication. 

This bot acts as an invisible assistant between external trucking vendors and internal receiving operations. It autonomously reads incoming WhatsApp group messages, extracts critical shipping data using Regex, filters out duplicates, forwards verified updates to internal teams, and automatically builds a `.csv` database for monthly reporting.

Built for high-volume logistics operations, such as those in the Kendal Special Economic Zone (KEK), to eliminate manual data entry and reduce communication bottlenecks.

## ✨ Key Features

* **Real-Time Data Extraction:** Uses Regular Expressions (Regex) to instantly identify and extract Container IDs, Driver Names, Fleet Numbers, and Contact Info from unstructured vendor messages.
* **Smart Duplicate Filtering (14-Day Cooldown):** Features a localized JSON database that remembers processed containers. It automatically drops duplicate spam or double-posts but intelligently resets after 14 days to accommodate physical container re-use in future import cycles.
* **Automated CSV Pipeline:** Every forwarded container is appended to a `shipping_log.csv` file with precise formatting and timestamps, creating an instant, audit-ready database for end-of-month reporting.
* **Professional Formatting:** Re-formats raw vendor text into clear, standardized alerts for the internal warehouse/ops team.
* **Admin Disconnect Alerts:** Sends a direct WhatsApp notification to the administrator if the bot goes online or offline.
* **24/7 Background Execution:** Designed to run continuously on a server or local machine using `pm2`.

## 🛠️ Tech Stack

* **Runtime:** [Node.js](https://nodejs.org/) (v22+)
* **API Wrapper:** [`whatsapp-web.js`](https://wwebjs.dev/) (Headless WhatsApp Web client via Puppeteer)
* **Process Management:** `pm2`
* **Data Storage:** Local JSON (Memory Cache) & CSV (Data Pipeline)

## 🚀 Installation & Setup

**1. Clone the repository and install dependencies:**
```bash
git clone https://github.com/triasahoy/wa-log.git
cd whatsapp-logistics-forwarder
npm install
