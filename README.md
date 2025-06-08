# SQL-Data-Analysis
# Inventory Management Dataset
## Project Background and Overview
An effective inventory management is critical to operational efficiency, cost control, and customer satisfaction in any product-based business. This exploratory analysis presents a comprehensive insight into an inventory management system of our hypothetical company. 

The inventory dataset contains data on different warehouse locations, cost and pricing, suppliers and their restocking performance and inventory levels for the year.

Insights and recommendations are provided on the following key areas:

- Stock analysis: By evaluating the products categories, we find which one still has enough inventory left before they need to be restocked.
- Supplier Performance: An analysis of the various suppliers based on their turnover rate and average lead time for restocking products.
- Cost and Pricing Analysis: Assessment of the top five most expensive products based on unit price and turnover based on the stock quantity and lead time.

### SQL Questions and Codes Used
Which product category has the highest total stock quantity across all products?
```
SELECT Category, SUM(Stock_Quantity) AS Total_stock
FROM ['Inventory_Mag$']
GROUP BY Category
ORDER BY Total_stock DESC
```

How many products currently have stock quantities below their reorder point?
```
WITH Stock_Reorder AS (
SELECT 
	CASE 
		WHEN Stock_Quantity < Reorder_Point THEN 'Yes'
		ELSE 'No'
	END AS R,
	COUNT(CASE 
		WHEN Stock_Quantity < Reorder_Point THEN 'Yes'
		ELSE 'No'
	END) AS Count_Of_Reorder
FROM ['Inventory_Mag$']
GROUP BY
	CASE 
		WHEN Stock_Quantity < Reorder_Point THEN 'Yes'
		ELSE 'No'
	END)
SELECT Count_Of_Reorder
FROM Stock_Reorder
WHERE R = 'Yes'
```

What minimum order quantity is required for a company to restock all products below the reorder point?
```
WITH CTE_Min AS (
	SELECT Reorder_Point,
			CASE WHEN Stock_Quantity < Reorder_Point THEN 'Yes'
				 ELSE 'No'
			END AS Reorder_prod
	FROM ['Inventory_Mag$']
	WHERE CASE WHEN Stock_Quantity < Reorder_Point THEN 'Yes'
				 ELSE 'No'
			END = 'Yes')			
SELECT MIN(Reorder_Point) As Lowest_reorder
FROM CTE_Min
```

Which supplier has the highest average lead time for restocking?
```
SELECT Supplier_ID, AVG(Lead_Time_Days) AS Avg_Lead_Time
FROM ['Inventory_Mag$']
GROUP BY Supplier_ID
ORDER BY Avg_Lead_Time DESC
```

What is the average time since the last restock for products marked as "Out of Stock"?
```
WITH CTE_Time AS (
	SELECT DATEDIFF(DAY, Last_Restock_Date, GETDATE()) AS Time_Days
	FROM ['Inventory_Mag$']
	WHERE Status = 'Out of Stock')
SELECT AVG(Time_Days) Avg_Days_Since_Last_Restock
FROM CTE_Time
```

Identify the months when the highest number of products were restocked—are there any seasonal trends?
```
SELECT 
	DATENAME(Month, Last_Restock_Date) AS Month_Restock, 
	COUNT(DATEPART(Month, Last_Restock_Date)) AS Restock_Month_Count
FROM ['Inventory_Mag$']
GROUP BY DATENAME(Month, Last_Restock_Date)
ORDER BY Restock_Month_Count DESC
```

Identify the top 5 most expensive products (by unit price) and their categories
```
SELECT TOP 5 Product_Name, Category, Unit_Price
FROM ['Inventory_Mag$']
ORDER BY Unit_Price DESC
```

Which product category has the fastest turnover based on stock quantity and lead time?
```
SELECT Product_Name, ROUND(Stock_Quantity/Lead_Time_Days, 2) AS Turnover
FROM ['Inventory_Mag$']
ORDER BY Turnover DESC
```

Which warehouse location has the highest number of products stored?
```
SELECT Warehouse_Location, COUNT(Product_Name) AS Total_Stored_Products
FROM ['Inventory_Mag$']
GROUP BY Warehouse_Location
ORDER BY Total_Stored_Products DESC
```


## Data Structure
 The inventory management consists of just one table which was then modelled by me to provide better insights as shown below: 

![Data Model (inventory)](https://github.com/user-attachments/assets/457f1422-8686-438b-8555-27103f461d07)

## Executive Summary
### Overview of Findings
This analysis supports more informed decision-making around inventory control, supplier coordination, and stock prioritization. Applying the insights from this report can lead to improved inventory performance, reduced costs, and stronger supply chain resilience.

### Stock Analysis
This inventory analysis provided key insights into the company's operational strengths and areas for optimization. The evaluation of stock levels revealed a concentration of inventory in high-stock categories, suggesting potential overstocking and increased holding costs. Simultaneously, understocked items within the low stock level category highlight risks of stockouts and lost sales opportunities.

### Supplier Performance
The turnover analysis showed clear differences in efficiency across product categories, with Clothing exhibiting the highest turnover and Office Supplies the lowest — pointing to the need for category-specific replenishment strategies. The ABC classification further emphasized that a relatively small portion of products (Group A) accounts for a significant share of inventory value, warranting focused monitoring and resource allocation.

### Cost and Pricing Analysis
The cost and pricing analysis highlighted that high-value products are being stocked at healthy levels, which may reflect strategic positioning but also necessitates ongoing assessment of demand and turnover to avoid capital inefficiency.

