# SQL Case study on Fiction Ecommerce data

## Contents 📖
- [Introduction](#introduction)
- [Problem Statement](#problem-statement)
- [Database Schema](#database-schema)
- [Case Study: Questions and answers](#case-study-questions-and-answers)
- [Feature Highlights](#feature-highlights)
- [Key Insights](#key-insights)
- [Use cases](#use-cases)

## Introduction

This project features a fictional e-commerce logistics case study, demonstrating SQL queries to analyze and optimize warehouse operations, shipping efficiency, and supplier performance. The dataset represents a simplified logistics workflow with tables for warehouses, shipments, products, suppliers, and shipment details.

**🛠️ Tool Used :** MySQL

## Problem Statement
  
A fictional e-commerce company, SwiftLogix, specializes in logistics and order fulfillment. The company needs insights into warehouse operations, shipping efficiency, and supplier reliability to streamline processes and optimize costs.

The case study revolves around five key datasets:
  - Warehouses
  - Shipments
  - Products
  - Shipment Details
  - Suppliers

## Database Schema

**Table : Warehouses**

| **Column Name** | **Data Type** | **Description**                          |
|-----------------|---------------|------------------------------------------|
| WarehouseID     | INT           | Unique identifier for each warehouse     |
| WarehouseName   | VARCHAR       | Name of the warehouse                    |
| Location        | VARCHAR       | City where the warehouse is located      |

**Table : Shipments**

| **Column Name**     | **Data Type** | **Description**                           |
|---------------------|---------------|-------------------------------------------|
| ShipmentID          | INT           | Unique identifier for each shipment       |
| WarehouseID         | INT           | ID of the warehouse handling the shipment |
| ShipmentDate        | DATE          | Date of the Shipment                      |
| DeliveryDate        | DATE          | Date of Delivery                          |
| TotalWeight         | DECIMAL       | Total weight of the Shipment              |
| Total Cost          | DECIMAL       | Cost of the Shipment                      |

**Table : Products**

| **Column Name**     | **Data Type** | **Description**                           |
|---------------------|---------------|-------------------------------------------|
| ProductID           | INT           | Unique identifier for each product        |
| ProductName         | VARCHAR       | Name of the product                       |
| Category            | DATE          | Product Category                          |
| Weight              | DATE          | Weight of the product(Kg)                 |

**Table : Shipment Details**

| **Column Name**     | **Data Type** | **Description**                           |
|---------------------|---------------|-------------------------------------------|
| ShipmentDetailID    | INT           | Unique identifier for each shipment detail|
| ShipmentID          | INT           | ID of the associated shipment             |
| ProductID           | INT           | ID of the product shipped                 |
| Quantity            | INT           | Number of the unit shipped                |

**Table : Suppliers**

| **Column Name**     | **Data Type** | **Description**                           |
|---------------------|---------------|-------------------------------------------|
| SupplierID          | INT           | Unique identifier for each supplier       |
| SupplierName        | VARCHAR       | Name of the Suppliers                     |
| Rating              | INT           | Supplier rating(1 to 5)                   |

## Case Study: Questions and answers

**1. Total Shipments and Costs by Warehouse**
- Objective: Calculate the total shipments, weight, and cost managed by each warehouse
```sql
SELECT W.WarehouseID, W.WarehouseName, COUNT(S.ShipmentID) AS TotalShipments, SUM(S.TotalWeight) AS TotalWeight, SUM(S.TotalCost) AS TotalCost
FROM Warehouses W
LEFT JOIN Shipments S ON W.WarehouseID = S.WarehouseID
GROUP BY 1,2
ORDER BY TotalCost DESC;
```
- **Tables**: `Warehouses` and `Shipments` are joined using `WarehouseID`.
- **Aggregation**:
  - `COUNT(S.ShipmentID)` calculates the total number of shipments for each warehouse.
  - `SUM(S.TotalWeight)` sums up the total weight of all shipments.
  - `SUM(S.TotalCost)` calculates the total cost of shipments.
- **Grouping**: The results are grouped by `WarehouseID` and `WarehouseName`.
- **Ordering**: The data is sorted in descending order of `TotalCost`.

**2. Average Delivery Time per Warehouse**
- Objective: Calculate the average delivery time (in days) for each warehouse using a window function.
```sql
SELECT W.WarehouseID, W.WarehouseName,
    AVG(DATEDIFF(S.DeliveryDate, S.ShipmentDate)) AS AvgDeliveryTime
FROM Warehouses W
JOIN Shipments S ON W.WarehouseID = S.WarehouseID
GROUP BY 1,2
ORDER BY AvgDeliveryTime ASC;
```
- **Tables**: `Warehouses` and `Shipments` are joined using `WarehouseID`.
- **Calculation through window function**:
  - `DATEDIFF(S.DeliveryDate, S.ShipmentDate)` computes the number of days between shipment and delivery.
  - `AVG()` calculates the average delivery time for each warehouse.
- **Grouping**: Results are grouped by `WarehouseID` and `WarehouseName`.
- **Ordering**: The data is sorted in ascending order of `AvgDeliveryTime` (quickest delivery times first).

**3. Best-Selling Products**
- Objective: Identify the top 5 best-selling products based on quantity.
```sql
SELECT P.ProductID, P.ProductName, SUM(SD.Quantity) AS TotalQuantity
FROM Products P
JOIN ShipmentDetails SD ON P.ProductID = SD.ProductID
GROUP BY 1,2
ORDER BY TotalQuantity DESC
LIMIT 5;
```
- **Tables**: `Products` and `ShipmentDetails` are joined using `ProductID`.
- **Aggregation**: 
  - `SUM(SD.Quantity)` calculates the total quantity of each product shipped.
- **Grouping**: Results are grouped by `ProductID` and `ProductName`.
- **Ordering**: The data is sorted in descending order of `TotalQuantity` (most shipped products first).
- **Limiting**: The `LIMIT 5` clause restricts the output to the top 5 products.

**4. Monthly Shipping Costs**
- Objective: Analyze the monthly shipping costs using a common table expression (CTE).
```sql
WITH MonthlyCosts AS (
    SELECT DATE_FORMAT(S.ShipmentDate, '%Y-%m') AS Month, SUM(S.TotalCost) AS MonthlyCost
    FROM Shipments S
    GROUP BY Month)
SELECT Month, MonthlyCost
FROM MonthlyCosts
ORDER BY Month;
```
- **Common Table Expression (CTE)**: 
  - `MonthlyCosts` computes the total cost of shipments grouped by month.
  - `DATE_FORMAT(S.ShipmentDate, '%Y-%m')` extracts the year and month from the shipment date.
- **Aggregation**: `SUM(S.TotalCost)` calculates the total shipment cost for each month.
- **Ordering**: Results are sorted chronologically by month.

**5. Supplier Performance**
- Objective: Rank suppliers by total shipments of their products using a window function.
```sql
SELECT S.SupplierID, S.SupplierName, COUNT(DISTINCT SD.ShipmentID) AS TotalShipments,
    RANK() OVER (ORDER BY COUNT(DISTINCT SD.ShipmentID) DESC) AS SupplierRank
FROM Suppliers S
JOIN Products P ON S.SupplierID = P.SupplierID
JOIN ShipmentDetails SD ON P.ProductID = SD.ProductID
GROUP BY S.SupplierID, S.SupplierName;
```
- **Joins**:
  - `Suppliers` is joined with `Products` using `SupplierID`.
  - `Products` is joined with `ShipmentDetails` using `ProductID`.
- **Aggregation**:
  - `COUNT(DISTINCT SD.ShipmentID)` calculates the total number of unique shipments for each supplier.
- **Ranking**:
  - `RANK() OVER (ORDER BY COUNT(DISTINCT SD.ShipmentID) DESC)` assigns a rank to suppliers based on the number of shipments in descending order.
- **Grouping**: Results are grouped by `SupplierID` and `SupplierName`.

**6. Product Weight Contribution**
- Objective: Calculate the weight contribution of each product in shipments using a window function.
```sql
SELECT P.ProductName,
    SUM(SD.Quantity * P.Weight) AS TotalWeight,
    SUM(SD.Quantity * P.Weight) * 100.0 / SUM(SUM(SD.Quantity * P.Weight)) 
        OVER () AS WeightPercentage
FROM Products P
JOIN ShipmentDetails SD ON P.ProductID = SD.ProductID
GROUP BY P.ProductName
ORDER BY TotalWeight DESC;
```
- **Joins**: `Products` is joined with `ShipmentDetails` using `ProductID`.
- **Calculations**:
  - `SUM(SD.Quantity * P.Weight)` computes the **total weight** of each product shipped.
  - `SUM(SUM(SD.Quantity * P.Weight)) OVER ()` calculates the total weight of all products (window function).
  - `WeightPercentage` computes each product's weight as a percentage of the total.
- **Grouping**: Results are grouped by `ProductName`.
- **Ordering**: Results are sorted in descending order of `TotalWeight` (heaviest products first).

**7. High-Cost Shipments**
- Objective: Identify shipments with costs above the average using a CTE and window functions.
```sql
WITH AverageCost AS (SELECT AVG(TotalCost) AS AvgCost
    FROM Shipments)
SELECT S.ShipmentID, S.ShipmentDate, S.TotalCost, AC.AvgCost,
    CASE WHEN S.TotalCost > AC.AvgCost THEN 'Above Average'ELSE 'Below Average' END AS CostCategory
FROM Shipments S
CROSS JOIN AverageCost AC
ORDER BY S.TotalCost DESC;
```
- **Common Table Expression (CTE)**: 
  - `AverageCost` calculates the average shipment cost using `AVG(TotalCost)`.
- **CROSS JOIN**:
  - Combines each shipment with the average cost for comparison.
- **CASE Statement**:
  - Compares `S.TotalCost` to `AC.AvgCost`:
    - **Above Average**: If the shipment's total cost exceeds the average.
    - **Below Average**: Otherwise.
- **Ordering**: Results are sorted in descending order of `TotalCost` (highest costs first).

## Feature Highlights
1. Basic SQL: Aggregations, filtering, and joins.
2. Window Functions: RANK, AVG, cumulative totals, and percentage calculations.
3. CTEs: Breaking down complex queries for readability and reuse.

## Key Insights
**1. Warehouse Performance Analysis**
- Metrics Analyzed: Total shipments, total weight handled, and total costs managed by each warehouse.
- Insights:
  - Identified warehouses contributing the most to overall shipment costs and volumes.
  - Highlighted underperforming warehouses for optimization opportunities.
    
**2. Delivery Efficiency**
- Metrics Analyzed: Average delivery time for each warehouse.
- Insights:
  - Warehouses with faster delivery times were pinpointed, helping to evaluate location efficiency and potential delays.
  - Provided data to benchmark warehouse performance.
    
**3. Best-Selling Products**
- Metrics Analyzed: Total quantity of each product shipped.
- Insights:
  - Identified top 5 products with the highest demand, enabling better inventory planning.
  - Helped focus procurement efforts on high-demand products.
    
**4. Monthly Shipping Trends**
- Metrics Analyzed: Monthly shipping costs using Common Table Expressions (CTEs).
- Insights:
  - Provided a clear view of monthly cost trends, helping to forecast budgets and spot seasonal fluctuations.
  - Allowed decision-makers to identify months with unusually high costs for deeper analysis.
    
**5. Supplier Performance**
- Metrics Analyzed: Total shipments handled by each supplier and their ranking using window functions.
- Insights:
  - Ranked suppliers by their contribution to the company’s operations, highlighting top-performing and underperforming suppliers.
  - Supported negotiations and supplier relationship management.
    
**6. Product Weight Contribution**
- Metrics Analyzed: Total weight contribution of each product category in shipments.
- Insights:
  - Showed which products or categories contributed the most to shipment weight, helping optimize shipping costs.
  - Informed decisions on packaging and product design for weight reduction.
    
**7. High-Cost Shipments**
- Metrics Analyzed: Shipments with costs above the average using CTEs and conditional logic.
- Insights:
  - Flagged high-cost shipments for further investigation.
  - Categorized shipments into "Above Average" and "Below Average" cost buckets, supporting cost control efforts.

**8. Cumulative Trends and Rankings**
- Metrics Analyzed: Cumulative shipping costs over time and supplier rankings using window functions.
- Insights:
   - Provided visibility into the company’s progressive spending trends.
   - Allowed tracking of suppliers’ contributions relative to others, supporting strategic procurement decisions.
     
## Use Cases
- Operational Efficiency: Identify bottlenecks in delivery times and warehouse operations.
- Cost Optimization: Spot high-cost trends and inefficient shipments.
- Inventory Planning: Focus on best-selling products and high-weight categories.
- Supplier Management: Evaluate and rank suppliers for performance improvement.
- Forecasting: Understand seasonal and monthly trends for budgeting and planning.

