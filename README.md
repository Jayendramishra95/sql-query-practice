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
