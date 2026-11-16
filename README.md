# SQL & Relational Database Design — Student Assignment Pack

- **Student:** Gülay Yusifli
- **Group:** 844i
- **DBMS Used:** SQLite 3 (DB Browser for SQLite 3.13.1)

---

## Repository Structure

- `SalesDB_Solutions.sql` — Complete SQL script containing table definitions (`CREATE TABLE`), reference data (`INSERT`), Basic SQL tasks (`B01–B10`), and Business Scenarios (`1.1–6.10`).
- `ERD Diagrams - Project A and Project B.pdf` — Entity-Relationship Diagrams for **Project A (Sales Management Database)** and **Project B (University Course Registration System)**.
- `SalesDBgulay.db` — Clean reference SQLite database (`9` customers, `8` products, `5` employees, `24` orders).
- `SalesDB_Practicegulay.db` — Practice SQLite database used for safe `INSERT`, `UPDATE`, `DELETE`, and `ALTER` operations.

---

## Written Explanations & Query Analysis

### Part I — Task B10 (Safe Data Modification)
Without a `WHERE` clause, an `UPDATE` statement modifies every single row in the `Products` table, and a `DELETE` statement removes all records from the table. Filtering by the primary key (`WHERE ProductID = 901`) restricts the modification strictly to the intended single test row while keeping all reference products intact.

### Scenario 1 — Inactive Customers vs. Excluded Customers
A customer who never ordered (`CustomerID = 9`, Ali Hasanov) has no matching records in the `Orders` table at all and is identified via `LEFT JOIN ... WHERE o.OrderID IS NULL`. In contrast, a customer excluded by a campaign filter has existing orders in the database, but those orders do not satisfy the required `WHERE` or `HAVING` conditions (such as country, date range, or minimum spending threshold).

### Scenario 4 — Targeted Campaign Planning
- **Filter Query (Task 4.3):** `ProductID = 101` (`Wireless Mouse`, price `55.00`) is included because it belongs to `Electronics`, falls within the `40–100` price range, and contains `Wireless` in its name. `ProductID = 103` (`Laptop`, `850.00`) is excluded because its price exceeds `100` and its name contains neither `Wireless` nor `USB`.
- **JOIN Query (Task 4.7):** `CustomerID = 4` (Sara Martin, France) is included in the reactivation list because she has no qualifying October sales (`Order 208` is `Pending` and `Order 222` has an invalid `ShipDate < OrderDate`), leaving `o.OrderID IS NULL` after the `LEFT JOIN`. `CustomerID = 3` (Emma Schmidt, Germany) is excluded because she has a valid delivered October order (`Order 205`).

### Scenario 5 — Fulfilment Exception Desk
- **Filter Query (Task 5.5):** `OrderID = 221` is flagged as inconsistent because its status is `Shipped` while `ShipDate` is `NULL`, and `OrderID = 222` is flagged because `ShipDate` (`2026-10-30`) is earlier than `OrderDate` (`2026-10-31`). `OrderID = 203` (`Pending` with `ShipDate IS NULL`) is excluded from the exception list because a `Pending` order is normally unshipped.
- **JOIN Query (Task 5.7):** `EmployeeID = 15` (Nina Stone) works in the `Sales` department and has zero pending orders, so `LEFT JOIN` keeps her in the workload report with `0` count and `0` sales. `EmployeeID = 13` (Alex Green) is excluded because he belongs to the `Support` department (`WHERE e.Department = 'Sales'`).

### Scenario 6 — Sales Team Review & Retention
- **Filter Query (Task 6.4):** `EmployeeID = 11` (David Lee) meets the monthly target with `3` qualifying October sales (`Orders 201, 206, 213`) totaling `1505.00` (`>= 2` orders and `>= 1000` sales). `EmployeeID = 15` (Nina Stone) is excluded because she has `0` qualifying sales in October. *(Note: Task 6.8 identifies customers who had not returned by `2026-11-07`, not permanent inactivity; Task 6.9 identifies customers new to the `Nov 01–07` comparison window, not necessarily first-time buyers overall.)*
- **JOIN Query (Task 6.1):** Using `LEFT JOIN` in the self-join ensures `EmployeeID = 12` (Maya Brown) remains in the directory with `ManagerName = NULL` since she has no manager (`ManagerID IS NULL`), while `EmployeeID = 11` (David Lee) is matched to Maya Brown via `ManagerID = 12`.

---

## Database Design Notes (ERD Projects)

- **Project A (Sales Management):** Captures `1:N` relationships from `Customers`, `Products`, and `Employees` to `Orders`, along with a `0..1 : 0..N` self-relationship on `Employees` (`ManagerID -> EmployeeID`).
- **Project B (University Course Registration):** Resolves the `M:N` relationship between `Students` and `Courses` using the associative `Enrollments` table, with a composite `UNIQUE(StudentID, CourseID)` constraint to prevent duplicate enrollment in the same course.