# SQL Case study on Fiction Ecommerce data

## Contents 📖
- [Introduction](#introduction)
- [Problem Statement](#problem-statement)
- [Database Schema](#database-schema)
- [Case Study: Questions and answers](#case-study-questions-and-answers)
- [Key Insights](#key-insights)

## Introduction

This project features a fictional e-commerce logistics case study, demonstrating SQL queries to analyze and optimize warehouse operations, shipping efficiency, and supplier performance. The dataset represents a simplified logistics workflow with tables for warehouses, shipments, products, suppliers, and shipment details.

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
| SupplierName        | VARCHAR       | ID of the associated shipment             |
| Rating              | INT           | ID of the product shipped                 |

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

