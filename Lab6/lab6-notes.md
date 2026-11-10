
# Lab 6 — PHP, PDO, and SQLite Integration

## Part 1: Database Setup

I created the SQLite database using the following command:

```bash
sqlite3 database/tutoring.db < database/setup-database.sql
```

I verified the database using `.tables` and confirmed that both required tables were present:

- students
- tutoring_requests

## Part 2: Running the Application

I started the PHP development server using:

```bash
php -S 0.0.0.0:8000
```

The Peer Tutoring Hub application opened successfully in GitHub Codespaces.

I submitted a test tutoring request for Adonis in course IS 6465. The request was saved and displayed as Request #2 with an initial status of Open.

**Application URL:** [Add the Codespaces browser URL here]

**Request details screenshot:**

![Submitted tutoring request](screenshots/lab6-request-details.png)

## Part 3: Application Workflow Tests

| Test | What I Did | Expected Result | Actual Result | Pass or Fail |
|---|---|---|---|---|
| Submit a valid request | Submitted a tutoring request for Adonis with valid information | A new request is created with status Open | Request #2 was created with status Open | PASS |
| View all requests | Opened the All Requests page | The new request appears in the list | Adonis's request appeared with status Open | PASS |
| Filter requests | Filtered requests by course IS 6465 | Matching requests are displayed | Adonis's request remained visible | PASS |
| Edit an Open request | Updated the description to mention INNER JOIN and LEFT JOIN | The revised information is displayed | The updated description appeared on Request #2 | PASS |
| Cancel an Open request | Cancelled Request #2 | The status changes to Cancelled | The request displayed status Cancelled | PASS |
| Try to edit or cancel a cancelled request | Inspected the cancelled request page | Editing and cancellation are no longer available | Edit and Cancel buttons were no longer displayed | PASS |

## Additional Validation Observation

When I initially entered "SQL Joins" as the request description, the application rejected the submission because the description must contain between 10 and 500 characters.

After entering a longer description, the request was submitted successfully.

## Remaining Work

- Part 4: Trace the request submission workflow
- Part 5: Run integration tests
- Part 6: Complete AI Skills Pack activities and reflection
- Part 7: Inspect the database and explain the SQL JOIN
- Verify documentation and submit

## Part 4: Trace the Request Submission Workflow

1. **Which file displays the tutoring request form?**
   `php/request-form.php` displays the form where students enter their tutoring request information.

2. **Which file receives the submitted form data?**
   `php/process-request.php` receives the submitted form data.

3. **Which file opens the PDO connection to SQLite?**
   `config/db.php` uses the `openDatabase()` function to create a PDO connection to `database/tutoring.db`.

4. **Which file coordinates validation and the database transaction?**
   `services/requestService.php` validates the submitted information, begins the transaction, saves the student and request, and commits or rolls back the transaction.

5. **Which files contain the SQL statements?**
   `repositories/studentPDO.php` and `repositories/requestPDO.php` contain the application repository SQL statements. `database/setup-database.sql` contains the database setup SQL.

6. **Which table stores student information?**
   The `students` table stores student IDs, names, and email addresses.

7. **Which table stores tutoring requests?**
   The `tutoring_requests` table stores request information such as course, topic, preferred date, preferred time, and status.

8. **How does a tutoring request connect to the student who submitted it?**
   The `student_id` column in `tutoring_requests` is a foreign key referencing `students.student_id`.

### Why SQL Statements Belong in Repository Files

SQL statements belong in repository files because they separate database operations from the form and user interface. This makes the application easier to organize, maintain, and test. It also allows database queries to be reused without placing SQL directly inside the form page.

## Part 5: Integration Tests

I ran the integration tests using:

```bash
php -d xdebug.mode=off tests/integration-tests.php
```

The initial run reported three failures because the expected database record counts did not account for the sample data inserted by the setup script. After correcting the test expectations, all 14 integration checks passed.

```text
PASS UC01 create and Open
PASS Lab 4 checkbox values preserved
PASS Duplicate email reuses student
PASS UC02 course and status filter
PASS Invalid reason rejected
PASS Invalid submission writes nothing
PASS Edit Open request
PASS Updated reason persisted
PASS Cancel Open request
PASS Cancelled row retained
PASS Second cancellation rejected
PASS Unrelated request unchanged
PASS FK enforced
PASS UC02 empty filter result
All integration checks passed
```
## Part 6: AI Skills Pack

### Activity 1: Application Builder

**Prompt:**

Explain how a tutoring request moves from php/request-form.php through PHP, PDO, and SQLite in this project. Identify the responsibility of each file.

**AI Response:**

The tutoring request begins in `php/request-form.php`, where the student enters their information. The form sends the submitted data to `php/process-request.php`, which processes the submission.

The `services/requestService.php` file validates the information using `includes/validation.php` and manages the database transaction. The `config/db.php` file establishes the PDO connection to the SQLite database.

The `repositories/studentPDO.php` and `repositories/requestPDO.php` files execute SQL statements to store student information and tutoring requests. The data is saved in the `students` and `tutoring_requests` tables, which are connected through `student_id`.

**Evaluation:**

The AI correctly identified `services/requestService.php` as the file responsible for validation and database transactions. I verified this by examining the `submitRequest()` function, which calls `validateSubmission()`, starts a transaction, and commits or rolls back the changes depending on whether the operation succeeds.

### Activity 2: Traceability Auditor

**Prompt:**

Trace the requirement “a student can submit a tutoring request” through the PHP files, service, repositories, SQLite tables, and integration tests in this project.

**AI Response:**

The tutoring request begins in `php/request-form.php`, where the student enters their information. The form submission is handled by `php/process-request.php`, which uses `services/requestService.php` to validate and save the request.

The service uses `repositories/studentPDO.php` to find or insert the student and `repositories/requestPDO.php` to insert the tutoring request. The information is stored in the SQLite `students` and `tutoring_requests` tables, connected by `student_id`.

The `tests/integration-tests.php` file verifies that a valid tutoring request can be created successfully.

**Traceability Table:**

| Requirement | Application Page | Service or Validation | Repository or SQL | Database Table | Test Evidence |
|---|---|---|---|---|---|
| Student submits a tutoring request | `php/request-form.php` and `php/process-request.php` | `services/requestService.php` and `includes/validation.php` | `repositories/studentPDO.php` and `repositories/requestPDO.php` | `students` and `tutoring_requests` | `tests/integration-tests.php` — UC01 create and Open (PASS) |

**Evaluation:**

The AI correctly traced the request submission process through the application's different layers. I verified the service and repository responsibilities by examining the PHP code. I also ran the integration tests and confirmed that the UC01 create and Open test passed, demonstrating that a valid tutoring request can be created successfully.

### Activity 3: Test Harness Generator

**Prompt:**

Suggest one useful test case for the Peer Tutoring Hub application. The test must relate to request creation, filtering, editing, cancellation, validation, or database integrity.

**AI Response:**

A useful test case is attempting to cancel a tutoring request that has already been cancelled.

The test would first create a valid tutoring request, cancel it, and then attempt to cancel it a second time. The expected result is that the application rejects the second cancellation and keeps the request status as Cancelled.

**Evaluation:**

1. **Is this test already covered?**

   Yes. The `tests/integration-tests.php` file includes a test called "Second cancellation rejected," which passed during execution.

2. **How would I test it manually or add it to the integration tests?**

   I would create a tutoring request through the application, cancel it, and then attempt to cancel it again. I would verify that the application prevents the second cancellation and that the database still shows the request status as Cancelled.

3. **Is the AI-generated test appropriate?**

   Yes. This test is appropriate because it verifies that cancelled requests cannot be modified through repeated cancellation attempts. It helps ensure that the application follows its business rules and maintains consistent request statuses.

   ### Skills Pack Reflection

Using AI helped me better understand how the different PHP files work together to process and store tutoring requests. It was especially useful for tracing the request submission process and identifying test cases for the application. I verified the AI-generated explanations by reviewing the actual PHP code and running the integration tests. This process showed me why developers should not rely entirely on AI-generated information, since its suggestions may not always match the application's code or requirements. Verifying AI responses helps ensure accuracy and prevents errors during development and testing.

## Part 7: Inspect the Database

**SQL JOIN Explanation:**

The JOIN combines information from the `tutoring_requests` and `students` tables using their shared `student_id` column. This allows the query to display each tutoring request alongside the student's name and email address. Instead of storing student information repeatedly in the requests table, the database connects the records through their relationship.