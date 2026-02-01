# ⚙️ n8n Workflow Collection

> ## 🚨 CRITICAL SECURITY NOTICE
> 
> **This repository previously contained exposed API keys that are still visible in git commit history.**  
> **Repository owners MUST take immediate action - see [SECURITY.md](SECURITY.md) for detailed remediation steps.**
> 
> - ⚠️ Exposed API keys must be **revoked immediately**
> - 🔧 Git history must be **cleaned using git-filter-repo or BFG**
> - 📖 See [SECURITY.md](SECURITY.md) for complete instructions

---

A curated collection of **n8n workflow templates** (`.json` files) for automating real-world tasks like AI research, job notifications, and daily reminders.



Each workflow is designed to demonstrate **practical automation skills** — including advanced parallel processing, hybrid AI model integration, and database orchestration.



## 🧩 What is n8n?



[n8n](https://n8n.io) is a **visual workflow automation platform** that lets you connect APIs, databases, and services with minimal code.  

It’s open-source and extensible, making it perfect for both personal automation and production pipelines.



## 🧠 What’s Inside



Each `.json` file in this repository represents a **complete, ready-to-import workflow**.



```text

📦 n8n-workflows/

┣ 📄 Multi-Agent Research Modified.json  <-- ✨ NEW!

┣ 📄 Tool_ Travily API.json <-- ✨ NEW! (Dependency for Research Agent)

┣ 📄 Job Automation with Ai.json

┣ 📄 LinkedIn_AI_Job_Notifier.json

┣ 📄 Quran_Hadith_Reminder.json

````



-----



### 4️⃣ Multi-Model Research Agent (Hybrid Architecture)



**An advanced, production-grade agentic workflow inspired by [Jack Van Der Vall](https://github.com/jackvandervall/agentic-archive).**



This workflow acts as a comprehensive research assistant that lives in **Telegram**. It uses a **"Divide and Conquer"** strategy to split a user's request into parallel tasks, executing them simultaneously to save time.



**Key Features:**



  * **Parallel Processing:** Uses a Router -\> Merge architecture to run **Web Research** and **Dataset Hunting** branches at the same time.

  * **Hybrid AI Intelligence:** Orchestrates multiple models to balance cost and performance:

      * **Grok (xAI):** Used for high-speed reasoning and query refinement.

      * **Gemini (Google):** Used for large-context information extraction.

      * **GPT-4o (OpenAI):** Used for the final academic paper writing.

  * **Tool Integration:**

      * **Tavily API:** Optimized web search for agents (replaced expensive Perplexity calls).

      * **Supabase:** Automatically logs every research topic, paper, and dataset link to a database.

      * **Telegram Bot:** Full interaction via chat; generates and sends a `.md` file to bypass Telegram's 4,096 character limit.



**Main Workflow:** `Multi-Agent Research Modified.json`  

**Required Tool:** `Tool_ Travily API.json` (Import this separately as a Tool)  

> ⚠️ **IMPORTANT:** After importing `Tool_ Travily API.json`, you **must** replace `YOUR_TAVILY_API_KEY_HERE` with your actual [Tavily API key](https://tavily.com). Never commit real API keys to version control.

**Preview:**

![Multi AGEN](https://github.com/RobinMillford/My_n8n_workflows/blob/main/Multi-Agent%20Research%20Modified.png?raw=true)

### 1️⃣ LinkedIn AI Job Notifier

This workflow:
- Fetches job postings from **LinkedIn RSS feeds**
- Uses an **AI model** to analyze job descriptions against multiple resumes
- Sends **Telegram notifications** only for jobs that are a high match

**File:** `LinkedIn_AI_Job_Notifier.json`  
**Preview:**

![LinkedIn AI Job Workflow](https://github.com/RobinMillford/My_n8n_workflows/blob/main/Linkedln_ai_job.png?raw=true)

---

### 2️⃣ Quran & Hadith Daily Reminder

This workflow:
- Sends a **daily Quran verse or Hadith** to Telegram
- Uses an **AI reflection generator** for a meaningful daily reminder
- Checks Google Sheets to **avoid duplicates**

**File:** `Quran_Hadith_Reminder.json`  
**Preview:**

![Quran Hadith Workflow](https://github.com/RobinMillford/My_n8n_workflows/blob/main/Quran_Hadith_Reminder.png?raw=true)

---

### 3️⃣ AI-Powered Job Application Assistant

This is a comprehensive workflow that automates the entire job application process.
- Accepts job details (text, image, or PDF) via a **simple n8n form**
- Uses **AI (Gemini)** to parse the job post and extract key details (Title, Company)
- Selects the correct resume from **Google Drive** and extracts its text
- Uses a second **AI (Gemini)** call to write a professional, personalized email by matching the resume text to the job description
- Sends the application via **Gmail** with the correct resume PDF attached
- Logs the application as a new **to-do item on a specific Notion page**

**File:** `Job Automation with Ai.json`  
**Preview:**

![AI Job Application Workflow](https://github.com/RobinMillford/My_n8n_workflows/blob/main/Job%20automation_ai.png?raw=true)

---

## 🚀 How to Use These Workflows

### **Step 1: Install n8n**

You can run n8n locally or with Docker.

#### Option A: Local (npm)
```bash
npm install -g n8n
n8n start
````

#### Option B: Docker

```bash
docker run -it --rm \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

Once running, open your browser at 👉 [http://localhost:5678](http://localhost:5678)

---

### **Step 2: Import a Workflow**

1. Open your **n8n dashboard**
2. Click **“Import from File”**
3. Select any `.json` file from this repository
4. The workflow will appear ready to configure

---

### **Step 3: Add Your Own Credentials**

Some workflows use APIs like **Telegram, Gmail, Groq, or Google Drive**.
These credentials are **never stored in the exported JSON** — you’ll need to re-add them.

1. Open nodes with a ⚠️ warning
2. Click **Credentials → Add New**
3. Enter your API key or connection settings
4. Save and close

> 🔒 **Note:**
> All credentials remain **local to your n8n instance**.
> No sensitive data is included in these workflow files.

---

### **Step 4: Run the Workflow**

Click **▶ Execute Workflow** to test.
You’ll see live data flowing through each node in real time.

---

## 🧾 Example Output

Each workflow outputs structured data similar to:

```json
{
  "status": "success",
  "message": "Workflow executed successfully",
  "data": {...}
}
```

---

## 💡 Tips & Best Practices

* 🧠 **Duplicate workflows** to test safely
* 🔗 **Connect workflows** with the **Execute Workflow** node
* 🕒 Use **Cron triggers** for scheduling
* 🌐 Use **Webhooks** or **API triggers** for event-based automation
* 🔐 Never commit `.env` files or API keys
* 💾 Backup your workflows periodically

---

## 💼 For Recruiters & Reviewers

This repository showcases:

✅ API & service integrations

✅ Data processing & transformation

✅ LLM (AI) integration for intelligent automation

✅ Real, production-ready workflow design

✅ Clear structure & clean documentation

---

## 🏷 License

This repository is open for **educational and portfolio purposes**.
Feel free to **fork**, **modify**, and **reuse** the workflows for personal learning or inspiration.

---

### 🌟 Author

**Yamin Hossain**
📧 [LinkedIn](https://www.linkedin.com/in/yamin-hossain-38a3b3263/) | 💻 [GitHub](https://github.com/RobinMillford)
