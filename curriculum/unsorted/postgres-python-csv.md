# Postgres in Python and CSVs


## Topics Covered / Goals
- Use the Psycopg library to create and update rows in a database
- Read and clean rows from a CSV, and insert those rows into a Postgres database
- Understand transactions in SQL, and how to use them in your code.
- Understand SQL-injection, and how to avoid it.

## Lesson

### Postgres and Python
> We have spent the past 3 days learning about SQL and have used the Postgres flavor in our challenges. We've learned how to create/read/update/destroy databases, database columns, and database rows. This is the foundation of all programming. It doesn't matter if you're doing development on the frontend, backend, server, mobile, desktop, etc., everything comes down to data persistence. A solid understanding of SQL will pay off huge dividends over the course of your career, so go back and ensure that you understand as much as you can!

> We will often use SQL within the context of other programming languages and today, we are going to connect Python and Postgres together. The Python library that allows us to connect to our database is called [Psycopg](http://initd.org/psycopg/). We're going to write Python code that can execute SQL commands. Let's test this out.

> Let's create a database that holds the records of everyone in our class and connect to it using Python. Let's create a folder to mess around in called `psycopg-example/` and within there, create `psycopg-example/class_roster.py`. We first need to create a virtual environment, and then install `psycopg` by running:

```sh
$ pip install psycopg
```

Next, let's create the database that we are going to connect to:

```sh
$ createdb class_roster
```

### Creating a database
Inside `class_roster.py`, let's import the Python library and start executing SQL commands:

```python
import psycopg

## Let's connect to our database
connection = psycopg.connect(f"dbname=class_roster")


## Let's create a query to create a students table and execute it
student_table_creation_query = "CREATE TABLE students (id serial PRIMARY KEY, name varchar, favorite_food varchar);"
connection.execute(student_table_creation_query)
connection.commit()
connection.close()
```

> Let's execute this by running:

```sh
python class_roster.py
psql class_roster

class_roster=# \d students
```

> You should see your students table with the columns you created. Continuing on, let's try to add a few records and query the database in `class_roster.py`:


### Parameterized Queries

> One of the benefits of using SQL in a programming language like python is that we can insert values into our queries from variables, instead of hard-coding them. 

```python
import psycopg

## Let's connect to our database
connection = psycopg.connect(f"dbname=class_roster")

## Let's create a query to create a students table and execute it
connection.execute("DROP TABLE IF EXISTS students")
student_table_creation_query = "CREATE TABLE students (id serial PRIMARY KEY, name varchar, favorite_food varchar);"
connection.execute(student_table_creation_query)
# hardcoded values are not very useful
connection.execute("INSERT INTO students (name, favorite_food) VALUES ('Alice', 'Cake')")


# danger! danger!
name="Bob"
food="Lemons"
connection.execute(f"INSERT INTO students (name, favorite_food) VALUES ('{name}', '{food}')")

# SQL injection attack
# name="Malory', 'apples'); DROP TABLE students;--"
# food="Pizza"
# connection.execute(f"INSERT INTO students (name, favorite_food) VALUES ('{name}', '{food}')")

# query params help avoid SQL injection, but they can become hard to read if we have many of them, or if any of them are repeated
name="Carol"
food="Tuna"
connection.execute("INSERT INTO students (name, favorite_food) VALUES (%s, %s)", (name, food))

name="Dan"
food="Bagels"
connection.execute("INSERT INTO students (name, favorite_food) VALUES (%(name)s, %(food)s)", {'name':name, 'food':food})

results = connection.execute("SELECT * FROM students;")
print(results.fetchall())

connection.commit()
connection.close()


```

### Transactions
> You may be wondering why we have to call 'commit' on the connection. The reason is that psycopg is automatically creating a transaction for us when we create a connection, but we still have to close it ourselves. 

> In SQL, if you want to group several queries together, you can run them inside of a transaction with the SQL statement `BEGIN TRANSACTION`. Psycopg does this for you automatically when you call `psycopg.connect()`. Then, when all of the related transactions succeed, you can commit the transaction with `COMMIT`. If something goes wrong, you can undo all of the related queries with `ROLLBACK`.

```python

# create a table for accounts
connection.execute("DROP TABLE IF EXISTS accounts")
account_table_creation_query = "CREATE TABLE accounts (id serial PRIMARY KEY, account_name varchar, balance int);"
connection.execute(account_table_creation_query)

# create two accounts
account_name="My Account"
balance= 100
connection.execute("INSERT INTO accounts (account_name, balance) VALUES (%(account_name)s, %(balance)s)", 
    {'account_name':account_name, 'balance':balance}
)

account_name="Your Account"
balance = 0
connection.execute("INSERT INTO accounts (account_name, balance) VALUES (%(account_name)s, %(balance)s)", 
    {'account_name':account_name, 'balance':balance}
)

results = connection.execute("SELECT * FROM accounts;")
print(results.fetchall())

# commit this transaction, adding two accounts 
connection.commit()

try:
    # transfer money from my account to your account
    connection.execute("UPDATE accounts SET balance=balance - 25 WHERE account_name = 'My Account';")

    # throwing an exception here means that I lose money, but you don't gain it, unless we use a transaction
    # raise Exception('oops!')
    connection.execute("UPDATE accounts SET balance=balance + 25 WHERE account_name = 'Your Account';")

    connection.commit()
    
except Exception:
    # something went wrong, so we should roll back the transaction
    print("Query failed! Roll back.")
    connection.rollback()

results = connection.execute("SELECT * FROM accounts;")
print(results.fetchall())

connection.close()

```

### Context Managers using `with`
> The above pattern with try/except is a reliable way to manage transactions, but it can be a bit tedious to manually commit and roll back your transactions all the time. Fortunately, the connection object can be used as a context manager, which means that it can automatically set itself up, and clean up when you're done, by using the `with` keyword. 

```python
with psycopg.connect(f"dbname=class_roster", autocommit=False) as connection:
    connection.execute("DROP TABLE IF EXISTS accounts")
    account_table_creation_query = "CREATE TABLE accounts (id serial PRIMARY KEY, account_name varchar, balance int);"
    connection.execute(account_table_creation_query)

    # create two accounts
    account_name="My Account"
    balance= 100
    connection.execute("INSERT INTO accounts (account_name, balance) VALUES (%(account_name)s, %(balance)s)", 
        {'account_name':account_name, 'balance':balance}
    )

    account_name="Your Account"
    balance = 0
    connection.execute("INSERT INTO accounts (account_name, balance) VALUES (%(account_name)s, %(balance)s)", 
        {'account_name':account_name, 'balance':balance}
    )
    # transfer money from my account to your account
    connection.execute("UPDATE accounts SET balance=balance - 25 WHERE account_name = 'My Account';")

    # if an error is raised, the transaction is automatically rolled back
    # raise Exception('oops!')
    connection.execute("UPDATE accounts SET balance=balance + 25 WHERE account_name = 'Your Account';")
    results = connection.execute("SELECT * FROM accounts;")
    print(results.fetchall())

# after the with-block, the transaction is committed, and the connection is closed
```



### CSV and Databases

Whether you love or hate it, CSV will be around forever. Business people all love Excel and as we've seen, CSV is nothing more than Excel without fanciness. Oftentimes, you'll have to work with CSV and databases together. Let's grab real estate transactions from City of Sacramento and put that information into a database. Click [here](../page-resources/sacramento_re_transactions.csv) to get the dataset.

Let's create a database, read the CSV file, clean some data, and insert the rows into the database together.

First, let's create a database:
```sh
$ createdb sacramento_real_estate
```
Next, let's create a file called `csv_example.py` and read into the CSV file and clean it up:
```python
import csv
import psycopg

with open('../page-resources/sacramento_re_transactions.csv', mode='r') as csv_file:
    csv_reader = csv.DictReader(csv_file)
    for row in csv_reader:
        # set a breakpoint here to view the row data
        pass
```

Now, if we run `python csv_example.py`, we stop where the breakpoint is set. If we enter `row`, we get an OrderedDict that we can access like a dictionary:
```
      6     csv_reader = csv.DictReader(csv_file)
----> 7     for row in csv_reader:
      8         pass 

{'street': '3526 HIGH ST', 'city': 'SACRAMENTO', 'zip': '95838', 'state': 'CA', 'beds': '2', 'baths': '1', 'sq__ft': '836', 'type': 'Residential', 'sale_date': '09/09/18', 'price': '59222', 'latitude': '38.631913', 'longitude': '-121.434879'}
ipdb> row['state']
'CA'
```

Notice that CSV reads everything in as a string. That's problematic for us if we want SQL to do any calculations. It applies to most fields. Let's clean it together and then save it into the database:

```python
import csv
import psycopg
import os

from decimal import Decimal
from datetime import datetime

table_creation_query = """
    CREATE TABLE properties (
        id serial PRIMARY KEY, 
        street_address varchar, 
        city varchar, 
        zip_code varchar, 
        state varchar, 
        number_of_beds integer, 
        number_of_baths integer, 
        square_feet integer, 
        property_type varchar, 
        sale_date timestamp, 
        sale_price integer, 
        latitude decimal, 
        longitude decimal
    );
"""

def clean_data(csv_row):
    cleaned = {}
    cleaned['street_address'] = csv_row['street']
    cleaned['city'] = csv_row['city']
    cleaned['zip_code'] = csv_row['zip']
    cleaned['state'] = csv_row['state']
    cleaned['number_of_beds'] = int(csv_row['beds'])
    cleaned['number_of_baths'] = int(csv_row['baths'])
    cleaned['square_feet'] = int(csv_row['sq__ft'])
    cleaned['property_type'] = csv_row['type']
    cleaned['sale_date'] = datetime.strptime(csv_row['sale_date'], '%m/%d/%y')
    cleaned['sale_price'] = csv_row['price']
    cleaned['latitude'] = Decimal(csv_row['latitude'])
    cleaned['longitude'] = Decimal(csv_row['longitude'])
    return cleaned

connection = psycopg.connect(f"dbname=sacramento_real_estate")
connection.execute(table_creation_query)

with open('../page-resources/sacramento_re_transactions.csv', mode='r') as csv_file:
    csv_reader = csv.DictReader(csv_file)
    for row in csv_reader:
        cleaned_data = clean_data(row)
        connection.execute("INSERT INTO properties (street_address, city, zip_code, state, number_of_beds, number_of_baths, square_feet, property_type, sale_date, sale_price, latitude, longitude) VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)", (cleaned_data['street_address'], cleaned_data['city'], cleaned_data['zip_code'], cleaned_data['state'], cleaned_data['number_of_beds'], cleaned_data['number_of_baths'], cleaned_data['square_feet'], cleaned_data['property_type'], cleaned_data['sale_date'], cleaned_data['sale_price'], cleaned_data['latitude'], cleaned_data['longitude']))

connection.commit()
connection.close()
```

With this data out of the CSV and in the database, you can query it and get some great insights into it. You can answer questions like:
- Are single family homes or condos more expensive on average?
- What's the most expensive house and condo sold? What's the cost per square foot?
- How much does each bedroom cost for a house in Sacramento? What if you wanted to get one more bedroom? What if you wanted one fewer?
- Same as above, but for bathrooms

Create queries in SQL and execute them to get a Python object back. Using those results and your knowledge of Python, answer the questions above.


## External Resources
- [Official Documentation](http://initd.org/psycopg/docs/usage.html)
- [SQL Injection](https://xkcd.com/327/)

## Assignments
- [Chicago Salaries](https://github.com/romeoplatoon/sql-city-of-chicago)
