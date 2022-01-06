# MYSQL-practice-from-github

620. Not Boring Movies
SELECT c.*
FROM Cinema c
WHERE (c.id % 2) = 1 AND c.description != "boring"
ORDER BY rating desc
