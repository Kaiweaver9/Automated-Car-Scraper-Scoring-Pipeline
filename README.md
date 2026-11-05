# Automated Car Scraper & Scoring Pipeline

An automated system that pulls pre-owned car listings from CarGurus, scores them based on custom value criteria, and ranks them live inside a custom Salesforce dashboard.

---

## 🚀 System Overview & Live Leaderboard

> *Live Salesforce List View sorting inventory by total score, landed price, and distance.*

<img width="1366" alt="Car Leaderboard Salesforce List View" src="assets/Car Scraper List View.png" />

---

## 🛠️ Tech Stack & Tools

* **Data Scraping:** Node.js, Puppeteer (Automated Web Browser)
* **Scheduled Automation:** GitHub Actions (Runs the scraper automatically every 6 hours)
* **Database & Storage:** Salesforce Custom Object (`Car_Candidate__c`)
* **Scoring Logic:** Salesforce Formula Field (`Total_Score__c`)
* **Process Automation:** Salesforce Flow Builder (Automated Email Alerts)
* **Data Quality:** Validation Rules (Data Cleaning & Link Verification)
* **Data Ingestion:** Salesforce REST API (Pushes web scraper data into Salesforce)

---

## ⚙️ How the Pipeline Works

1. **Automated Web Scraping (GitHub Actions):** 
   * A scheduled job runs every 6 hours to open a headless browser via Puppeteer.
   * The script collects price, mileage, location, and listing URLs from CarGurus.
2. **Data Import:**
   * The collected data is sent directly into Salesforce custom records (`Car_Candidate__c`) using the REST API.
3. **Automatic Scoring (`Total_Score__c`):**
   * Every new car record is calculated using a custom formula score.
   * The score adjusts based on price, mileage, year, previous owners, and distance.
4. **Sorted Leaderboard:**
   * Cars appear on a sorted Salesforce List View (**Car Leaderboard**), highlighting top choices (scoring 75+) with listing links ready to view.

---

## 🧮 Custom Scoring Logic (Formula Engine)

The custom formula computes a real-time score (`Total_Score__c`) using simple weighted rules:
* **Mileage (up to 40 pts):** Higher mileage drops the score based on standard wear curves.
* **Owner History (up to 10 pts):** Single-owner cars get full points; 2 owners get 8 points; 3+ owners get 6 points.
* **Distance & Shipping:** Deducts points if the car is farther away or requires transport fees.
* **Price Adjustment:** Rewards cars listed below baseline market values.

<details open>
<summary><b>Visual Proof: Formula Builder Logic</b></summary>
<br>
<img width="1366" alt="Total Score Formula Field Logic" src="assets/car total score formula field.png" />
<p><i>The underlying formula logic calculating scores dynamically across mileage, year, previous owners, and transport math.</i></p>
</details>

---

## ⚡ Declarative Process Automation (Flows)

When incoming records hit the database, an automated record-triggered flow monitors candidate scores in real time. If a candidate vehicle meets or exceeds the target threshold (Score >= 70), the system automatically triggers an email notification to alert stakeholders immediately without requiring manual review.

<details open>
<summary><b>Visual Proof: Record-Triggered Flow</b></summary>
<br>
<img width="1366" alt="Record-Triggered Flow Builder" src="assets/car scraper record-triggered flow.png" />
<p><i>Active Flow Builder layout showing the decision node and automated email action.</i></p>
</details>

---

## 🛡️ Data Quality & Integrity (Validation Rules)

To maintain database hygiene and prevent corrupted data from entering Salesforce through external API payloads, a custom validation rule evaluates all incoming listing links. Any record missing a valid CarGurus URL structure is blocked at the database layer with a field-level error message.

<details open>
<summary><b>Visual Proof: Validation Rule Setup</b></summary>
<br>
<img width="1366" alt="Validation Rule Configuration" src="assets/car candidate URL validation rule.png" />
<p><i>Active validation rule formula blocking malformed URLs and enforcing field format standards.</i></p>
</details>

---

## 📂 Project Structure

```text
├── .github/
│ └── workflows/
│ └── scraper-cron.yml # Runs the scraper every 6 hours
├── assets/ # Screenshots of Salesforce setup & architecture
├── src/
│ ├── scraper.js # Web scraper script
│ ├── transformer.js # Cleans and formats data
│ └── sfConnector.js # Sends data to Salesforce via API
├── salesforce/
│ ├── objects/ # Custom object files
│ └── formulas/ # Scoring formula code
└── README.md
