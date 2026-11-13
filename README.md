# AUTHOR: PEARL

# Library Management System - Database Design Principles & Multi-Table Schemas

## 1. Database Purpose / Mission Statement
The purpose of the Library Management System database is to efficiently store,
organise and manage information about books, members, authors and borrowing
activities. It aims to eliminate data duplication, ensure data accuracy, and
allow librarians to quickly track which books are available, who borrowed them,
and when they are due back.

## 2. Table Separation
To avoid data redundancy, data is separated by subject into distinct tables.
Each table stores only information about one subject:

1.  **Authors** - Stores information about authors only
2.  **Books** - Stores information about books only
3.  **Members** - Stores information about library members only
4.  **Borrowings** - Stores information about borrowing transactions only

This separation follows database design principles: one subject per table,
no repeating groups, and relationships are created using Primary Keys and
Foreign Keys instead of duplicating data.

## 3. Fields & Primary Keys - Members Table

| Field Name | Data Type | Description |
| :--- | :--- | :--- |
| member_id | SERIAL PRIMARY KEY | Unique ID for each member |
| first_name | VARCHAR(100) | Member's first name |
| last_name | VARCHAR(100) | Member's last name |
| email | VARCHAR(150) UNIQUE | Member's email address |
| phone_number | VARCHAR(20) | Member's contact number |
| join_date | DATE | Date member joined the library |

**Primary Key:** `member_id`
It uniquely identifies each member and ensures no two members have the same ID.
It is used as a Foreign Key in the Borrowings table to link a borrowing record
to a specific member.

## 4. PostgreSQL Practical Exercise

### Step 1: Create and Connect to Database
```sql
CREATE DATABASE library_db;
\c library_db

### Step 2: Create Parent Table - authors
CREATE TABLE authors (
    author_id SERIAL PRIMARY KEY,
    author_name VARCHAR(100)
);

### Step 3: Create Child Table - books
CREATE TABLE books (
    book_id SERIAL PRIMARY KEY,
    title VARCHAR(150),
    published_year INT,
    author_id INT REFERENCES authors(author_id)
);
author_id in books is a Foreign Key that references author_id in authors.
This creates a One-to-Many relationship: one author can write many books.

### Step 4: Insert Sample Records
INSERT INTO authors (author_name) VALUES
('Chimamanda Ngozi Adichie'),
('Ngugi wa Thiong''o'),
('Trevor Noah');

INSERT INTO books (title, published_year, author_id) VALUES
('Americanah', 2013, 1),
('Purple Hibiscus', 2003, 1),
('Decolonising the Mind', 1986, 2),
('Born a Crime', 2016, 3);

### Step 5: JOIN Query - Books Alongside Their Authors
SELECT books.book_id, books.title, books.published_year, authors.author_name
FROM books
JOIN authors ON books.author_id = authors.author_id;

### Expected Output:
1 | Americanah | 2013 | Chimamanda Ngozi Adichie
2 | Purple Hibiscus | 2003 | Chimamanda Ngozi Adichie
3 | Decolonising the Mind | 1986 | Ngugi wa Thiong'o
4 | Born a Crime | 2016 | Trevor Noah

