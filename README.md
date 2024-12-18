# SQL-Case-study-Fiction-Ecommerce-data

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

**2. **

