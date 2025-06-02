# Leetcode SQL
```sql
595. Big Countries
SELECT name, population, area
FROM World
WHERE area >= 3000000
   OR population >= 25000000;


1757. Recyclable and Low Fat Products
# Write your MySQL query statement below
SELECT product_id FROM Products WHERE low_fats = 'Y' AND recyclable = 'Y';

584. Find Customer Referee
# Write your MySQL query statement below
SELECT name from Customer where referee_id != 2 or referee_id is null



183. Customers Who Never Order
SELECT name AS Customers
FROM Customers
LEFT JOIN Orders ON Customers.id = Orders.customerId
WHERE Orders.customerId IS NULL;

175.Combine Two Tables
# Write your MySQL query statement below
select FirstName, LastName, City, State
from Person left join Address
on Person.PersonId = Address.PersonId;

176. Second Highest Salary # Write your MySQL query statement below
SELECT
    (SELECT DISTINCT
            Salary
        FROM
            Employee
        ORDER BY Salary DESC
        LIMIT 1 OFFSET 1) AS SecondHighestSalary;
     
177. Nth Highest Salary
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
DECLARE M INT; 
    SET M = N-1; 
  RETURN (
      SELECT DISTINCT salary
      FROM Employee
      ORDER BY salary DESC
      LIMIT M, 1
  );
END

181.  Employees Earning More Than Their Managers
 # Write your MySQL query statement below
select E.name as Employee from Employee E
join Employee as M
on E.ManagerId = M.Id
where E.Salary > M.Salary;

196. Delete Duplicate Emails
DELETE p1
FROM Person p1
JOIN Person p2
  ON p1.email = p2.email
 AND p1.id > p2.id;

180. Consecutive Numbers
SELECT DISTINCT t1.Num AS ConsecutiveNums
FROM Logs t1
JOIN Logs t2
  ON t1.Id + 1 = t2.Id
 AND t1.Num = t2.Num
JOIN Logs t3
  ON t2.Id + 1 = t3.Id
 AND t2.Num = t3.Num;

184.Department Highest Salary
SELECT d.departmentName,
       IFNULL(MAX(e.salary), NULL) AS HighestSalary
FROM Department d
LEFT JOIN Employee e
  ON d.departmentId = e.departmentId
GROUP BY d.departmentName;

596.Classes More Than 5 Students
SELECT class
FROM Classes
GROUP BY class
HAVING COUNT(studentId) > 5;
