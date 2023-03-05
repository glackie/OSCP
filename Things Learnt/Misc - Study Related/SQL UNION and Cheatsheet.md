<mark style="background: #ADCCFFA6;">SQL</mark>

UNION

Used to combine the result-set of two or more SELECT statements.

SELECT column_name(s) FROM Table1 UNION SELECT column_name(s) FROM Table2;

UNION ALL

Combines the result set of two or more SELECT statements(it allows duplicate values)

SELECT City FROM table1 UNION ALL SELECT City FROM table2 ORDER BY City;


SQL Injection Cheatsheet:
https://portswigger.net/web-security/sql-injection/cheat-sheet


