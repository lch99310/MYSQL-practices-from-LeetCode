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

184. Department Highest Salary
~~~~sql
SELECT d.name AS "Department", e.name AS "Employee", e.salary AS "Salary"
FROM Employee e LEFT JOIN Department d
ON e.departmentId = d.id
WHERE (e.departmentId, e.salary) IN (
    SELECT e.departmentId, MAX(e.salary)
    FROM Employee e, Department d
    WHERE e.departmentId = d.id
    GROUP BY e.departmentId)
~~~~

196. Delete Duplicate Emails
~~~~sql
DELETE p1.*
FROM Person p1, Person p2
WHERE p1.email = p2.email AND p1.id > p2.id;
~~~~
* Tip: write SELECT query first, replace SELECT with DELETE 

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

512. Game Play Analysis II
~~~~sql
SELECT a.player_id, a.device_id
FROM Activity a 
WHERE (a.player_id, a.event_date) IN (
    SELECT player_id, MIN(event_date)
    FROM Activity 
    GROUP BY player_id )
~~~~

Wrong answer↓
~~~~sql 
SELECT a.player_id, a.device_id
FROM Activity a 
GROUP BY a.player_id
HAVING MIN(a.event_date)
~~~~
* Because HAVING has to be followed by a condition, however in SELECT clause there is no condition.
* **Note: WHERE clause cannot follow by aggregation, HAVING clause can follow by aggregation. That's different!**

577. Employee Bonus
~~~~sql
SELECT e.name, b.bonus
FROM Employee e 
LEFT JOIN Bonus b
ON e.empId = b.empId
WHERE b.bonus < 1000 OR b.bonus IS null
~~~~
* Because it is possible that there is no bonus, use LEFT JOIN can fix this problem.
* The difference between IS and =, IS is an operator tests a value against a Boolean value. = is eqaul to.

584. Find Customer Referee
~~~~sql
SELECT c.name
FROM Customer c
WHERE c.referee_id != 2 OR c.referee_id IS NULL
~~~~
* Note: != is the same as <> in MySQL

586. Customer Placing the Largest Number of Orders
~~~~sql
SELECT o.customer_number
FROM Orders o
GROUP BY o.customer_number
ORDER BY COUNT(o.order_number) DESC
LIMIT 1
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

597. Friend Requests I: Overall Acceptance Rate
~~~~sql
SELECT IFNULL(ROUND((COUNT(DISTINCT r.requester_id, r.accepter_id)/COUNT(DISTINCT f.sender_id, f.send_to_id)), 2), 0.00) AS "accept_rate" 
FROM FriendRequest f, RequestAccepted r;
~~~~
* NOTE: You should not put () after DISTINCT clause

603. Consecutive Available Seats
~~~~sql
SELECT DISTINCT c1.seat_id
FROM Cinema c1 JOIN Cinema c2
ON ABS(c1.seat_id - c2.seat_id) =1
WHERE c1.free = 1 AND c2.free = 1
ORDER BY c1.seat_id ASC
~~~~
* NOTE: DISTINCT is needed, cuz join on abs = 1, which means it could be +1 or -1 and cause overlap.

607. Sales Person
~~~~sql
SELECT sp.name
FROM SalesPerson sp
WHERE sp.sales_id NOT IN (SELECT o.sales_id
                         FROM Orders o
                         LEFT JOIN Company c ON o.com_id = c.com_id
                         LEFT JOIN SalesPerson sp ON sp.sales_id = o.sales_id
                         WHERE c.name = "RED"
                         );
~~~~

612. Shortest Distance in a Plane
~~~~sql
SELECT ROUND(SQRT(MIN(POW((p1.x - p2.x), 2) + POW((p1.y - p2.y), 2))), 2) AS "shortest"
FROM Point2D p1, Point2D p2
WHERE p1.x != p2.x OR p1.y != p2.y
~~~~

613. Shortest Distance in a Line
~~~~sql
SELECT MIN(ABS(p1.x - p2.x)) AS "shortest"
FROM Point p1, Point p2
WHERE p1.x != p2.x
~~~~

619. Biggest Single Number
~~~~sql
SELECT MAX(mn.num) AS "num"
FROM (SELECT num, COUNT(num) AS "count"
     FROM MyNumbers
     GROUP BY num) mn
WHERE mn.count = 1
~~~~

Answer below should work too↓
~~~~sql
SELECT IFNULL(mn.num, NULL) AS "num"
FROM MyNumbers mn
GROUP BY mn.num
HAVING COUNT(mn.num) = 1
ORDER BY mn.num DESC
LIMIT 1
~~~~
* NOTE: MAX() would take care of NULL on its own. If there is no match, MAX() would return NULL.

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

1050. Actors and Directors Who Cooperated At Least Three Times
~~~~sql
SELECT ad.actor_id, ad.director_id
FROM ActorDirector ad
GROUP BY ad.actor_id, ad.director_id
HAVING COUNT(ad.timestamp) >= 3
~~~~

1068. Product Sales Analysis I
~~~~sql
SELECT p.product_name, s.year, s.price
FROM Sales s, Product p
WHERE s.product_id = p.product_id AND s.sale_id IN (
    SELECT DISTINCT(sale_id)
    FROM Sales)
~~~~

1069. Product Sales Analysis II
~~~~sql
SELECT s.product_id, SUM(s.quantity) AS "total_quantity"
FROM Sales s
GROUP BY s.product_id
~~~~

1070. Product Sales Analysis III
~~~~sql
SELECT s.product_id, s.year AS "first_year", s.quantity, s.price
FROM Sales s
WHERE (s.product_id, s.year) IN (
                SELECT product_id, MIN(year) OVER (PARTITION BY product_id ORDER BY year ASC) AS "year"
                FROM Sales)
~~~~

1075. Project Employees I
~~~~sql
SELECT p.project_id, ROUND(AVG(e.experience_years), 2) AS "average_years"
FROM Employee e, Project p
WHERE e.employee_id = p.employee_id
GROUP BY p.project_id
~~~~

1076. Project Employees II
~~~~sql
SELECT p.project_id
FROM (SELECT project_id, DENSE_RANK() OVER(ORDER BY COUNT(employee_id) DESC) AS "rank" 
     FROM Project
     GROUP BY project_id) p
WHERE p.rank = 1
~~~~

Wrong answer↓
~~~~sql
SELECT p.project_id
FROM (SELECT project_id, DENSE_RANK() OVER(PARTITION BY project_id ORDER BY COUNT(employee_id) DESC) AS "rank" 
     FROM Project) p
WHERE p.rank = 1
~~~~
* NOTE: When PARTITION BY within OVER clause, it means rank within each partition. Howeverm when GROUP BY outside OVER clause, it means rank all groups.

1082. Sales Analysis I
~~~~sql
SELECT s.seller_id
FROM (SELECT seller_id, DENSE_RANK() OVER(ORDER BY SUM(price) DESC) AS "rank"
     FROM Sales s
     GROUP BY s.seller_id) s
WHERE s.rank = 1
~~~~

1083. Sales Analysis II
~~~~sql
SELECT DISTINCT s.buyer_id
FROM Product p LEFT JOIN Sales s
ON p.product_id = s.product_id
WHERE p.product_name = "S8" AND s.buyer_id NOT IN (
    SELECT s.buyer_id
    FROM Product p LEFT JOIN Sales s
    ON p.product_id = s.product_id
    WHERE p.product_name = "iPhone")
~~~~

1084. Sales Analysis III
~~~~sql
SELECT p.product_id, p.product_name
FROM Product p LEFT JOIN Sales s
ON p.product_id = s.product_id
GROUP BY p.product_id
HAVING MIN(s.sale_date) >= '2019-01-01' AND MAX(s.sale_date) <= '2019-03-31'
~~~~

1148. Article Views I
~~~~sql
SELECT DISTINCT v.author_id AS "id"
FROM Views v
WHERE v.author_id = v.viewer_id
ORDER BY v.author_id ASC
~~~~

1173. Immediate Food Delivery I
~~~~sql
SELECT ROUND(SUM(t.immediate) / COUNT(t.immediate)*100, 2) AS "immediate_percentage"
FROM (SELECT d.*, IF(d.order_date = d.customer_pref_delivery_date, 1, 0) AS "immediate"
     FROM Delivery d
     ) t
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

1193. Monthly Transactions I
~~~~sql
SELECT DATE_FORMAT(t.trans_date, '%Y-%m') AS "month", t.country, COUNT(t.state) AS "trans_count", SUM(IF(t.state = "approved", 1, 0)) AS "approved_count", SUM(t.amount) AS "trans_total_amount", SUM(IF(t.state = "approved", t.amount, 0)) AS "approved_total_amount"
FROM Transactions t
GROUP BY DATE_FORMAT(t.trans_date, "%Y-%m"), t.country
~~~~

1251. Average Selling Price
~~~~sql
SELECT t.product_id, ROUND(SUM(t.values) / SUM(t.units), 2) AS "average_price"
FROM (SELECT DISTINCT p.product_id, p.price, u.units, u.units * p.price AS "values"
      FROM Prices p LEFT JOIN UnitsSold u
      ON p.product_id = u.product_id AND
      u.purchase_date BETWEEN p.start_date AND p.end_date
      ) t
GROUP BY t.product_id
~~~~
* NOTE: I think DISTINCT is necceasy here to remove duplicate.

1303. Find the Team Size
~~~~sql
SELECT e.employee_id, COUNT(e.team_id) OVER(PARTITION BY e.team_id) AS "team_size"
FROM Employee e
~~~~

1350. Students With Invalid Departments
~~~~sql
SELECT s.id, s.name
FROM Students s LEFT JOIN Departments d
ON d.id = s.department_id
WHERE d.id IS null
~~~~
* In MySQL, NULL is a special data type. If use = NULL, means nothing (nothing return). So in this case, we can only use IS NULL! 

1571. Warehouse Manager
~~~~sql
SELECT w.name AS "warehouse_name", SUM(p.Width*p.Length*p.Height*w.units) AS "volume"
FROM Warehouse w JOIN Products p
ON w.product_id = p.product_id
GROUP BY w.name
~~~~

1581. Customer Who Visited but Did Not Make Any Transactions
~~~~sql
SELECT v.customer_id, COUNT(v.visit_id) AS "count_no_trans"
FROM Visits v LEFT JOIN Transactions t
ON t.visit_id = v.visit_id
WHERE t.transaction_id IS NULL
GROUP BY v.customer_id
~~~~

1587. Bank Account Summary II
~~~~sql
SELECT u.name, SUM(t.amount) AS "balance"
FROM Transactions t LEFT JOIN Users u
ON t.account = u.account
GROUP BY t.account
HAVING SUM(t.amount) > 10000
~~~~

1683. Invalid Tweets
~~~~sql
SELECT t.tweet_id
FROM Tweets t
WHERE CHAR_LENGTH(t.content) >15
~~~~
* LENGTH() returns the length of the string measured in bytes. CHAR_LENGTH() returns the length of the string measured in characters.

1693. Daily Leads and Partners
~~~~sql
SELECT ds.date_id, ds.make_name, COUNT(DISTINCT ds.lead_id) AS "unique_leads", COUNT(DISTINCT ds.partner_id) AS "unique_partners"
FROM DailySales ds
GROUP BY ds.date_id, ds.make_name
~~~~

1741. Find Total Time Spent by Each Employee
~~~~sql
SELECT e.event_day AS "day", e.emp_id, SUM(e.out_time - e.in_time) AS "total_time"
FROM Employees e
GROUP BY e.emp_id, e.event_day
~~~~

1757. Recyclable and Low Fat Products
~~~~sql
SELECT p.product_id
FROM Products p
WHERE p.low_fats = "Y" AND p.recyclable = "Y"
~~~~

1821. Find Customers With Positive Revenue this Year
~~~~sql
SELECT c.customer_id
FROM Customers c
WHERE c.year = 2021 AND c.revenue > 0
~~~~

1873. Calculate Special Bonus
~~~~sql
SELECT e.employee_id, IF(e.employee_id % 2 !=0 AND e.name NOT LIKE "M%", e.salary, 0) AS "bonus"
FROM Employees e
~~~~
