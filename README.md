# Analyze Data in a Model Car Database with MySQL Workbench Project
## Date March 20, 2025
## Time 10 hours
<br/>

## Project scenario
Mint Classics Company, a retailer of classic model cars and other vehicles, is looking at <strong>closing one of its storage facilities.</strong>

To support a data-based business decision, they are looking for suggestions and recommendations for reorganizing or reducing inventory, while still maintaining timely service to their customers. For example, they would like to be able to ship a product to a customer within 24 hours of the order being placed.

1) Where are items stored and if they were rearranged, could a warehouse be eliminated?
2) How are inventory numbers related to sales figures? Do the inventory counts seem appropriate for each item?
3) Are we storing items that are not moving? Are any items candidates for being dropped from the product line?

The answers to questions like those should help you formulate suggestions and recommendations for reducing inventory to close one of the storage facilities. 

## Project Objectives

1. Explore products currently in inventory.
2. Determine important factors that may influence inventory reorganization/reduction.
3. Provide analytic insights and data-driven recommendations.

## Skill demonstrated
1. MySQL

## Tools used
1. MySQL workbench
2. PowerPoint
3. Excel
   
## Database ER Diagram (craw's foot)
![ER diagran](https://github.com/nanpiyaporn/ModelCarMySQL/blob/main/Database%20ER%20diagram%20(crow's%20foot).jpeg)

## Approach
Step 0: 
- We need to clean the table that does not have "N/A" in the columns
- Make sure that the columns are not duplicates ( It shows some duplicates information of 'orderNumber' at 'orderdetails' and 'orders' table) 
       
Step 1: we need to check which warehouse uses a few products:

```mysql
SELECT warehouseCode, COUNT(productCode) AS total_products, SUM(quantityInStock) AS total_stock
FROM products
GROUP BY warehouseCode
ORDER BY total_stock DESC;
```
The outcome shows that warehouse d has fewer products and the number of products used

| warehouseCode  | total_products  | total_stock |
| ------------- | ------------- | ------------- |
| b  | 38  | 219183 |
| a | 25  | 131688 |
| c  | 24 | 124880  |
| d  | 23  | 79380  |

Step 2: We need to check in each warehouse how the profit is

```mysql
SELECT 
    p.warehouseCode, 
    COUNT(p.productCode) AS total_products, 
    SUM(p.quantityInStock) AS total_stock, 
    SUM(od.quantityOrdered * od.priceEach) AS total_revenue,
    SUM(od.quantityOrdered * p.buyPrice) AS total_cost,
    SUM((od.quantityOrdered * od.priceEach) - (od.quantityOrdered * p.buyPrice)) AS total_profit
FROM products p
LEFT JOIN orderdetails od ON p.productCode = od.productCode
GROUP BY p.warehouseCode
ORDER BY total_stock DESC;
```
The answer follows the first question that warehouse D makes less profits compare with other warehouse

| warehouseCode  | total_products  | total_stock | total_revernue | total_cost | total_profit |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| b  | 38  | 219183 | 3853922.49|2327710.29 |1526212.20 |
| a | 25  | 131688 |2076063.66 |1240847.65 | 835216.01|
| c  | 24 | 124880  | 1797559.63| 1060291.30|737268.33 | 
| d  | 23  | 79380  | 1876644.83|1149461.12 | 727183.71|

Step 3: we would like to track down how much profit in each product.
Surprisingly! Warehouse C makes less profit than Warehouse D which means we better close Warehouse C  instead of Warehouse D because we use less work but get more profit.

```mysql
SELECT 
    p.warehouseCode, 
    COUNT(p.productCode) AS total_products, 
    SUM(p.quantityInStock) AS total_stock, 
    SUM(od.quantityOrdered * od.priceEach) AS total_revenue,
    SUM(od.quantityOrdered * p.buyPrice) AS total_cost,
    SUM((od.quantityOrdered * od.priceEach) - (od.quantityOrdered * p.buyPrice)) AS total_profit,
    (SUM((od.quantityOrdered * od.priceEach) - (od.quantityOrdered * p.buyPrice)) / COUNT(p.productCode)) AS profit_per_product
FROM products p
LEFT JOIN orderdetails od ON p.productCode = od.productCode
GROUP BY p.warehouseCode
ORDER BY total_stock DESC;
```
The result shows

| warehouseCode  | total_products  | total_stock | total_revernue | total_cost | total_profit |profilt_per_product |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| b  | 38  | 219183 | 3853922.49|2327710.29 |1526212.20 | 1509.606528 |
| a | 25  | 131688 |2076063.66 |1240847.65 | 835216.01| 1201.749655 |
| c  | 24 | 124880  | 1797559.63| 1060291.30|737268.33 | 1122.174018 |
| d  | 23  | 79380  | 1876644.83|1149461.12 | 727183.71| 1146.977461 |

Next: we need to analyze the inventory and sales relationship
1. Find slow-moving products (Products with high stock but low sales might be overstocked)
```mysql
SELECT p.productCode, p.productName, p.quantityInStock, 
       IFNULL(SUM(od.quantityOrdered), 0) AS total_sold
FROM products p
LEFT JOIN orderdetails od ON p.productCode = od.productCode
GROUP BY p.productCode
ORDER BY total_sold ASC;
```
The result shows the 'productCode' is 'S18_3233', 'productName' is '1985 Toyota Supra is the slowest product because it cannot sell at all, and still has in stock 7733 cars.


![slow sale](https://github.com/nanpiyaporn/ModelCarMySQL/blob/main/slowsale.jpg)

Next, check the monthly sales products
```mysql
SELECT MONTH(o.orderDate) AS month, 
       SUM(od.quantityOrdered) AS total_sold
FROM orders o
JOIN orderdetails od ON o.orderNumber = od.orderNumber
GROUP BY month
ORDER BY total_sold DESC;
```
The result shows that November is the most selling product months

![monthly sell](https://github.com/nanpiyaporn/ModelCarMySQL/blob/main/Number%20of%20sales%20vs.%20Month.png)

## Solution

1. **Closure Recommendation**: Warehouse **C** should be closed as it generates the lowest profit per product while occupying a significant amount of storage space, leading to inefficiencies in inventory management.  
2. **Stock Replenishment Strategy**: **November** is the peak sales month, so inventory should be restocked in advance to meet increased demand.  
3. **Discontinuation Analysis**: The **1985 Toyota Supra** should be considered for removal due to a lack of demand. However, further analysis is recommended to determine the cause—whether it’s due to pricing, marketing, or product relevance—to optimize business operations.  


