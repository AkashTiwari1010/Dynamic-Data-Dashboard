# Dynamic Excel/CSV Data Analysis Dashboard

A production-ready, full-stack reusable analytics dashboard web application built with **Next.js, TypeScript, Tailwind CSS, Prisma ORM, and PostgreSQL**. 

Upload any Excel (`.xlsx`, `.xls`) or CSV file and automatically run an end-to-end data pipeline: **Upload → Validate → Clean → Deduplicate → Store → Query → Analyze → Display**.

---

## 🌟 Key Features

* 📁 **Smart File Upload**: Drag-and-drop support for CSV, XLS, and XLSX files with type/size validation and progress tracking.
* 🧹 **Automated Data Cleaning Engine**:
  * **Duplicate Detection**: Identifies and strips exact duplicate records while maintaining audit stats.
  * **Missing Data Detection**: Identifies `NULL`, blank, and empty values column-by-column.
  * **Value Standardization**: Trims whitespace and normalizes text casing (e.g., `Delhi`, `delhi`, `DELHI` -> `Delhi`).
  * **Type Inference**: Automatically casts values into `INTEGER`, `DECIMAL`, `DATE`, `CATEGORICAL`, or `TEXT`.
  * **Numeric Metric Validation**: Ensures currency, sales, fees, salary, and score fields are cleaned and stored as numbers.
* 🗄️ **Relational Database Storage**: Flexible PostgreSQL JSONB schema via Prisma ORM for seamless storage of structured datasets without rigid schema changes.
* 🧠 **Dynamic Column Detection**: Intelligently classifies columns (Time, Categorical, Numeric Metrics, Performance) across various domains (Education, Sales, HR/Employees, Retail, etc.).
* ⚡ **16 One-Click Analysis Operations**: Pre-configured analytical actions executing directly against backend database queries.
* 📊 **Rich SaaS Dashboard UI**: KPI metrics cards, interactive charts (Recharts), dynamic paginated preview tables, search, and sorting.
* 🧪 **One-Click Sample Dataset**: Pre-loaded education sample dataset for instant testing.

---

## 🏗️ Tech Stack

| Layer | Technology |
| :--- | :--- |
| **Framework** | Next.js 15 (App Router), React 19, TypeScript |
| **Styling** | Tailwind CSS, Lucide React Icons, Framer Motion |
| **Data Viz** | Recharts (Bar, Line, Pie, Area Charts) |
| **Database** | PostgreSQL |
| **ORM** | Prisma ORM |
| **File Parsing** | SheetJS (`xlsx`) & `papaparse` |
| **HTTP & API** | Next.js API Routes / Native Fetch API |

---

## 🔄 End-to-End Pipeline

```
  [ User Upload ] 
        │
        ▼
  [ File Parsing & Validation ] (.xlsx / .csv)
        │
        ▼
  [ Cleaning Engine ] (Type Detection ➔ Standardize ➔ Deduplicate ➔ Missing Value Audit)
        │
        ▼
  [ PostgreSQL Database ] (Stored via Prisma JSONB Records + Column Metadata)
        │
        ▼
  [ Analytics Query Engine ] (Automated SQL Aggregations & Analytics Execution)
        │
        ▼
  [ SaaS Dashboard UI ] (16 Interactive Buttons ➔ Dynamic Charts & KPI Cards)
```

---

## 🎯 16 Dynamic One-Click Analysis Operations

The dashboard dynamically maps operations depending on the detected column types:

| # | Analysis Operation | Description | Target Column Types | Education Domain Example |
|---|---|---|---|---|
| 1 | **Total Count / Enrollment** | Calculates total dataset records and key totals | All / Numeric | Total Students / Total Enrollment |
| 2 | **Year-Wise Analysis** | Aggregates metrics grouped by date/year | Time/Date | Year-Wise Enrollment |
| 3 | **Category-Wise Analysis** | Groups record count by main categorical field | Categorical | Branch-Wise / Dept-Wise Enrollment |
| 4 | **Category Numeric Analysis** | Aggregates numeric sums grouped by category | Categorical + Numeric | Branch-Wise Total Fees / Revenue |
| 5 | **Average Metric Value** | Calculates overall & group averages | Numeric | Average Fees / Average Salary |
| 6 | **Performance Analysis** | Evaluates performance scores, ratings, placements | Performance / Metric | Placement & Score Distribution |
| 7 | **Maximum Value** | Identifies highest metrics overall & per category | Numeric | Maximum Placement / Max Salary |
| 8 | **Minimum Value** | Identifies lowest metrics overall & per category | Numeric | Minimum Placement / Min Salary |
| 9 | **Overall Average** | Consolidated metric averages across dataset | Numeric | Overall Average Metrics |
| 10 | **Count by Category** | Counts sub-categories or sections per branch | Categorical | Sections count per Branch |
| 11 | **Top Categories** | Highlights highest performing categories | Categorical + Metric | Top Branches / Top Products |
| 12 | **Lowest Categories** | Highlights lowest performing categories | Categorical + Metric | Lowest Branches / Lowest Sellers |
| 13 | **Year + Category Analysis** | Cross-tabulates metrics across Time + Category | Date + Categorical | Year + Branch Enrollment Trends |
| 14 | **Duplicate Audit** | Shows duplicate count pre- and post-cleaning | Audit Metadata | Duplicate Records Analysis |
| 15 | **Missing Data Audit** | Details null/blank counts & percentages by column | Audit Metadata | Column Missing Values Breakdown |
| 16 | **Overall Summary** | Full consolidated statistical profile | Entire Dataset | Complete Statistical Summary |

---

## 🗃️ Database Architecture (Prisma Schema)

```prisma
model Dataset {
  id               String          @id @default(uuid())
  name             String
  originalFileName String
  fileType         String
  originalRowCount Int
  duplicateCount   Int
  cleanedRowCount  Int
  columnCount      Int
  createdAt        DateTime        @default(now())
  columns          DatasetColumn[]
  records          DatasetRecord[]
}

model DatasetColumn {
  id           String   @id @default(uuid())
  datasetId    String
  columnName   String
  detectedType String   // INTEGER, DECIMAL, DATE, CATEGORICAL, TEXT
  nullable     Boolean
  dataset      Dataset  @relation(fields: [datasetId], references: [id], onDelete: Cascade)
}

model DatasetRecord {
  id        String   @id @default(uuid())
  datasetId String
  rowIndex  Int
  data      Json     // JSONB key-value mapping of sanitized row fields
  dataset   Dataset  @relation(fields: [datasetId], references: [id], onDelete: Cascade)

  @@index([datasetId])
}
```

---

## 🔌 API Specification

### Dataset Management
* **`POST /api/datasets/upload`**: Uploads CSV/XLSX file, triggers cleaning engine, and stores dataset.
* **`GET /api/datasets`**: Retrieves list of all processed datasets with metadata.
* **`GET /api/datasets/:id`**: Gets dataset details and metadata.
* **`DELETE /api/datasets/:id`**: Removes dataset and associated column metadata/records.

### Data Preview & Quality
* **`GET /api/datasets/:id/preview?page=1&limit=20&search=`**: Paginated data preview with column filtering.
* **`GET /api/datasets/:id/quality`**: Provides audit stats (original rows, duplicates removed, missing count per column, types).

### Analytical Queries
* **`POST /api/datasets/:id/analyze`**: Dynamic custom backend analysis execution.
* **`GET /api/datasets/:id/analysis/:analysisType`**: Executes one of the 16 pre-defined dynamic backend analyses.

---

## 🚀 Getting Started

### 1. Prerequisites
* **Node.js**: v18.x or higher
* **PostgreSQL**: Local instance or cloud database (e.g. Supabase, Neon)

### 2. Environment Setup
Create a `.env` file in the root directory:

```env
DATABASE_URL="postgresql://postgres:password@localhost:5432/dynamic_analytics?schema=public"
NEXT_PUBLIC_APP_URL="http://localhost:3000"
```

### 3. Installation & Database Migration

```bash
# Install dependencies
npm install

# Run Prisma schema migrations
npx prisma db push

# Generate Prisma Client
npx prisma generate
```

### 4. Development Server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## 🛠️ Folder Structure

```text
├── app/
│   ├── api/
│   │   └── datasets/       # API routes for upload, preview, quality, and analysis
│   ├── dashboard/          # Analytics Dashboard pages
│   ├── layout.tsx          # Root layout with sidebar navigation
│   └── page.tsx             # Landing Page & File Upload Dropzone
├── components/
│   ├── dashboard/          # Cards, Charts, Tables, & 16 Analysis Action Buttons
│   ├── upload/             # File Dropzone & Progress Indicators
│   └── ui/                 # Reusable UI components (Buttons, Modals, Badges)
├── lib/
│   ├── cleaning/           # Data cleaning & deduplication engine
│   ├── detect/             # Column classification logic
│   ├── prisma.ts           # Prisma database client instance
│   └── utils.ts            # Helper utilities
├── prisma/
│   └── schema.prisma       # Relational database schema
└── public/
    └── sample-data.csv     # Sample Education Dataset
```

---

## 📄 License
This project is open-source and available under the [MIT License](LICENSE).
