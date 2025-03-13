1- write a query to find second highest salary in an employee table.

soln- WITH SalaryRanks AS (
    SELECT
        salary,
        dense_rank() OVER (ORDER BY salary DESC) AS rank
    FROM
        employees
)
SELECT
    salary AS SecondHighestSalary
FROM
    SalaryRanks
WHERE
    rank = 2

2- fetch all employees from above employees table whose names contain the letter "a"  exactly twice in mssql.

sol- SELECT * 
FROM employees
WHERE LEN(name) - LEN(REPLACE(LOWER(name), 'a', ''))=2

3- retrieve only duplicate records from employees table

sol- select name,department,salary, count(*) as duplicate 
from employees
group by name,department,salary
having count(*)>1

4- write a query to calculate the running total of sales by date.

select * from sales
SELECT 
    sale_date,
    amount,
    SUM(amount) OVER (ORDER BY sale_date) AS running_total
FROM sales
ORDER BY sale_date

5- Find employees who earns more than the average salary in their department

SELECT e1.*
FROM employees e1
inner JOIN (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
) e2
ON e1.department = e2.department
WHERE e1.salary > e2.avg_salary

6- write a query to find the most frequently occuring value in a column

SELECT name, COUNT(*) AS occurrence_count
FROM employees
GROUP BY name
having COUNT(*)>=3

8- write a query to count how many employees share the same salary.

SELECT salary, COUNT(*) as  employee_count 
FROM employees
GROUP BY salary

Challenging level

1- Retrieve customers who made their first purchase in the last 6 months.

WITH FirstPurchase AS (
    SELECT 
        customer_id,
        MIN(purchase_date) AS first_purchase_date
    FROM customers_q1
    GROUP BY customer_id
)
SELECT *
FROM FirstPurchase
WHERE first_purchase_date >= DATEADD(MONTH, -6, GETDATE());

2- How do you pivot a table to convert rows into columns

SELECT *
FROM (
    SELECT product_id, year, sales_amount
    FROM sales_q2
) AS SourceTable
PIVOT (
    SUM(sales_amount) 
    FOR year IN ([2022], [2023])
) AS PivotTable;

3- write a query to calculate the  percentage change in sales month-over-month.

SELECT 
    sales_date,
    sales_amount,
    LAG(sales_amount) OVER (ORDER BY sales_date) AS previous_month_sales,
    CASE WHEN LAG(sales_amount) OVER (ORDER BY sales_date) IS NULL THEN NULL 
    ELSE 
            ((sales_amount - LAG(sales_amount) OVER (ORDER BY sales_date)) 
            / LAG(sales_amount) OVER (ORDER BY sales_date)) * 100
   END AS percentage_change
FROM sales_q3

4- Find the median salary of employees in a table

SELECT 
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) AS median_salary
FROM employees_q4;

5-  Fetch all users who logged in consecutively for 3 days or more

WITH ConsecutiveLogins AS (
    SELECT 
        user_id, 
        login_date,
        LAG(login_date, 1) OVER (PARTITION BY user_id ORDER BY login_date) AS prev_day,
        LAG(login_date, 2) OVER (PARTITION BY user_id ORDER BY login_date) AS prev_2day
    FROM logins_q5
)
SELECT DISTINCT user_id
FROM ConsecutiveLogins
WHERE 
    DATEDIFF(DAY, prev_day, login_date) = 1 
    AND DATEDIFF(DAY, prev_2day, prev_day) = 1;

6- Write a query to delete duplicate rows while keeping one occurrence.

WITH CTE AS (
    SELECT 
        id, 
        name, 
        ROW_NUMBER() OVER (PARTITION BY name ORDER BY id) AS row_num
    FROM duplicates_q6
)
DELETE FROM duplicates_q6 
WHERE id IN (SELECT id FROM CTE WHERE row_num > 1);

7- Create a query to calculate the ratio of sales between two categories.

WITH CategoryTotals AS (
    SELECT 
        category, 
        SUM(sales_amount) AS total_sales
    FROM category_sales_q7
    GROUP BY category
)
SELECT 
    (SELECT total_sales FROM CategoryTotals WHERE category = 'Electronics') * 1.0 / 
    (SELECT total_sales FROM CategoryTotals WHERE category = 'Clothing') AS sales_ratio
FROM CategoryTotals
WHERE category = 'Electronics'

8- How would you implement a recursive query to generate a hierarchical structure?

WITH EmployeeHierarchy AS (
    
    SELECT 
        employee_id, 
        employee_name, 
        manager_id, 
        1 AS hierarchy_level
    FROM employees_hierarchy_q8
    WHERE manager_id IS NULL
    
    UNION ALL
    
    
    SELECT 
        e.employee_id, 
        e.employee_name, 
        e.manager_id, 
        eh.hierarchy_level + 1 AS hierarchy_level
    FROM employees_hierarchy_q8 e
    INNER JOIN EmployeeHierarchy eh 
        ON e.manager_id = eh.employee_id
)
SELECT * FROM EmployeeHierarchy
ORDER BY hierarchy_level, manager_id, employee_id

9- Write a query to find gaps in sequential numbering within a table.

Select id+1 as gap from (select id, lead(id) over(order by id asc) as next_num
from sequence_q9) A
where next_num - id > 1

10- Split a comma-separated string into individual rows using SQL.

SELECT id, TRIM(value) AS item
FROM csv_data_q10
CROSS APPLY STRING_SPLIT(csv_string, ',')



