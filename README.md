<p align="center">
  <img src="docs/assets/banner.svg" alt="MedCare Pharma: AI-assisted Demand Sensing and Autonomous Procure-to-Pay" width="100%">
</p>

<p align="center">
  <a href="https://cognizant-hackathon-p1.vercel.app/"><img alt="Live demo" src="https://img.shields.io/badge/Live%20demo-Demand%20Sensing-0E7C86?style=for-the-badge&logo=vercel&logoColor=white"></a>
  <a href="#-the-journey-of-one-order"><img alt="Walkthrough" src="https://img.shields.io/badge/Walkthrough-18%20screens-123A7A?style=for-the-badge"></a>
  <a href="#-my-role"><img alt="My role" src="https://img.shields.io/badge/My%20role-OCR%20%2B%203--way%20match-F59E0B?style=for-the-badge"></a>
</p>

<p align="center">
  <img alt="Next.js" src="https://img.shields.io/badge/Next.js-14-000000?logo=nextdotjs">
  <img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white">
  <img alt="Python" src="https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white">
  <img alt="FastAPI" src="https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white">
  <img alt="Flask" src="https://img.shields.io/badge/Flask-000000?logo=flask">
  <img alt="XGBoost" src="https://img.shields.io/badge/XGBoost-ML-EB5B28">
  <img alt="Hugging Face" src="https://img.shields.io/badge/TrOCR-Hugging%20Face-FFD21E?logo=huggingface&logoColor=black">
  <img alt="SQLite" src="https://img.shields.io/badge/SQLite-003B57?logo=sqlite&logoColor=white">
</p>

---

## 💊 The problem, in 30 seconds

A pharmaceutical distributor runs warehouses (**distribution centres, or DCs**) across India. Three things keep going wrong:

| 😟 Pain | 💸 What it costs |
|---|---|
| **Medicines run out** at one warehouse because nobody saw demand rising | Patients and pharmacies wait; emergency orders cost more |
| **Stock expires** in another warehouse that had too much | Write-offs. The live demo shows **₹12.4 lakh** of stock at risk of expiry |
| **Invoices are checked by hand** against orders and deliveries | Slow payments, and overcharges slip through |

**MedCare Pharma connects all three into one pipeline.** It forecasts the shortage and moves near-expiry stock between warehouses when that's cheaper. Otherwise it raises a purchase request, picks the best supplier with a transparent score, tracks the delivery, reads the invoice with OCR, and auto-approves it only if **order, delivery and invoice all agree**.

---

## 🗺️ How it all connects

```mermaid
flowchart LR
    A["📈 Demand Sensing<br/>forecast + reorder point"] -->|shortage| B{"Cheaper to move<br/>near-expiry stock<br/>from another DC?"}
    B -->|yes| T["🚚 Inter-DC transfer"]
    B -->|no| R["📝 Purchase requisition"]
    C["💬 Chatbot<br/>'Need 500 paracetamol<br/>at Siliguri ASAP'"] --> R
    R --> S["🏭 AI supplier scoring<br/>price · lead time · on-time · reliability"]
    S --> P["📄 Purchase order"]
    P --> G["📦 Goods receipt"]
    G --> I["🧾 Invoice<br/>(OCR or generated)"]
    I --> M{"✅ 3-way match<br/>PO = GR = Invoice<br/>within ±2%?"}
    M -->|yes| OK["💰 Auto-approved"]
    M -->|no| AP["👩‍💼 Approvals queue<br/>approve · reject · escalate"]
```

A shortage the forecast finds and a request typed into the chatbot become **the same kind of requisition**, so both flow through the rest of the pipeline the same way.

---

## 🎬 The journey of one order

*All screenshots are from the real app running locally. Nothing is mocked up.*

### 1️⃣ Spot the shortage before it happens

<table>
<tr>
<td width="50%"><img src="docs/screenshots/app/02-demand-sensing.jpg" alt="Demand sensing cards"></td>
<td width="50%"><img src="docs/screenshots/app/02b-ml-forecast-comparison.jpg" alt="ML forecast comparison"></td>
</tr>
<tr>
<td>Every medicine × warehouse is forecast and compared to its <b>reorder point</b>. Here, Paracetamol at Siliguri has 120 units but needs 606. The app notices <b>Chennai has 205 units expiring in 35 days</b>, and that moving them costs ₹80.85 against ₹114.81 to buy new.</td>
<td>An <b>XGBoost</b> model can be switched on for comparison. On held-out data it scores 12% error against 10% for a naive baseline, so it stays <i>opt-in</i>. The app reports that result honestly instead of hiding it.</td>
</tr>
</table>

### 2️⃣ Ask for stock in plain English

<table>
<tr>
<td width="50%"><img src="docs/screenshots/app/04-chatbot-requisition.jpg" alt="Chatbot creates a requisition"></td>
<td width="50%"><img src="docs/screenshots/app/03-demand-sensing-requisition-created.jpg" alt="Requisition created from a demand alert"></td>
</tr>
<tr>
<td><i>"Need 500 units of paracetamol at Siliguri DC ASAP"</i> becomes a structured requisition: product, quantity, warehouse, and <b>CRITICAL</b> urgency (because of "ASAP").</td>
<td>Or skip typing: one click on a shortage card raises the same requisition automatically.</td>
</tr>
</table>

### 3️⃣ Let AI pick the supplier, and explain why

<table>
<tr>
<td width="50%"><img src="docs/screenshots/app/07-why-this-supplier.jpg" alt="Why this supplier breakdown"></td>
<td width="50%"><img src="docs/screenshots/app/08-no-qualifying-supplier.jpg" alt="No qualifying supplier"></td>
</tr>
<tr>
<td>Suppliers are scored on <b>price 30% · lead time 20% · on-time delivery 25% · reliability 25%</b>. The winner and the runners-up are shown with their scores, and a <b>predicted delivery risk</b> is attached to the PO.</td>
<td>Hard rules come first: <b>GMP certification</b>, defect rate, minimum order quantity and spare capacity. If nobody qualifies, the app says so instead of guessing.</td>
</tr>
</table>

### 4️⃣ Receive the goods, and catch the wrong quantity

<table>
<tr>
<td width="50%"><img src="docs/screenshots/app/11-goods-receipt-received.jpg" alt="Goods received"></td>
<td width="50%"><img src="docs/screenshots/app/12-over-delivery-flagged.jpg" alt="Over-delivery flagged"></td>
</tr>
<tr>
<td>Deliveries are recorded against the PO. Partial deliveries are tracked as progress.</td>
<td><b>230 units arrived for a 200-unit order?</b> That's flagged immediately, not quietly accepted.</td>
</tr>
</table>

### 5️⃣ Read the invoice, then match all three documents

<table>
<tr>
<td width="50%"><img src="docs/screenshots/app/15-three-way-match-pass.jpg" alt="3-way match pass"></td>
<td width="50%"><img src="docs/screenshots/app/16-three-way-match-needs-review.jpg" alt="3-way match needs review"></td>
</tr>
<tr>
<td><b>3-way match</b>: the Purchase Order (what we asked for), the Goods Receipt (what arrived) and the Invoice (what we're billed) agree within ±2%, so the invoice is <b>Auto-Approvable</b>.</td>
<td>A mismatch drops the score to 75%. The failing check is highlighted, and the invoice cannot be approved for payment automatically.</td>
</tr>
</table>

### 6️⃣ A human decides only the exceptions

<table>
<tr>
<td width="50%"><img src="docs/screenshots/app/17-approvals-queue.jpg" alt="Approvals queue"></td>
<td width="50%"><img src="docs/screenshots/app/01-dashboard.jpg" alt="Procurement dashboard"></td>
</tr>
<tr>
<td>Only flagged invoices reach a person, with the reason spelled out and <b>Approve / Reject / Escalate</b> buttons.</td>
<td>The dashboard tracks requisitions, auto-generated POs, OCR success and the 3-way-match rate live.</td>
</tr>
</table>

<details>
<summary><b>📸 More screens</b> (requisitions, purchase orders, suppliers, invoices, analytics)</summary>
<br>

| | |
|---|---|
| ![Requisition detail](docs/screenshots/app/05-requisition-run-ai-sourcing.jpg) | ![PO auto-generated](docs/screenshots/app/06-po-auto-generated.jpg) |
| ![Suppliers](docs/screenshots/app/09-suppliers.jpg) | ![Goods receipt scanning](docs/screenshots/app/10-goods-receipt-scanning.jpg) |
| ![Invoice upload](docs/screenshots/app/13-invoice-upload-ocr.jpg) | ![Invoices matched and flagged](docs/screenshots/app/14-invoices-matched-and-flagged.jpg) |
| ![Analytics](docs/screenshots/app/18-analytics.jpg) | |

</details>

---

## 🌐 Live demo: the Demand Sensing planning suite

👉 **[cognizant-hackathon-p1.vercel.app](https://cognizant-hackathon-p1.vercel.app/)**

The deployed version is the **planning side** (Part 1): inventory, expiry risk, warehouses, replenishment and alerts across 6 DCs.

> ⏳ **First visit may take ~50 seconds.** The backend runs on a free server that sleeps when idle. If the boxes say "Failed to load", wait a moment and refresh.

<table>
<tr>
<td width="50%"><img src="docs/screenshots/live-p1/01-dashboard.jpg" alt="Live dashboard"></td>
<td width="50%"><img src="docs/screenshots/live-p1/04-expiry-risk.jpg" alt="Expiry risk"></td>
</tr>
<tr>
<td align="center">Network dashboard: 30.9K units, 12 critical SKUs</td>
<td align="center">Expiry risk: batches expiring soon, ₹12.4L at risk</td>
</tr>
</table>

<details>
<summary><b>📸 All live-demo pages</b></summary>
<br>

| | |
|---|---|
| ![Demand forecast](docs/screenshots/live-p1/02-demand-forecast.jpg) | ![Inventory](docs/screenshots/live-p1/03-inventory.jpg) |
| ![Warehouses](docs/screenshots/live-p1/05-warehouses.jpg) | ![Replenishment planner](docs/screenshots/live-p1/06-replenishment-planner.jpg) |
| ![Alerts](docs/screenshots/live-p1/07-alerts.jpg) | ![Reports](docs/screenshots/live-p1/08-reports.jpg) |

</details>

The full procure-to-pay flow above runs locally ([setup below](#-run-it-yourself)).

---

## 👩‍💻 My role

> **Harshvardhan Rajgarhia** ([@Harsh1574](https://github.com/Harsh1574)): owner of the **invoice OCR module** and the **3-way match validation** used by the live app.

**1. Built an OCR pipeline for handwritten and scanned invoices** (`src/extract.py`)

<table>
<tr>
<td width="38%"><img src="docs/assets/ocr-handwritten-invoice.jpg" alt="Handwritten test invoice"></td>
<td>

```mermaid
flowchart TB
    a["📷 Invoice photo"] --> b["OpenCV: grayscale +<br/>adaptive threshold"]
    b --> c["Erase ruled table lines<br/>(morphological opening)"]
    c --> d["Split into text lines<br/>(row-ink projection)"]
    d --> e["Microsoft TrOCR<br/>reads each line"]
    e --> f["Regex parser finds<br/>Ordered · Received · Price · Amount<br/>(tolerant to misreads)"]
    f --> g["✅ 3-way match"]
```

</td>
</tr>
</table>

On this handwritten test invoice, the match **should** fail: 6,100 received against 6,000 ordered is an over-delivery, and ₹1,36,000 doesn't equal 6,100 × ₹210.

**2. Wrote the 3-way match rules** (`src/validate.py`)

±2% tolerance on quantity ordered, quantity received and amount reconciliation, plus a strict over-delivery check. **This exact function powers the live app's matching**: the procurement service imports it directly, so OCR'd and generated invoices are judged by the same rules.

**3. Made an engineering call.** Testing on real invoice photos showed TrOCR (a model trained on single handwritten lines) wasn't reliable enough on full, table-heavy invoices. For the live demo, the team switched extraction to **Google Cloud Vision**, with a human review step, while keeping my validation logic as the decision-maker.

---

## 🧠 Supply-chain ideas inside

| Concept | In plain English | Where you see it |
|---|---|---|
| **Reorder point** | "When stock falls below this, order now." Daily demand × lead time, plus a 7-day safety buffer | Demand Sensing cards |
| **Safety stock** | Extra cushion for surprise demand | Reorder-point maths |
| **Demand sensing** | Using fresh signals (promotions, distributor orders, season) to adjust the forecast | Forecast notes on each card |
| **Inter-DC transfer** | Move stock that will expire elsewhere before buying new | "Transfer recommended" |
| **Weighted supplier scoring** | Rank suppliers on several factors, not just price | "Why this supplier?" |
| **GMP compliance** | Pharma ingredients must come from certified suppliers | Hard filter in sourcing |
| **3-way match** | PO = Goods Receipt = Invoice before paying | 3-Way Matching page |
| **Management by exception** | People only review what fails the rules | Approvals queue |

---

## 🏗️ Under the hood

```mermaid
flowchart LR
    U["🖥️ Next.js 14 web app<br/>:3000"] --> X["API routes<br/>(server-side proxy)"]
    X --> CB["💬 Chatbot · Flask<br/>:8000"]
    X --> PO["🏭 Procurement · FastAPI<br/>:8001"]
    X --> DS["📈 Demand Sensing · FastAPI<br/>:8003"]
    X -.-> GV["☁️ Google Cloud Vision<br/>(invoice OCR)"]
    CB --- D1[("requisitions.db")]
    PO --- D2[("scm_project.db")]
    DS --- D3[("demand_sensing.db<br/>+ CSV history")]
    PO -. reuses .-> V["src/validate.py<br/>3-way match"]
```

**Think of it as a restaurant.** The web app is the dining room, the three Python services are separate kitchens, and the API routes are the waiters. The browser never walks into a kitchen directly.

<details>
<summary><b>📈 How the forecast works</b></summary>
<br>

- **Default forecast is rule-based on purpose**: a 14-day moving average × a seasonal multiplier (1.15), plus small, named adjustments for active promotions and a rising distributor-order trend (1.05). Every number traces back to one line of arithmetic, so a planner can see *why* it recommended something.
- **Reorder point** = forecast/day × (lead time + 7 safety days). Recommendations are capped by the warehouse's free capacity.
- A shortage that stays **Critical for more than 24 hours** is escalated to the Procurement Lead.
- **XGBoost experiment**: lag features (1/7/14/30 days), rolling mean/std (7/14/28), calendar, promo and distributor signals, with a **time-based** hold-out (last 14 days per series) so no future data leaks into training. Result: WMAPE 12.0% against 10.0% for the naive baseline. More history, not a fancier model, is the real lever.

</details>

<details>
<summary><b>💬 How the chatbot reads requests</b></summary>
<br>

It's a rule-based form-filler, not a chat model:
- **Quantity**: a regex finds a number plus a unit word ("500 units").
- **Product**: matched against an alias table with fuzzy fallback ("brufen" finds Ibuprofen 400mg).
- **Warehouse**: matched against DC city names.
- **Urgency**: "ASAP/urgent" gives CRITICAL, "by Friday/soon" gives HIGH, anything else gives MEDIUM.
- Anything missing is **filled with a default and flagged as assumed**, never silently guessed. An optional LLM mode (Claude) exists behind `EXTRACTION_MODE=llm`.

</details>

<details>
<summary><b>🧾 How invoices are matched</b></summary>
<br>

- **Generate Invoice** builds an invoice from the PO and receipt with small random variations. About 1 in 4 is deliberately out of tolerance, so the exception path can be demoed.
- **Upload Invoice** sends a photo or scan to Google Cloud Vision; a person reviews and corrects the extracted fields before submitting.
- Both go through the same `three_way_match()`: ±2% on quantity ordered, quantity received and total-amount reconciliation, plus a strict over-delivery check.

</details>

<details>
<summary><b>⚠️ Known limitations (being upfront)</b></summary>
<br>

- The XGBoost forecast doesn't yet beat the naive baseline on 90 days of data, so it's opt-in and labelled experimental.
- Goods Receipt's "IoT sensor scan" is a manual quantity entry with a short animation, not a real sensor feed.
- Every stage needs one human click (by design, so the demo stays controllable). "Autonomous" means the decisions are automated, not the clicks.
- A few dashboard and analytics charts are labelled **Illustrative data**, since a demo database has no month-over-month history.
- Supplier unit costs in the synthetic catalogue are low, so some PO totals look small.
- More detail: [docs/TECHNICAL_NOTES.md](docs/TECHNICAL_NOTES.md) and [models/README.md](models/README.md).

</details>

---

## 🚀 Run it yourself

Needs **Python 3.10–3.12** and **Node.js 18+**.

```bash
git clone https://github.com/Harsh1574/Medcare-Pharma.git
cd Medcare-Pharma

python -m venv venv
venv\Scripts\activate            # Windows  (macOS/Linux: source venv/bin/activate)
pip install flask fastapi uvicorn reportlab xgboost scikit-learn
npm install
```

Optional, for real invoice OCR: create `.env.local` containing `GOOGLE_CLOUD_VISION_API_KEY=your-key`.

Start each in its own terminal (with the venv activated):

| Service | Command | Port |
|---|---|---|
| Chatbot | `cd chatbot && python app.py` | 8000 |
| Procurement | `uvicorn po_generation.main:app --port 8001` | 8001 |
| Demand Sensing | `uvicorn demand_sensing.main:app --port 8003` | 8003 |
| Web app | `npm run dev` | **3000** ← open this |

**Try this path:** Demand Sensing → *Create Requisition* → Purchase Requisitions → *Run AI Sourcing* → Purchase Orders → *Send* → Goods Receipt → *Record Delivery* → Invoices → *Generate Invoice* → 3-Way Matching.

> The legacy TrOCR pipeline (`python src/main.py`) additionally needs `torch transformers opencv-python pillow sentencepiece`.

<details>
<summary><b>📁 Project structure</b></summary>
<br>

```
app/              Next.js pages + app/api/* proxy routes
components/       UI components (Radix + Tailwind)
lib/              Frontend API client and types
chatbot/          Flask: natural-language → requisition
po_generation/    FastAPI: sourcing, POs, goods receipt, invoices, matching
demand_sensing/   FastAPI: forecasting, replenishment, transfers, ML comparison
src/              OCR pipeline (TrOCR + OpenCV) and 3-way match validation
data/             Synthetic demand, supplier, promo and distributor data
models/           Trained model artifacts and notes
docs/             Screenshots, assets, technical notes
```

</details>

---

## 🤝 Team

Built in a team of five for the **Cognizant NPN Supply Chain Hackathon**: Debopam Ghosh, Chandana Jana, Dipti Choubey, **Harshvardhan Rajgarhia**, Subhodeep Ghosh.

<p align="center"><sub>MedCare Pharma is a fictional company. All demand, supplier and invoice data is synthetic.</sub></p>
