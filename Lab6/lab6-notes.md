
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
