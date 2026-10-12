# Practical 3 — Employee Attendance System Using DynamoDB

## 1. Aim

To create an employee attendance table in Amazon DynamoDB and store and retrieve employee attendance records through the AWS Management Console.

## 2. Practical scope

The college practical list states: **Employee Attendance System** — a company wants to maintain employee attendance records on the cloud so employees and managers can access the records easily.

This procedure focuses on the DynamoDB table and records. It does not add EC2, S3, a web application, or programming because those are not specified in this practical statement.

Use the exact table name, key, attributes, and sample values if your teacher provides them. The names and data below are a clear example to practise the required operations.

## 3. AWS service used

**Amazon DynamoDB** is a managed NoSQL database service. It stores records as items with attributes and allows an item to be identified by its primary key.

## 4. Requirements

- AWS account with permission to use DynamoDB.
- Internet browser and access to the AWS Management Console.
- AWS Region: Mumbai (`ap-south-1`), unless your college specifies another region.
- Permission to create and view DynamoDB tables and items.

## 5. Example table design

Create a table named `EmployeeAttendance` with partition key `AttendanceID` of type **String**.

| Attribute | Example | DynamoDB type | Purpose |
|---|---|---|---|
| `AttendanceID` | `A001` | String (partition key) | Unique ID for this attendance record |
| `EmployeeID` | `E101` | String | Employee identifier |
| `EmployeeName` | `Rahul Sharma` | String | Employee name |
| `Date` | `2026-10-13` | String | Attendance date in ISO format |
| `CheckIn` | `09:00` | String | Check-in time |
| `CheckOut` | `17:00` | String | Check-out time |
| `Status` | `Present` | String | Attendance status |

For this beginner practical, the date and time values are stored as strings. This makes them easy to enter in the DynamoDB Console. If your official question specifies a different key structure, follow that question.

## 6. Step 1 — Open DynamoDB

1. Sign in to the AWS Management Console.
2. Select the Mumbai region (`ap-south-1`) from the region selector unless your lab specifies a different region.
3. Search for **DynamoDB** and open the service.
4. Select **Tables** from the navigation menu.

## 7. Step 2 — Create the attendance table

1. Choose **Create table**.
2. For **Table name**, enter `EmployeeAttendance`.
3. For **Partition key**, enter `AttendanceID`.
4. Select **String** as the partition-key type.
5. Do not add a sort key unless your official question requires one.
6. Keep the default settings unless your teacher specifies otherwise.
7. Choose **Create table**.
8. Wait for the table status to become **Active**.

If a table with the required name already exists, check its partition key and region rather than creating a duplicate. Do not delete an existing table just to repeat this step.

## 8. Step 3 — Add an attendance record

1. Open the `EmployeeAttendance` table.
2. Choose **Explore table items**.
3. Choose **Create item**.
4. Add the attributes below. In the console's item editor, use the appropriate String type for each field.

| Attribute name | Value |
|---|---|
| `AttendanceID` | `A001` |
| `EmployeeID` | `E101` |
| `EmployeeName` | `Rahul Sharma` |
| `Date` | `2026-10-13` |
| `CheckIn` | `09:00` |
| `CheckOut` | `17:00` |
| `Status` | `Present` |

5. Confirm `AttendanceID` is included and is exactly `A001`.
6. Choose **Create item** or **Save**, depending on the console version.

Expected: the console returns to the items view and the record is available in the table.

## 9. Step 4 — Add another record

To demonstrate that the table can hold multiple attendance records, create a second item:

| Attribute name | Value |
|---|---|
| `AttendanceID` | `A002` |
| `EmployeeID` | `E102` |
| `EmployeeName` | `Priya Patil` |
| `Date` | `2026-10-13` |
| `CheckIn` | `09:15` |
| `CheckOut` | `17:05` |
| `Status` | `Present` |

Use **Create item** again. The partition key `AttendanceID` must be unique for each item in this example.

## 10. Step 5 — View and retrieve attendance records

1. Open **Explore table items** for `EmployeeAttendance`.
2. Refresh the item view if necessary.
3. Confirm that records `A001` and `A002` are present.
4. Open each item to view the employee ID, name, date, check-in, check-out, and status.

If your console offers a filter field, you may filter by `AttendanceID` or `EmployeeID` to make a record easier to locate. The exact filter interface may vary by console version.

## 11. Optional: add a record using the DynamoDB low-level JSON format

Only use this section if your instructor asks you to enter an item in DynamoDB's JSON editor or use AWS CLI. The Console form above is sufficient for the basic console practical.

Example item representation:

```json
{
  "AttendanceID": {"S": "A001"},
  "EmployeeID": {"S": "E101"},
  "EmployeeName": {"S": "Rahul Sharma"},
  "Date": {"S": "2026-10-13"},
  "CheckIn": {"S": "09:00"},
  "CheckOut": {"S": "17:00"},
  "Status": {"S": "Present"}
}
```

In this representation, `S` means String.

## 12. Expected output

The practical is successful when:

- The `EmployeeAttendance` table exists and has status **Active**.
- The table uses `AttendanceID` as its String partition key.
- The items `A001` and `A002` appear in the items view.
- Each item shows the expected employee and attendance details.

## 13. Screenshots/evidence

Capture screenshots from your own AWS Console:

1. DynamoDB Tables page showing `EmployeeAttendance`.
2. Table details showing the partition key `AttendanceID` and table status **Active**.
3. Item editor or saved item showing record `A001`.
4. Items view showing both `A001` and `A002`.
5. One opened item showing the attendance attributes.

Avoid exposing account-sensitive information in screenshots shared publicly.

## 14. Record-book write-up

### Aim
To maintain employee attendance records using Amazon DynamoDB.

### Requirements
AWS account, browser, AWS Management Console, and permission to create and manage DynamoDB tables.

### Procedure
1. Open DynamoDB in the required AWS region.
2. Create the `EmployeeAttendance` table with `AttendanceID` as a String partition key.
3. Add the first attendance item with employee ID, name, date, check-in, check-out, and status.
4. Add a second item using a different attendance ID.
5. View both records in the table's items view.
6. Confirm that the attributes and values were saved correctly.

### Output
Two employee attendance records were stored and displayed in DynamoDB.

### Result
Employee attendance records were created and viewed successfully using Amazon DynamoDB.

## 15. Troubleshooting

### The table name is already in use in your account
Open the existing table and check its region, partition key, and data. Do not delete it if it contains records you need. Use the table already created if it meets the practical requirements.

### `AttendanceID` is missing or has the wrong type
The partition key must match the table schema exactly. For this example, use `AttendanceID` with type String.

### The second item replaces the first or cannot be saved
Make sure each record has a different `AttendanceID`. In this example, use `A001` and `A002`.

### The table does not appear
Check the selected AWS region and refresh the Tables page. DynamoDB tables are regional resources.

### You cannot create a table or item
Your AWS account may not have the required permissions. Ask your instructor or account administrator to grant the appropriate DynamoDB access. Do not attempt to bypass access controls.

### The date or time is not shown as a special date/time type
The sample design intentionally stores `Date`, `CheckIn`, and `CheckOut` as String attributes. DynamoDB supports typed attributes, and strings are suitable for this simple demonstration.

## 16. Cost and security precautions

- Use your college AWS account and follow its lab rules.
- Do not create duplicate tables unnecessarily.
- Do not enter real employee personal information in a practice account.
- Do not share AWS credentials or screenshots containing secrets.
- If the table is only for practice, follow your instructor's cleanup instructions before deleting it. Do not delete records needed for later practicals.
- Check the AWS billing dashboard and resource usage if your account is subject to charges.

## 17. Viva questions and answers

**Q1. What is employee attendance management?**  
It is the process of recording when employees attend work, including dates and check-in/check-out details.

**Q2. What is Amazon DynamoDB?**  
A managed NoSQL database service offered by AWS.

**Q3. What is a DynamoDB table?**  
A collection of items that share a table schema for key attributes.

**Q4. What is an item?**  
A single record in a DynamoDB table.

**Q5. What is an attribute?**  
A named piece of data in an item, such as `EmployeeID` or `Status`.

**Q6. What is the partition key in this example?**  
`AttendanceID`, of type String.

**Q7. Why does each item need a different `AttendanceID` here?**  
It is the primary key used to identify each attendance record.

**Q8. Why is `EmployeeID` also stored?**  
It identifies the employee associated with the attendance record; the example's primary key is the attendance record ID.

**Q9. What is the purpose of the `Status` attribute?**  
It records a value such as Present or Absent for the attendance entry.

**Q10. Can DynamoDB store multiple records for one employee?**  
Yes. The table design and keys determine how those records are uniquely identified.

**Q11. Why are date and time stored as strings in this example?**  
They are easy to enter in the console and follow a consistent text format for this simple practical.

**Q12. What does Active mean for a table?**  
The table is available for normal operations.

**Q13. Does this practical create a web application?**  
No. The stated practical is a DynamoDB attendance-record exercise; it does not specify a web application.

**Q14. Why must the region be correct?**  
DynamoDB tables are regional, so a table may not appear if the console is set to a different region.

**Q15. How do you confirm the practical worked?**  
The table is Active and both sample items are visible with the expected attributes.

## 18. Final checklist

- [ ] Read the official question and confirmed any required names or fields.
- [ ] Selected the intended AWS region.
- [ ] Created or verified `EmployeeAttendance`.
- [ ] Used `AttendanceID` as the String partition key in this example.
- [ ] Added item `A001`.
- [ ] Added item `A002` with a different key.
- [ ] Viewed both records in DynamoDB.
- [ ] Captured screenshots of the table and items.
- [ ] Reviewed the viva questions.
- [ ] Followed the lab's AWS security and cleanup rules.
