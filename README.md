# 🛒 Amazon Apparel Sales Performances

[![View Dashboard ](https://img.shields.io/badge/View%20Dashboard-Click%20Here-black?style=for-the-badge&logo=powerbi&logoColor=yellow&labelColor=yellow)](https://app.powerbi.com/view?r=eyJrIjoiNTc4MTc1ZWQtNTU4NC00NGY3LTg5NTQtNzhlNmQ5MDI3MTQwIiwidCI6IjM3MzhkYjE5LTA4MzUtNDhmZS05MjhiLWMxZjI3ZmNkN2Y2NCJ9)

## 📑 Table of Contents
  - [📌 Problem Statement](#-problem-statement)
  - [⭐ Project Planning using Star Method](#-project-planning-using-star-method)
  - [📂 Data Source](#-data-source)
  - [🔄 Data Preprocessing \& ETL](#-data-preprocessing--etl)
  - [🗄️ Data Modelling](#️-data-modelling)
  - [📊 Data Analysis](#-data-analysis)
  - [📈 Dashboard](#-dashboard)
  - [❓Findings](#findings)
  - [⚙️ Tools, Software and Libraries](#️-tools-software-and-libraries)


## 📌 Problem Statement
<details>
<summary>
View Statement ⬇️
</summary><br>

**The fashion segment on Amazon generates massive amounts of sales and unit-level data across multiple cities and states in India. However, this data is scattered and difficult for stakeholders to interpret quickly.**
- **The problem: How can we track sales performance, seller activity, and product-level insights to improve decision-making in the apparel category?**

</details>


## ⭐ Project Planning using Star Method
<details>

<summary>
View Stratergy ⬇️
</summary>

- Understand key KPIs: Overall Sales, Units, Seller Count
- Build hierarchical view: State → City → Product → Product Details
- Enable drilldowns: from overview → product listings → product-level insights
- Design dashboards with clear filters and interactions

### 📝 S - Situation
Amazon’s apparel sales data was scattered, making it hard for stakeholders to track performance across sellers, cities, and products. A unified, interactive view was needed for better decision-making.

### 🎯 T - Task
- Create Interactive Power BI Dashboard
- Track overall sales, units, and sellers
- Monitor city-wise and state-wise performance
- Provide drillthrough to product and product-level views
- Ensure stakeholders could filter by sales/units dynamically

### ⚡ A - Action

I collected raw Amazon Fashion sales data, cleaned and formatted it, and created a dynamic option to switch between sales and units. Key measures like overall sales, seller count, and reviews were built in Power BI for analysis. Dashboards were designed at three levels — Overview, Products, and Product View — to track performance from high-level trends down to individual products.

### 🏆 R - Result

- The dashboard revealed top-performing states and cities, highlighted product-level gaps such as low-rated SKUs, and gave management a clear tool to track sales and units. 

- By identifying sales trends, high-performing products, and top-selling items, the solution contributed to a **10% increase in revenue**. 

- Additionally, regional sales visualizations **improved market insights by 15%**, enabling more informed strategic planning and faster, data-driven decisions.

</details>


## 📂 Data Source
<details>
<summary>
View Source ⬇️
</summary><br>

- Web Scrapping using Python from [Amazon.in](https://www.amazon.in/) 
- Imported raw Amazon Sale Report.xlsx 
- Imported raw amazon-fashion.csv 

</details>


## 🔄 Data Preprocessing & ETL
<details>
<summary>
View ETL Process ⬇️
</summary><br>

**Our data is initially import from the Excel/CSV file into Power BI, and then the subsequent Extract, Transform, and Load (ETL) is executed in Power Query**
<br>
1. Cleaned nulls, formatted dates, ensured proper data types.  

2. Image URL Cleaning – split image URLs by delimiter, removed duplicates, and dropped the redundant column **large2**.

3. Data Merge – Joined the Amazon table with the amazon-fashion - YT table on the primary key ASIN to enrich the dataset with additional attributes like Amount or price of product in **Amazon** Table.

4. Null and Zero Handling – Applied up/down fill in Power BI to handle missing or zero values in the Price of Product columns.

5. Category Column Transformation – Split the Category field (using lower/upper case delimiter) into Category1, Category2, and Category3. Replaced null values with blanks, then created a new custom column **"Category"** using:

  ```
   [Category.1] & " " & [Category.2] & " " & [Category.3]

  ```

6. Derived Date Column – Created a new Month column using:

  ```
  Month = FORMAT(Amazon[Date], "mmm")

  ```

</details>


## 🗄️ Data Modelling
<details>
<summary>
View Modeling ⬇️
</summary><br>

The Data Model illustrates the correlation between various tables. The following is the Data Model of sales Data:

<img width="700" height="400" alt="Image" src="https://github.com/user-attachments/assets/d9cac1b5-b15b-4e81-be20-e636134bbecd" /> <br>

The data model was designed in Power BI to connect transactional and reference data for better analysis:

- Tables Used:

  - Amazon Sale Report → core sales, units, pricing, and transactional details
  - amazon-fashion → product metadata (reviews, categories, images, etc.)

- Relationship Setup:

  - Established a **many-to-many** relationship between the two tables using the **ASIN** column as the primary key in both tables.

  - This ensured that each product could be mapped across multiple sales records while preserving product-level attributes from the reference table.

- Key Measures Defined:

  - Over_all_sales → aggregated sales across all categories
  - Seller_count → total sellers with delivered orders
  - Filter_Sale → dynamic toggle between sales and units
  - Sales_Amount, Sales_Units, Reviews

- Derived Columns:

  - Month = FORMAT(Amazon[Date], "mmm")
  - Category (concatenated split categories for better grouping)

</details>


## 📊 Data Analysis
<details>
<summary>
View Analysis ⬇️
</summary><br>

DAX Measures Used In DashBoards:
1. Return_Units 
```
= var val= CALCULATE([Sale_Units],CONTAINSSTRING(Amazon[Status],"Return"))
return IF(val=BLANK(),0,val)
```

2. Reviews 
```
= var val = COUNT('amazon-fashion'[no__of_reviews])
return IF(ISBLANK(val),0)
```

3. Sale_Ammount 
```
= var val = SUM(Amazon[Total_Ammount])
return if(ISBLANK(val),0)
```

4. Sale_Units 
```
= var selecting = SELECTEDVALUE(Sale_Option[Type])
var _units =SUM(Amazon[Qty])
var _sale = SUM(Amazon[Total_Ammount])
return IF(selecting="1",_sale,_units)
```

5. Sale_Units 
```
= var selecting = SELECTEDVALUE(Sale_Option[Type])
var _units =SUM(Amazon[Qty])
var _sale = SUM(Amazon[Total_Ammount])
return IF(selecting="1",_sale,_units)
```

6. All_Sale 
```
= CALCULATE([Sale_Units],ALL('amazon-fashion'[Category]))
```

7. Order_Counts 
```
= var val = CALCULATE(COUNT('amazon-fashion'[seller_id]),CONTAINSSTRING(Amazon[Status],"Delivered"))
return IF(val=BLANK(),"0",val)
```

Calculated Column used:

1. Month 
```
1.	month = FORMAT(Amazon[Date],"mmm")
```

Table Implemented: 

1. Sale_Option 
```
= DataTable("Name", STRING,"Type", STRING,{{"1","Sales"},{"2","Units"}})
```   

</details>

## 📈 Dashboard
<details>
<summary>
View Images ⬇️
</summary>

> ### 1. OverView
> <a href="https://app.powerbi.com/view?r=eyJrIjoiNTc4MTc1ZWQtNTU4NC00NGY3LTg5NTQtNzhlNmQ5MDI3MTQwIiwidCI6IjM3MzhkYjE5LTA4MzUtNDhmZS05MjhiLWMxZjI3ZmNkN2Y2NCJ9" target="_blank"> <img width="1286" height="733" alt="Image" src="https://github.com/user-attachments/assets/9bb51257-c8cc-4784-b4e8-9442103f0c96" />
</a>

> ### 2. Products
> <img width="1278" height="732" alt="Image" src="https://github.com/user-attachments/assets/7e19ada0-3cde-49f7-a352-119cacda5eaa" />


> ### 3. Products View
> <img width="1291" height="736" alt="Image" src="https://github.com/user-attachments/assets/90254f0e-44d7-4d1a-9e17-7130f9ff4d1f" />

</details>

## ❓Findings
<details>
<summary> 
View Findings ⬇️
</summary> <br>

- Top City: Bengaluru (8.2M sales, 10.9K units)
- Top State: Maharashtra (16M sales, 20K units)
- Seller Count: 19K active sellers
- Product-level drillthrough revealed stock-outs and low-rated SKUs (e.g., socks with 2.9 rating, 900 sales amount).
- Clear seasonal trend → peaks around mid-May, dip in June.

</details>


## ⚙️ Tools, Software and Libraries
<details>
<summary> 
View tools ⬇️
</summary> <br>

- Power BI → data modeling & dashboard creation
- DAX → calculated measures (sales, units, seller count, reviews and ...)
- Excel/CSV → dataset handling
- Icons/Images → used for product visuals

</details>
