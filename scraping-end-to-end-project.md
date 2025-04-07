## 🧠 Data Engineering Training Project: Job Listings Scraper + LLM Agent

 `Note: The codes below is a template and will not run.`

### 🌐 Objective:
Scrape job listings from **RemoteOK** or **WeWorkRemotely**, clean and store the data, automate the process, and build a simple **LLM (Large Language Model) agent** to answer analytical questions based on the job data.

---

### 📦 Project Overview:

| Phase | Goal |
|-------|------|
| 1. Scraping | Extract job listings from RemoteOK or WeWorkRemotely |
| 2. Storage | Save the data to a CSV or SQLite database |
| 3. Cleaning | Clean and structure the data for analysis |
| 4. Automation | Make the process run automatically (daily or weekly) |
| 5. LLM Agent | Use the data to build a chatbot that answers questions |

---

## 🛠 Tools to Choose From

| Task | Tools |
|------|------|
| Scraping | Python, `requests`, `BeautifulSoup`, `Scrapy`, `Selenium` |
| Storage | CSV, SQLite, PostgreSQL, Pandas |
| Automation | Python script + Task Scheduler / `cron`, Prefect, Airflow |
| LLM Agent | OpenAI API (GPT-3.5), LangChain, LlamaIndex, Hugging Face Transformers |

---

## 🪼 Step-by-Step Instructions

### 🔹 Step 1: Web Scraping

**Goal**: Extract job data (title, company, location, date, tags) from RemoteOK or WeWorkRemotely.

#### Example with RemoteOK:
```bash
pip install requests beautifulsoup4
```

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

url = "https://remoteok.com/"
headers = {'User-Agent': 'Mozilla/5.0'}

response = requests.get(url, headers=headers)
soup = BeautifulSoup(response.text, 'html.parser')

jobs = []
for row in soup.select('tr.job'):
    title = row.get('data-position')
    company = row.get('data-company')
    tags = [tag.text for tag in row.select('.tag')]
    date = row.select_one('time')['datetime'] if row.select_one('time') else None
    jobs.append({'title': title, 'company': company, 'tags': tags, 'date_posted': date})

df = pd.DataFrame(jobs)
df.to_csv('remoteok_jobs.csv', index=False)
print("Scraping completed and saved to CSV.")
```

#### ✅ Alternative: Try WeWorkRemotely — layout is different but similar method.

---

### 🔹 Step 2: Store the Data

**Options**:
- Save to a **CSV** file
- Save to a **SQLite** database

Example for SQLite:
```python
import sqlite3

conn = sqlite3.connect('jobs.db')
df.to_sql('jobs', conn, if_exists='replace', index=False)
conn.close()
```

---

### 🔹 Step 3: Clean the Data

Tasks:
- Remove duplicates
- Fill missing values
- Normalize text
- Flatten lists

Example:
```python
df.drop_duplicates(inplace=True)
df['tags'] = df['tags'].apply(lambda x: ', '.join(x) if isinstance(x, list) else '')
df['title'] = df['title'].str.strip().str.lower()
```

---

### 🔹 Step 4: Automate the Process

#### Option 1: Simple Scheduling
- Use `cron` or **Task Scheduler** to run daily

#### Option 2: Prefect
```bash
pip install prefect
```

```python
from prefect import flow, task

@task
def scrape():
    # scraping logic here
    pass

@task
def clean():
    # cleaning logic here
    pass

@flow
def job_scraper_flow():
    scrape()
    clean()

job_scraper_flow()
```

Then:
```bash
prefect deployment build scraper.py:job_scraper_flow -n "daily-job-scraper"
prefect deployment apply job_scraper_flow-deployment.yaml
```

---

### 🔹 Step 5: Build a LLM Agent

**Goal**: Answer questions like:
- "What company posted the most jobs?"
- "Which job title is most common?"
- "What job category pays the most?"
- "How many job postings in the last month mention 'Python'?"
- "Which skillset is trending based on frequency of appearance?"


#### Option A: OpenAI + LangChain
```bash
pip install openai langchain pandas
```

```python
import pandas as pd
from langchain.llms import OpenAI
from langchain.agents import create_pandas_dataframe_agent

df = pd.read_csv('remoteok_jobs.csv')

llm = OpenAI(api_key="YOUR_API_KEY", temperature=0)
agent = create_pandas_dataframe_agent(llm, df, verbose=True)

agent.run("Which company posted the most jobs?")
```

#### Option B: Use ChatGPT + CSV Upload
- Upload the CSV to ChatGPT Pro and ask questions directly.

---

## 📚 Bonus Learning Resources

### Web Scraping
- [BeautifulSoup Docs](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
- [Scrapy Tutorial](https://docs.scrapy.org/en/latest/intro/tutorial.html)
- [Web Scraping 101](https://realpython.com/beautiful-soup-web-scraper-python/)

### Automation
- [Python Cron Jobs](https://opensource.com/article/19/7/python-cron-jobs)
- [Prefect Docs](https://docs.prefect.io/)

### LLM Agents
- [LangChain Quickstart](https://python.langchain.com/docs/get_started/introduction)
- [OpenAI API Docs](https://platform.openai.com/docs/)
- [LlamaIndex Docs](https://docs.llamaindex.ai/)

---

## ✅ Final Project Deliverable

Each trainee should:
- Upload their Python code to GitHub
- Submit a README explaining:
  - What site they scraped
  - Columns extracted
  - How data was cleaned/stored
  - Automation steps
  - Example question + LLM response

