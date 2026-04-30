# 📊 SCD Type 1 using Alter Row (ADLS → Azure SQL, No Join)

## 🚀 Project Overview

This project demonstrates **Slowly Changing Dimension (SCD) Type 1** using **Mapping Data Flow** in Azure Data Factory.

* **Source:** Azure Data Lake Storage Gen2 (CSV file)
* **Sink:** Azure SQL Database

The pipeline performs **insert and update (overwrite)** operations using **Alter Row transformation** without using a Join.

---

## 🧩 Architecture

```
ADLS (Source File)
        ↓
Mapping Data Flow
        ↓
Alter Row (Upsert Logic)
        ↓
Azure SQL Table (Sink)
```

---

## 🎯 Objective

* Load employee data from ADLS
* Insert new records into Azure SQL
* Update existing records (overwrite)
* Maintain latest state of data (SCD Type 1)

---

## 🗄️ Azure SQL Table (Target)

```sql
CREATE TABLE employee
(
    employee_id INT PRIMARY KEY,
    FirstName NVARCHAR(128),
    salary INT,
    location NVARCHAR(128)
);

INSERT INTO employee VALUES (1,'Jeena',10000,'india');
INSERT INTO employee VALUES (2,'Georgy',5000,'india');
INSERT INTO employee VALUES (4,'Lilly',5000,'UK');
```

---

## 📂 Source Data (ADLS File)

```text
employee_id,FirstName,salary,location
1,Jeena,12000,india
2,Georgy,5000,india
3,David,7000,USA
```

---

## ⚙️ Implementation Steps

### 1️⃣ Source (ADLS)

* Load CSV file from ADLS Gen2 container

---

### 2️⃣ Alter Row Transformation

Apply upsert logic:

```
upsertIf(1=1)
```

👉 Marks all rows for upsert

---

### 3️⃣ Sink (Azure SQL)

* Table: `employee`
* Enable: ✔ Upsert
* Key Column: `employee_id`

---

## 🔄 How It Works

| Condition                    | Action |
| ---------------------------- | ------ |
| Record exists (based on key) | Update |
| Record does not exist        | Insert |

👉 Matching is handled by Azure SQL using the key column.

---

## 📊 Output (After Execution)

| employee_id | FirstName | salary | location |
| ----------- | --------- | ------ | -------- |
| 1           | Jeena     | 12000  | india    |
| 2           | Georgy    | 5000   | india    |
| 3           | David     | 7000   | USA      |
| 4           | Lilly     | 5000   | UK       |

---

## 🔍 Key Features

* Simple pipeline (no Join required)
* Uses sink-based upsert logic
* Implements SCD Type 1 (overwrite)
* Works well for small to medium datasets

---

## ⚠️ Limitations

* Performs **blind updates** (updates even if data is unchanged)
* No change detection logic
* Not optimal for large-scale production pipelines

---

## ⚡ Performance Tips

* Add index on `employee_id`
* Use staging option in Azure SQL sink
* Avoid unnecessary updates for large datasets

---

## ❗ Common Mistakes

* Not setting key column in sink ❌
* Assuming Alter Row does comparison ❌
* Using this approach for SCD Type 2 ❌

---

## 🧠 Interview Explanation

**Q: How did you implement SCD Type 1 without Join?**

**Answer:**

> I used `upsertIf(1=1)` in Alter Row and configured a key column (employee_id) in the Azure SQL sink. The sink performs key-based matching to decide whether to insert or update records. This approach simplifies the pipeline but performs blind updates since it does not include change detection.

---

## 🛠️ Tools & Technologies

* Azure Data Factory
* Azure Data Lake Storage Gen2
* Azure SQL Database

---

## 📌 Future Enhancements

* Add change detection using hash comparison
* Implement SCD Type 2
* Add incremental loading (watermark)
* Add logging and error handling

---

## 👤 Author

Jeena Paul

---

## ⭐ Conclusion

This project demonstrates a simple and effective implementation of **SCD Type 1 using Alter Row without Join** in Azure Data Factory, suitable for learning and basic ETL pipelines.
