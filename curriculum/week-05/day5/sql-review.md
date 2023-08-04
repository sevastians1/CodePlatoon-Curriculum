# SQL Review Day


### SQL Queries

We use SQL commands to interact with our data in our database tables. 

- SELECT
  - Used to extract data records from a table
  
  ```sql
  -- retrieve all students whose first name begins with the letter 'A'
  SELECT students.first_name, students.last_name -- sepficy the fields to display in the result
  FROM students -- specify which table to search
  WHERE students.first_name ILIKE 'A%'; --specify the condition our to-be-retrieved records much satisfy
  ```

- INSERT
  - Used to insert data records into a table
  
  ```sql
  -- insert a few new students
  INSERT INTO students (first_name, last_name, birth_date, locker_id) --specify which fields to add values into
  VALUES --specify the values of each new record (value order must match the field order listed above)
    ('Anna', 'Abrams', '2005-05-05', 2),
    ('Biil', 'Brooks', '2006-06-06', 4),
    ('Cassie', 'Cage', '2007-07-07', 6),
    ('David', 'Derby', '2008-08-08', 8);
  ```
- UDPATE
  - Used to update data records in a table
  
  ```sql
  -- reassign student with id 77 to a new locker with id 101
  UPDATE students -- speficy which table to search and update
  SET locker_id = 101 -- specify the new field value
  WHERE student.id = 77; --specify the condition our to-be-updated record(s) much satisfy
  ```
- DELETE
  - Used to delete data records in a table 
  
  ```sql
  -- delete all students with the last name of 'Cage'
  DELETE FROM students -- speficy which table to search and update
  WHERE student.last_name = 'Cage'; --specify the condition our to-be-deleted record(s) much satisfy
  ```
- JOIN
  - Used to link data records from two different tables together
  - Often used on Foreign Key related fields
  
  ```sql
  -- display the student id, locker number, and locker combination for all students
  SELECT S.id, L.number, L.combination
  FROM students S
  JOIN lockers L ON S.locker_id = L.id; -- link the student record with their corresponding locker record, matching the locker_id field from the student record with the primary id from the locker record 
  ```

### Constraints

We create constraints in our database tables so that the data records within those tables adhere to certain rules.

- Primary Key (PK)
  - A field (i.e. column) that acts as the the primary unique identifier for a given table
- Foreign Key (FK)
  - A field whose values references (i.e. must match) another column's value, usually from another table
  - Essentially used to "link" a record from one table to a record in another table
- Unique Key (UQ)
  - A field whose value will be guaranteed to be unique across all other records in the SAME table
  - Similar to a primary key
- NULL
  - Can the value for a field be left empty?
- CHECK
  - Does the value for a field need to satisfy some condition? (e.g gpa <= 4.0 && gpa >= 0.0)

[Contraints docs](https://www.postgresql.org/docs/current/ddl-constraints.html)

### Entity relationships

We create relationships between our database tables to model our application's data in a correct manner.

- One-to-One
  - Example: Students & Lockers
    - Each student can have only ONE locker
    - Eack locker can be only used by ONE student
  - Solution: Add a Foreign Key + Unique Key
    - Create a Foreign Key constraint on the "locker_id" field in the Students table, referencing the Lockers table, so that a student is guaranteed to be assigned to an existing locker
    - Create a Unique Key constraint on the "locker_id" field in the Students table, so that no single locker can be assigned to two different Student records in the Students table

  ```sql
  CREATE TABLE lockers (
    id SERIAL PRIMARY KEY,
    number INTEGER  NOT NULL,
    combination VARCHAR(12) NOT NULL
  );

  CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(32) NOT NULL,
    last_name VARCHAR(32) NOT NULL,
    birth_date DATE NOT NULL,
    locker_id INTEGER UNIQUE REFERENCES lockers (id) -- foreign key relation + unique key relation, nullable (a student might not have a locker)
  );
  ```

- One-to-Many
  - Example: Professors & Courses
    - Each professor can teach MANY different courses
    - Each course can only have ONE professor teaching it
  - Solution: Add a Foreign Key
    - Create a Foreign Key constraint on the "professor_id" field in the Courses table, referencing the Professors table, so that the course is guaranteed to be taught by an existing professor

  ```sql
  CREATE TABLE professors (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(32) NOT NULL,
    last_name VARCHAR(32) NOT NULL,
    start_date DATE NOT NULL
  );

  CREATE TABLE courses (
    id SERIAL PRIMARY KEY,
    name VARCHAR(64) NOT NULL,
    description TEXT,
    credits SMALLINT NOT NULL,
    professor_id INTEGER NOT NULL REFERENCES professors (id) -- foreign key relation, non-nullable (a course MUST have a professor)
  );
  ```

- Many-to-Many
  - Example: Students & Courses
    - Each student can be enrolled in MANY different courses
    - Each course can have MANY different students enrolled in it
  - Solution: Create a Join Table
    - Create a new table, which should contain a Foreign Key "student_id" field, referencing the Students table, and a Foriegn Key "course_id" field, referencing teh Courses table
    - This new table allows us to sastify our many-to-many relationship without having to duplicate record data in other tables 

  ```sql
  -- join table to combine students with courses in a many-to-many relation
  CREATE TABLE enrollments (
    id SERIAL PRIMARY KEY,
    student_id INTEGER NOT NULL REFERENCES students (id),
    course_id INTEGER NOT NULL REFERENCES courses (id),
    grade VARCHAR(2),
    UNIQUE(student_id, course_id) -- unique key relation (prevent a student from being enrolled in the same course more than once)
  );
  ```
  
### Final Schema

```sql
CREATE TABLE lockers (
  id SERIAL PRIMARY KEY,
  number INTEGER  NOT NULL,
  combination VARCHAR(12) NOT NULL
);


CREATE TABLE students (
  id SERIAL PRIMARY KEY,
  first_name VARCHAR(32) NOT NULL,
  last_name VARCHAR(32) NOT NULL,
  birth_date DATE NOT NULL,
  locker_id INTEGER UNIQUE REFERENCES lockers (id) -- foreign key relation + unique key relation, nullable (a student might not have a locker)
);


CREATE TABLE professors (
  id SERIAL PRIMARY KEY,
  first_name VARCHAR(32) NOT NULL,
  last_name VARCHAR(32) NOT NULL,
  start_date DATE NOT NULL
);


CREATE TABLE courses (
  id SERIAL PRIMARY KEY,
  name VARCHAR(64) NOT NULL,
  description TEXT,
  credits SMALLINT NOT NULL,
  professor_id INTEGER NOT NULL REFERENCES professors (id) -- foreign key relation, non-nullable (a course MUST have a professor)
);


CREATE TABLE enrollments (
  id SERIAL PRIMARY KEY,
  student_id INTEGER NOT NULL REFERENCES students (id),
  course_id INTEGER NOT NULL REFERENCES courses (id),
  grade VARCHAR(2),
  UNIQUE(student_id, course_id) -- unique key relation (prevent a student from being enrolled in the same course more than once)
);
```

### Seed Data

```sql
INSERT INTO lockers (id, number, combination)
VALUES
  (1, 101, '11-21-31'),
  (2, 102, '12-22-32'),
  (3, 103, '13-23-33'),
  (4, 104, '14-24-34'),
  (5, 205, '15-25-35'),
  (6, 206, '16-26-36'),
  (7, 207, '17-27-37'),
  (8, 208, '18-28-38');


INSERT INTO students (first_name, last_name, birth_date, locker_id)
VALUES
  ('Anna', 'Abrams', '2005-05-05', 2),
  ('Biil', 'Brooks', '2006-06-06', 4),
  ('Cassie', 'Cage', '2007-07-07', 6),
  ('David', 'Derby', '2008-08-08', 8);


INSERT INTO professors (first_name, last_name, start_date)
VALUES
  ('Charlie', 'Chaplin', '2010-01-01'),
  ('Rachael', 'Ray', '2012-01-01'),
  ('Steven', 'Spielburg', '2014-01-01'),
  ('Tina', 'Turner', '2016-01-01'),
  ('Vince', 'Vaughn', '2018-01-01');


INSERT INTO courses (name, description, credits, professor_id)
VALUES
  ('CS 101', 'Computer Science Fundamentals', 3, 1),
  ('CS 202', 'Computer Science Intermediate', 4, 5),
  ('Databases 203', 'Working with databases', 4, 3),
  ('Cooking 301', 'Learn how to cook', 3, 2),
  ('Singing 203', NULL, 3, 5),
  ('Singing 307', NULL, 4, 4);


INSERT INTO enrollments (student_id, course_id, grade)
VALUES
  (1, 2, 'B+'),
  (1, 3, 'A-'),
  (1, 5, NULL),
  (2, 1, 'B'),
  (2, 3, 'A+'),
  (2, 5, NULL),
  (3, 1, 'C'),
  (4, 4, 'B-'),
  (4, 1, 'F'),
  (4, 2, NULL);
```


- display student name, course name(s) for the student with locker combination '17-27-37'

## Resources 

### SQL Workshop
- [Links to video and materials repo](https://github.com/sierraplatoon/curriculum/blob/main/week-05/day5/sql-workshop.md)

### Working with SQL in python
- [Executing SQL using a python library (psycopg)](https://www.youtube.com/watch?v=BThxg1U-p08&list=PLu0CiQ7bzwEQknl3vI9wEMwgNBzcg9kOs&index=7)
- [Transactions](https://youtu.be/BThxg1U-p08?list=PLu0CiQ7bzwEQknl3vI9wEMwgNBzcg9kOs&t=2911)
- [From CSV to database](https://youtu.be/BThxg1U-p08?list=PLu0CiQ7bzwEQknl3vI9wEMwgNBzcg9kOs&t=5665)

## Assignments
- [SQL Zoo](http://sqlzoo.net/) 
- [PG Exercises](https://pgexercises.com/questions/basic/) 
