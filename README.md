# Analyze Data in a Model Car Database with MySQL Workbench Project
## Date March 20, 2025
## Time 4 hours
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
1. R
2. MySQL

## Tools used
1. MySQL workbench
2. PowerPoint
3. Excel
4. 
## Database ER Diagram (craw's foot)
![ER diagran](https://github.com/nanpiyaporn/ModelCarMySQL/blob/main/Database%20ER%20diagram%20(crow's%20foot).jpeg)

## Approach
```sh
SELECT warehouseCode, COUNT(productCode) AS total_products, SUM(quantityInStock) AS total_stock
FROM products
GROUP BY warehouseCode
ORDER BY total_stock DESC;
```
## Solution

## Summary
