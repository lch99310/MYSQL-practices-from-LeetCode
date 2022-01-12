# MYSQL-practices-from-leetcode
175. Combine Two Tables
~~~~sql
SELECT p.firstName, p.lastName, a.city, a.state
FROM Person p
LEFT JOIN Address a
ON p.personId = a.personId
~~~~

176. Second Highest Salary
~~~~sql
SELECT MAX(e.salary) AS "SecondHighestSalary"
FROM Employee e
WHERE e.salary < (SELECT MAX(salary) FROM Employee)
~~~~

177. Nth Highest Salary
~~~~sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
DECLARE M int;
SET M = N -1;
  RETURN (
      # Write your MySQL query statement below.
      SELECT DISTINCT e.salary AS "getNthHighestSalary(N)"
      FROM Employee e 
      ORDER BY e.salary DESC
      LIMIT 1 OFFSET M
  );
END
~~~~

178. Rank Scores
~~~~sql
SELECT s.score, DENSE_RANK() OVER(ORDER BY s.score DESC) AS "rank"
FROM Scores s
~~~~
* Note: 
* Rank generates ranking number based on total number. e.g.: 1,2,2,4,5.... 
* Dense_Rank generate consecutive ranking number. e.g.: 1,2,2,3,4.....

181. Employees Earning More Than Their Managers
~~~~sql
SELECT e.name AS "Employee"
FROM Employee e, Employee m
WHERE e.managerId = m.id AND e.salary > m.salary
~~~~

182. Duplicate Emails
~~~~sql
SELECT  p.email
FROM Person p
GROUP BY p.email
HAVING count(p.email) > 1
~~~~

183. Customers Who Never Order
~~~~sql
SELECT c.name AS "Customers"
FROM Customers c
LEFT JOIN Orders o
ON c.id = o.customerId
WHERE o.id IS NULL
~~~~
or
~~~~sql
SELECT c.name AS "Customers"
FROM Customers c
WHERE c.id NOT IN (
    SELECT o.customerId 
    FROM Orders o)
~~~~

196. Delete Duplicate Emails
~~~~sql
DELETE p1.*
FROM Person p1, Person p2
WHERE p1.email = p2.email AND p1.id > p2.id;
~~~~

197. Rising Temperature
~~~~sql
SELECT w1.id
FROM Weather w1, Weather w2
WHERE DATEDIFF(w1.recordDate, w2.recordDate) = 1 AND w1.temperature > w2.temperature
~~~~

511. Game Play Analysis I
~~~~sql
SELECT a.player_id, MIN(a.event_date) AS "first_login"
FROM Activity a
GROUP BY a.player_id
~~~~

595. Big Countries
~~~~sql
SELECT name, population, area
FROM World
WHERE area >= 3000000 OR population >= 25000000
~~~~

596. Classes More Than 5 Students
~~~~sql
SELECT c.class
FROM Courses c
GROUP BY c.class
HAVING COUNT(c.student) >= 5
~~~~

620. Not Boring Movies
~~~~sql
SELECT c.*
FROM Cinema c
WHERE (c.id % 2) = 1 AND c.description != "boring"
ORDER BY rating desc
~~~~

627. Swap Salary  * (need to review)
~~~~sql
UPDATE Salary s
SET s.sex = (CASE WHEN s.sex = "m" THEN "f" ELSE "m" END)
~~~~

1179. Reformat Department Table  * (need to review)
~~~~sql
SELECT d.id,
SUM(CASE WHEN d.month = "Jan" THEN d.revenue ELSE NULL END) AS "Jan_Revenue",
SUM(CASE WHEN d.month = "Feb" THEN d.revenue ELSE NULL END) AS "Feb_Revenue",
SUM(CASE WHEN d.month = "Mar" THEN d.revenue ELSE NULL END) AS "Mar_Revenue",
SUM(CASE WHEN d.month = "Apr" THEN d.revenue ELSE NULL END) AS "Apr_Revenue",
SUM(CASE WHEN d.month = "May" THEN d.revenue ELSE NULL END) AS "May_Revenue",
SUM(CASE WHEN d.month = "Jun" THEN d.revenue ELSE NULL END) AS "Jun_Revenue",
SUM(CASE WHEN d.month = "Jul" THEN d.revenue ELSE NULL END) AS "Jul_Revenue",
SUM(CASE WHEN d.month = "Aug" THEN d.revenue ELSE NULL END) AS "Aug_Revenue",
SUM(CASE WHEN d.month = "Sep" THEN d.revenue ELSE NULL END) AS "Sep_Revenue",
SUM(CASE WHEN d.month = "Oct" THEN d.revenue ELSE NULL END) AS "Oct_Revenue",
SUM(CASE WHEN d.month = "Nov" THEN d.revenue ELSE NULL END) AS "Nov_Revenue",
SUM(CASE WHEN d.month = "Dec" THEN d.revenue ELSE NULL END) AS "Dec_Revenue"
FROM Department d
GROUP BY d.id
ORDER by d.id
~~~~
