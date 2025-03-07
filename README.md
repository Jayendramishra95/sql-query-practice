write a query to find second highest salary in an employee table.
WITH SalaryRanks AS (
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

fetch all employees from above employees table whose names contain the letter "a"  exactly twice in mssql.
SELECT * 
FROM employees
WHERE LEN(name) - LEN(REPLACE(LOWER(name), 'a', ''))=2
