# 📶 Telecom ETL Project using SSIS

## 📌 Project Overview
This ETL project is designed for a **telecommunications company** to process real-time transactional data generated every 5 minutes. The incoming data is in **CSV format** and includes key fields such as **IMSI, IMEI, CELL**, and the **timestamp** of the event.

The project uses **SQL Server Integration Services (SSIS)** to extract, transform, validate, and load this data into a structured **SQL Server** database for further analysis and reporting.

---

## 📁 Data Source

The source CSV files contain transaction data with the following structure:

| Column Name | Data Type | Length | Nullable | Sample              |
|-------------|-----------|--------|----------|---------------------|
| ID          | Int       | -      | No       | 156                 |
| IMSI        | String    | 9      | No       | 310120265           |
| IMEI        | String    | 14     | Yes      | 490154203237518     |
| CELL        | Int       | -      | No       | 123                 |
| LAC         | Int       | -      | No       | 12                  |
| EVENT_TYPE  | String    | 1      | Yes      | 1                   |
| EVENT_TS    | Timestamp | -      | No       | 2020-01-16 07:45:43 |

---

## 🔄 ETL Processing Rules

| Source Column | Transformation Rules                                                                 | Target Column   |
|---------------|----------------------------------------------------------------------------------------|-----------------|
| ID            | As-is                                                                                 | Transaction_id  |
| IMSI          | As-is (Reject if NULL)                                                                | IMSI            |
| IMSI          | Join with IMSI reference to get subscriber_id (If not found, use -99999)              | subscriber_id   |
| IMEI          | Substring (First 8 chars), if null or <15 chars → -99999                              | TAC             |
| IMEI          | Substring (Last 6 chars), if null or <15 chars → -99999                               | SNR             |
| IMEI          | As-is                                                                                 | IMEI            |
| CELL          | As-is (Reject if NULL)                                                                | CELL            |
| LAC           | As-is (Reject if NULL)                                                                | LAC             |
| EVENT_TYPE    | As-is                                                                                 | EVENT_TYPE      |
| EVENT_TS      | Validate format (YYYY-MM-DD HH:MM:SS), Reject if NULL                                 | EVENT_TS        |

---

## ❌ Rejected Records

- Rejected records are stored in a **dedicated rejection table**.
- The **original file name** of each rejected record is **logged** for traceability.

---

## ✅ Audit and Logging

During data loading, the following metrics are captured:

- Total number of transactions in the CSV file.
- Number of successfully inserted records.
- Number of rejected records.
- Linking stored records to the original CSV file name.
- Post-processing: The CSV file is moved to an **archive folder** after successful processing.

---

## 🛠️ Tools & Technologies

- **SQL Server 2019+**
- **SQL Server Integration Services (SSIS)**
- **SQL Server Management Studio (SSMS)**
- **Windows File System** for handling file movement (incoming → archive)

---

## 📊 Project Value

- Ensures **data quality** through strict validation and rejection handling.
- Enables **real-time processing** of telecom transactions.
- Provides a **reliable data model** for analysis and reporting.
- Supports **auditing and traceability** for every processed file.

---

