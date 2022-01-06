# MYSQL-practice-from-github
175. Combine Two Tables
~~~~sql
SELECT p.firstName, p.lastName, a.city, a.state
FROM Person p
LEFT JOIN Address a
ON p.personId = a.personId
~~~~

181. Employees Earning More Than Their Managers
~~~~sql
SELECT e.name AS "Employee"
FROM Employee e, Employee m
WHERE e.managerId = m.id AND e.salary > m.salary
~~~~

183. Duplicate Emails
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

197. Rising Temperature
~~~~sql
SELECT w1.id
FROM Weather w1, Weather w2
WHERE DATEDIFF(w1.recordDate, w2.recordDate) = 1 AND w1.temperature > w2.temperature
~~~~

620. Not Boring Movies
~~~~sql
SELECT c.*
FROM Cinema c
WHERE (c.id % 2) = 1 AND c.description != "boring"
ORDER BY rating desc
~~~~
