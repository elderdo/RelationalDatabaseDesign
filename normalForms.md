# Database Normal Forms (Video-Style Review)

Normalization reduces redundancy and update anomalies. Rules are cumulative:
to be in a higher form, all lower forms must already hold.

## Dependency Notation

- `A -> B`: functional dependency (A determines B)
- `AB -> C`: composite determinant
- `A ->> B`: multivalued dependency (used in 4NF)

---

## 1NF

Rule:

- Values must be atomic (no lists in one cell)
- No repeating groups

Example relation:

- `Orders(order_id, customer_name, items)`

Problem:

- `items` holds multiple values, so rows are not atomic

Decomposition:

- `Orders(order_id, customer_name)`
- `OrderItems(order_id, item_name)`

SQL Server example:

```sql
CREATE TABLE Orders (
    order_id INT IDENTITY(1,1) PRIMARY KEY,
    customer_name NVARCHAR(100) NOT NULL
);

CREATE TABLE OrderItems (
    order_item_id INT IDENTITY(1,1) PRIMARY KEY,
    order_id INT NOT NULL,
    item_name NVARCHAR(100) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id)
);
```

---

## 2NF

Rule:

- Must be in 1NF
- No partial dependency on a composite key

Example relation:

- `OrderLine(order_id, product_id, product_name, qty)`
- Primary key: `(order_id, product_id)`

Dependencies:

- `(order_id, product_id) -> qty`
- `product_id -> product_name` (partial dependency, violates 2NF)

Decomposition:

- `Products(product_id, product_name)`
- `OrderLine(order_id, product_id, qty)`

SQL Server example:

```sql
CREATE TABLE Products (
    product_id INT IDENTITY(1,1) PRIMARY KEY,
    product_name NVARCHAR(100) NOT NULL
);

CREATE TABLE OrderLine (
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    qty INT NOT NULL,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (product_id) REFERENCES Products(product_id)
);
```

---

## 3NF

Rule:

- Must be in 2NF
- No transitive dependency among non-key attributes

Example relation:

- `Employees(emp_id, emp_name, dept_id, dept_name)`

Dependencies:

- `emp_id -> emp_name, dept_id`
- `dept_id -> dept_name`
- Therefore `emp_id -> dept_name` (transitive, violates 3NF)

Decomposition:

- `Departments(dept_id, dept_name)`
- `Employees(emp_id, emp_name, dept_id)`

SQL Server example:

```sql
CREATE TABLE Departments (
    dept_id INT IDENTITY(1,1) PRIMARY KEY,
    dept_name NVARCHAR(100) NOT NULL
);

CREATE TABLE Employees (
    emp_id INT IDENTITY(1,1) PRIMARY KEY,
    emp_name NVARCHAR(100) NOT NULL,
    dept_id INT NOT NULL,
    FOREIGN KEY (dept_id) REFERENCES Departments(dept_id)
);
```

---

## BCNF

Rule:

- For every non-trivial dependency `X -> Y`, `X` must be a superkey

Example relation:

- `Teaching(student_id, course, instructor)`

Dependencies:

- `(student_id, course) -> instructor`
- `instructor -> course` (determinant is not a superkey, violates BCNF)

Decomposition:

- `InstructorCourse(instructor, course)`
- `StudentInstructor(student_id, instructor)`

SQL Server example:

```sql
CREATE TABLE InstructorCourse (
    instructor NVARCHAR(100) PRIMARY KEY,
    course NVARCHAR(100) NOT NULL
);

CREATE TABLE StudentInstructor (
    student_id INT NOT NULL,
    instructor NVARCHAR(100) NOT NULL,
    PRIMARY KEY (student_id, instructor),
    FOREIGN KEY (instructor) REFERENCES InstructorCourse(instructor)
);
```

---

## 4NF

Rule:

- Must be in BCNF
- No non-trivial multivalued dependency unless determinant is a superkey

Example relation:

- `EmployeeInfo(emp_id, skill, project)`

Dependencies:

- `emp_id ->> skill`
- `emp_id ->> project`
- Skills and projects are independent; storing both together causes
  spurious combinations

Decomposition:

- `EmployeeSkill(emp_id, skill)`
- `EmployeeProject(emp_id, project)`

SQL Server example:

```sql
CREATE TABLE EmployeeSkill (
    emp_id INT NOT NULL,
    skill NVARCHAR(100) NOT NULL,
    PRIMARY KEY (emp_id, skill)
);

CREATE TABLE EmployeeProject (
    emp_id INT NOT NULL,
    project NVARCHAR(100) NOT NULL,
    PRIMARY KEY (emp_id, project)
);
```

---

## Fast Review Grid

| Form | Check                                  |
| ---- | -------------------------------------- |
| 1NF  | Atomic values only                     |
| 2NF  | No partial dependency on composite key |
| 3NF  | No transitive dependency               |
| BCNF | Every determinant is a superkey        |
| 4NF  | Separate independent multivalued facts |
