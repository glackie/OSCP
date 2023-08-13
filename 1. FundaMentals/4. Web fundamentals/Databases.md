Web applications utilize back end [databases](https://en.wikipedia.org/wiki/Database) to store various content and information related to the web application. 

This can be core web application assets like images and files, web application content like posts and updates, or user data like usernames and passwords. 

This allows web applications to easily and quickly store and retrieve data and enable dynamic content that is different for each user.

There are many different types of databases, each of which fits a certain type of use. Most developers look for certain characteristics in a database, such as `speed` in storing and retrieving data, `size` when storing large amounts of data, `scalability` as the web application grows, and `cost`.

---

## Relational (SQL)

[Relational](https://en.wikipedia.org/wiki/Relational_database) (SQL) databases store their data in tables, rows, and columns. Each table can have unique keys, which can link tables together and create relationships between tables.

For example, we can have a `users` table in a relational database containing columns like `id`, `username`, `first_name`, `last_name`, and so on. 

The `id` can be used as the table key. Another table, `posts`, may contain posts made by all users, with columns like `id`, `user_id`, `date`, `content`, and so on.

We can link the `id` from the `users` table to the `user_id` in the `posts` table to easily retrieve the user details for each post, without having to store all user details with each post.

A table can have more than one key, as another column can be used as a key to link with another table. For example, the `id` column can be used as a key to link the `posts` table to another table containing comments, each of which belongs to a certain post, and so on.

<mark style="background: #BBFABBA6;">The relationship between tables within a database is called a Schema.</mark>


<mark style="background: #ADCCFFA6;">
Some of the most common relational databases include:</mark>

Type - Description

[MySQL](https://en.wikipedia.org/wiki/MySQL)

The most commonly used database around the internet. It is an open-source database and can be used completely free of charge

[MSSQL](https://en.wikipedia.org/wiki/Microsoft_SQL_Server)

Microsoft's implementation of a relational database. Widely used with Windows Servers and IIS web servers

[Oracle](https://en.wikipedia.org/wiki/Oracle_Database)

A very reliable database for big businesses, and is frequently updated with innovative database solutions to make it faster and more reliable. It can be costly, even for big businesses

[PostgreSQL](https://en.wikipedia.org/wiki/PostgreSQL)

Another free and open-source relational database. It is designed to be easily extensible, enabling adding advanced new features without needing a major change to the initial database design

Other common SQL databases include: `SQLite`, `MariaDB`, `Amazon Aurora`, and `Azure SQL`.

## Use in Web Applications

Most modern web development languages and frameworks make it easy to integrate, store, and retrieve data from various database types. 


But first, the database has to be installed and set up on the back end server, and once it is up and running, the web applications can start utilizing it to store and retrieve data.

For example, within a `PHP` web application, once `MySQL` is up and running, we can connect to the database server with:

Code: php

```php
$conn = new mysqli("localhost", "user", "pass");
```

Then, we can create a new database with:

Code: php

```php
$sql = "CREATE DATABASE database1";
$conn->query($sql)
```

After that, we can connect to our new database, and start using the `MySQL` database through `MySQL` syntax, right within `PHP`, as follows:

Code: php

```php
$conn = new mysqli("localhost", "user", "pass", "database1");
$query = "select * from table_1";
$result = $conn->query($query);
```

Web applications usually use user-input when retrieving data. 

For example, when a user uses the search function to search for other users, their search input is passed to the web application, which uses the input to search within the database(s).

Code: php

```php
$searchInput =  $_POST['findUser'];
$query = "select * from users where name like '%$searchInput%'";
$result = $conn->query($query);
```

Finally, the web application sends the result back to the user:

Code: php

```php
while($row = $result->fetch_assoc() ){
	echo $row["name"]."<br>";
}
```

This basic example shows us how easy it is to utilize databases. However, if not securely coded, database code can lead to a variety of issues, like [SQL Injection vulnerabilities](https://owasp.org/www-community/attacks/SQL_Injection).


