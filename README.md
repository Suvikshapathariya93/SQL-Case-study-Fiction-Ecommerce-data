# SQL-Case-study-Fiction-Ecommerce-data

## Contents 📖
- [Introduction](#introduction)
- [Problem Statement](#problem-statement)
- [Database Schema](#database-schema)
- [Case/task Questions and answers](#case/task-questions-and-answers)
- [Key Insights](#key-insights)

## Introduction

This project features a fictional e-commerce logistics case study, demonstrating SQL queries to analyze and optimize warehouse operations, shipping efficiency, and supplier performance. The dataset represents a simplified logistics workflow with tables for warehouses, shipments, products, suppliers, and shipment details.

## Problem Statement
  
A fictional e-commerce company, SwiftLogix, specializes in logistics and order fulfillment. The company needs insights into warehouse operations, shipping efficiency, and supplier reliability to streamline processes and optimize costs.

The case study revolves around three key datasets:
  - Warehouses
  - Shipments
  - Products

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
| ShipmentID          | INT           | Unique identifier fro each shipment       |
| WarehouseID         | INT           | ID of the warehouse handling the shipment |
| ShipmentDate        | DATE          | Date of the Shipment                      |
| DeliveryDate        | DATE          | Date of Delivery                          |
| TotalWeight         | DECIMAL       | Total weight of the Shipment              |
| Total Cost          | DECIMAL       | Cost of the Shipment                      |

