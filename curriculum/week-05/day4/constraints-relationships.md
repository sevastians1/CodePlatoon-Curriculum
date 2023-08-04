# Constraints and Relationships


## Topics Covered / Goals
- Database Constraints
- Database Relationships
- Database Schema Design

## Lesson

The larger your application, the more tables you will have, and the more ways they will intersect. Database schema design is especially important for fullstack applications. Today, we'll talk about types of constraints you can add to database tables, types of relationships you can create between database tables, and how to put it all together to create a database schema design that works for our application needs. 

### Constraints

A database constraint is effectively a rule (or limitation) that we add to a column or multiple columns for a table. These help us create proper relationships without worrying about invalid data. There are a few types of constraints that we can add (see: [constraints](https://www.postgresql.org/docs/14/ddl-constraints.html)) to our tables, but we're going to focus on these three:

- Primary Key (PK)
  - Establishes the main (i.e. primary) unique identification column(s) for a table's records
  - Every table in a relational database should have a PK
  - PK column values can't be NULL

```sql
CREATE TABLE professors (
  id serial PRIMARY KEY,
  ...
);
```

- Foreign Key (FK)
  - Establishes column(s) whose values references (i.e. matches) a PK in another table (common), or even in the same table (rare)
  - FK column values can be NULL ("orphaned record")

```sql
CREATE TABLE courses (
  ...
  professor_id integer REFERENCES professors (id),
);
```

...or...

```sql
CREATE TABLE courses (
  ...
  professor_id integer
);

ALTER TABLE courses
ADD CONSTRAINT fk_course_professors
FOREIGN KEY (professor_id) 
REFERENCES professors (id);
```

- Unique Key (UQ) 
  - Establishes column(s) whose values must be unique across all records 
  - UQ column values can be NULL

```sql
CREATE TABLE lockers (
  ...
  user_id integer UNIQUE,
);
```


### Relationships

Entities (i.e. tables) in our database can have one of three main relationships with one another:
- One-to-One
  - A relationship where AT MOST one record from Table-A can ever be referenced by a record from Table-B
  - This is usually a rare relationship to find in most database models
  - This is achieved in one of two ways:
    - A primary key + foreign key
    - A foreign key + unique key   
- One-To-Many
  - A relationship where one record from Table-A may be referenced by one or more records from Table-B
  - This is achieved by creating a foreign key on a column in Table-B, to reference the PK in Table-A
- Many-To-Many
  - A relationship where a record from Table-A may be associated with one or more records from Table-B, and a record from Table-B may be associated with one or more records from Table-A
  - This is achieved by creating a join table (a.k.a. a through table)

### Database Schema Design

Let's use Facebook as a simple example to understand how to create a database schema that satisfies our needs, and incorporates using some of the constraints and relationships we talked about above. We're going to try to model the concept of having users who can write posts, write comments, or react to posts on Facebook. Here are the main tables we would need to create:


**user_accounts** |
---|
id |
username |
password |
last_login_date |


**user_profiles** |
---|
id |
user_id |
profile_photo_url |
about_me |
personal_quote |

**posts** |
---|
id |
content |
user_id |

**comments** |
---|
id |
content |
user_id |
post_id |

**reaction_types**
---|
id |
type |


Now let's talk about some relationships that we'd want to establish here between our tables...

**What is the relationship between `user_accounts` and `user_profiles`?**

...Can a user have more than one profile? **No.**  
...Can a profile have more than one user? **No.**  

This is a one-to-one relationship!

- *Solution:* Create a FK + UQ constraint for column `user_id` in table `user_profiles`

**What is the relationship between `user_accounts` and `posts`?**

...Can a user create more than one post? **Yes.**  
...Can a post be created by more than one user? **No.**  

This is a one-to-many relationship!

- *Solution:* Create a FK constraint for column-`user_id` in table-`posts`

**What is the relationship between `posts` and `reaction_types`?**

...Can a post have more than one reaction (i.e. "LIKE")? **Yes.**  
...Can a reaction ("LIKE") be made to more than posts? **Yes.**  

This is a many-to-many relationship!

- *Solution:* Create a new join table (using FKs) between table-`posts` and table-`reaction_types`, called table-`post_reactions`:

**post_reactions**
---|
id |
post_id |
reaction_id |
user_id |

In this new table, column-`post_id` would have a FK to table-`posts`, column-`reaction_id` would have a FK to table-`reaction_types`, and colunm-`user_id` would have a FK to table-`user_accounts`. 

Additionally, we may want to create a UQ for column-`post_id` + column-`user_id` because the same user is not allowed to have multiple reactions to the same post. 

## Assignments
- [Schema Design](https://github.com/sierraplatoon/sql-schema-design)
- [Schema Script](https://github.com/sierraplatoon/sql-schema-script)
- [Schema Modifications](https://github.com/sierraplatoon/sql-schema-modifications)

