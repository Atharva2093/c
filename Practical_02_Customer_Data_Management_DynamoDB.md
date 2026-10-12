# Practical 2 — Customer Data Management Using Amazon DynamoDB

## 1. Aim

To create a table in Amazon DynamoDB and store, view, and manage customer information such as customer ID, name, email, phone number, and city.

## 2. Scope and method

This practical follows the available Cloud Computing Practical Quick Guide:

- AWS service: **Amazon DynamoDB**
- Method: **AWS Management Console (browser interface)**
- AWS Region: **Asia Pacific (Mumbai), `ap-south-1`**
- Table name: **`CustomerData`**
- Partition key: **`CustomerID`**
- Partition-key data type: **String**

This is a DynamoDB Console practical. Do not add EC2, S3, a web application, or programming code unless the official question specifically asks for them.

## 3. Requirements

- An AWS account with permission to open DynamoDB and create/manage tables.
- A web browser and internet connection.
- Access to the AWS Management Console.
- Region set to **Asia Pacific (Mumbai) — `ap-south-1`**.

### Before starting

1. Sign in to the AWS Management Console.
2. Check the account/role shown in the upper-right corner. Use only an account you are authorized to use.
3. In the AWS Region selector, select **Asia Pacific (Mumbai) `ap-south-1`**.
4. Confirm the region before creating resources. DynamoDB tables are regional; a table created in another region will not appear in Mumbai.

## 4. Sample data to enter

Use these example records. They are practice data, not real customer records.

| CustomerID | CustomerName | Email | Phone | City |
|---|---|---|---|---|
| C001 | Rahul Sharma | rahul.sharma@example.com | 9876500001 | Nashik |
| C002 | Priya Patil | priya.patil@example.com | 9876500002 | Pune |
| C003 | Aman Verma | aman.verma@example.com | 9876500003 | Mumbai |

### Attribute types

- `CustomerID`: String — partition key.
- `CustomerName`: String.
- `Email`: String.
- `Phone`: String.
- `City`: String.

**Why store Phone as a String?** A phone number is an identifier, not a quantity used in arithmetic. Storing it as a String avoids numeric interpretation and can preserve leading zeroes in formats that use them.

**Important:** Each `CustomerID` must be unique because it is the table's partition key. Use `C001`, `C002`, and `C003` for the three separate items.

## 5. Step-by-step procedure in AWS Console

### Step 1 — Open DynamoDB

1. Open the AWS Management Console.
2. Use the top search bar and type **DynamoDB**.
3. Select **Amazon DynamoDB** from the results.
4. Confirm the region in the top-right is **Asia Pacific (Mumbai) `ap-south-1`**.

### Step 2 — Start table creation

1. In DynamoDB's navigation panel, open **Tables** if it is not already selected.
2. Choose **Create table**.
3. In **Table name**, enter exactly:

   `CustomerData`

4. In **Partition key**, enter exactly:

   `CustomerID`

5. For the partition-key type, select **String**.
6. Do not add a sort key; this practical only requires the partition key.
7. For table settings, keep the default settings unless your college instructions require something different. For a simple lab, default settings are sufficient.
8. Review the table name and key carefully.
9. Choose **Create table**.

### Step 3 — Wait until the table is active

1. Return to the Tables list if needed.
2. Find `CustomerData`.
3. Wait for its status to show **Active**. Refresh the list if necessary.
4. Do not try to add records before the table is ready.

### Step 4 — Open the items view

1. Click the `CustomerData` table name.
2. Find the table's **Explore table items** or **Explore items** view (the wording may vary slightly with the current AWS Console).
3. Open that view.
4. Choose **Create item**.

### Step 5 — Add the first customer (C001)

1. In the item editor, create the partition-key attribute:
   - Attribute name: `CustomerID`
   - Type: `String`
   - Value: `C001`
2. Add the next attribute:
   - Attribute name: `CustomerName`
   - Type: `String`
   - Value: `Rahul Sharma`
3. Add:
   - Attribute name: `Email`
   - Type: `String`
   - Value: `rahul.sharma@example.com`
4. Add:
   - Attribute name: `Phone`
   - Type: `String`
   - Value: `9876500001`
5. Add:
   - Attribute name: `City`
   - Type: `String`
   - Value: `Nashik`
6. Check that the attribute names and values are spelled correctly.
7. Choose **Create item** or **Save changes**, depending on the editor's button label.
8. Verify that the item appears in the items list.

**Console note:** DynamoDB's item editor can display attributes in a form or JSON view depending on the console version. If you use JSON view, the equivalent item is:

```json
{
  "CustomerID": {"S": "C001"},
  "CustomerName": {"S": "Rahul Sharma"},
  "Email": {"S": "rahul.sharma@example.com"},
  "Phone": {"S": "9876500001"},
  "City": {"S": "Nashik"}
}
```

This JSON is a reference for the AWS DynamoDB low-level attribute format; do not paste it into a form that expects ordinary JSON unless the editor explicitly supports that format.

### Step 6 — Add the second customer (C002)

1. From **Explore table items**, choose **Create item**.
2. Add the following attributes as Strings:

   - `CustomerID`: `C002`
   - `CustomerName`: `Priya Patil`
   - `Email`: `priya.patil@example.com`
   - `Phone`: `9876500002`
   - `City`: `Pune`

3. Save/create the item.
4. Confirm that `C002` appears in the items list.

Equivalent low-level DynamoDB item:

```json
{
  "CustomerID": {"S": "C002"},
  "CustomerName": {"S": "Priya Patil"},
  "Email": {"S": "priya.patil@example.com"},
  "Phone": {"S": "9876500002"},
  "City": {"S": "Pune"}
}
```

### Step 7 — Add the third customer (C003)

1. Choose **Create item** again.
2. Add the following attributes as Strings:

   - `CustomerID`: `C003`
   - `CustomerName`: `Aman Verma`
   - `Email`: `aman.verma@example.com`
   - `Phone`: `9876500003`
   - `City`: `Mumbai`

3. Save/create the item.
4. Confirm that `C003` appears in the items list.

Equivalent low-level DynamoDB item:

```json
{
  "CustomerID": {"S": "C003"},
  "CustomerName": {"S": "Aman Verma"},
  "Email": {"S": "aman.verma@example.com"},
  "Phone": {"S": "9876500003"},
  "City": {"S": "Mumbai"}
}
```

### Step 8 — View and verify all customer items

1. Return to **Explore table items** for `CustomerData`.
2. Refresh the results if the newly created records do not appear immediately.
3. Confirm that all three partition-key values are present: `C001`, `C002`, and `C003`.
4. Open an item to inspect its attributes if required.
5. Confirm that every item has `CustomerID`, `CustomerName`, `Email`, `Phone`, and `City`.

### Step 9 — Optional: demonstrate updating a customer

Only do this if the teacher asks you to show that records can be managed/edited.

1. Open the item with `CustomerID = C002`.
2. Choose **Edit item**.
3. Change the `City` value from `Pune` to another sample value, such as `Nashik`.
4. Save changes.
5. Reopen or refresh the item and verify the updated city.

Do not change the partition key to an ID that is already used by another item. Changing the key is not a normal edit of the same item; it identifies a different item.

## 6. Expected output

The table should show:

- Table name: `CustomerData`
- Table status: `Active`
- Partition key: `CustomerID` (String)
- Three items with IDs `C001`, `C002`, and `C003`
- Each item contains customer name, email, phone, and city

The actual screen layout can change with AWS Console updates. The table name, key, item IDs, and data are the important checks.

## 7. Screenshots/evidence for the practical record

Capture your own screenshots from your AWS account:

1. **Region and table:** DynamoDB Tables page showing `CustomerData` and `Active` status. If the region is not visible in the screenshot, capture enough of the console to identify the selected region.
2. **Key schema:** Table details showing `CustomerID` as the partition key and its type as String.
3. **Items list:** `Explore table items` showing `C001`, `C002`, and `C003`.
4. **Item details:** Open one item and show its `CustomerName`, `Email`, `Phone`, and `City` attributes.
5. **Optional update:** If you perform the update, capture the changed value after saving.

Do not use fabricated screenshots or claim that records exist if you have not created them.

## 8. Record-book write-up

### Aim
To create a DynamoDB table named `CustomerData` and store customer details using `CustomerID` as the partition key.

### Requirements
AWS account, browser, internet access, AWS Management Console, and permission to create DynamoDB tables and items.

### Procedure
1. Open the AWS Management Console and select Mumbai region (`ap-south-1`).
2. Open DynamoDB and create table `CustomerData`.
3. Set `CustomerID` as the String partition key; do not add a sort key.
4. Wait for table status `Active`.
5. Open the table's item explorer and create the three sample customer items.
6. Verify all records and attributes in the items view.
7. Capture screenshots of the table and records.

### Output
The `CustomerData` table was created in Active status and displayed three customer records with their IDs, names, emails, phone numbers, and cities.

### Result
Customer information was successfully stored and viewed using Amazon DynamoDB.

## 9. Troubleshooting

### `CustomerData` is not visible
- Confirm that the console is set to `ap-south-1` (Mumbai).
- Confirm that you are signed into the correct AWS account/role.
- Check the Tables list and refresh it.

### Table creation fails with a permission error
- The AWS identity may not have permission to create DynamoDB tables. Use an authorized college/lab account or ask the account administrator; do not try to bypass permissions.

### The table is still creating
- Wait and refresh the table list until the status becomes `Active`.

### The item editor says the key is missing
- The partition-key attribute must be named exactly `CustomerID` and have type String.
- Every item must contain a `CustomerID` value.

### You receive an error about a duplicate key
- The partition-key value is already used. Use a unique ID such as `C004` for another customer, or open the existing item if you intended to edit it.

### The item appears to be missing
- Check that you are viewing `CustomerData` in the correct region.
- Refresh the item explorer and check the ID spelling.
- Confirm that the save/create operation completed successfully.

## 10. Important DynamoDB concepts

- **Table:** A collection of items.
- **Item:** One record, such as one customer.
- **Attribute:** A field inside an item, such as `Email` or `City`.
- **Partition key:** The attribute DynamoDB uses to distribute and identify items. In this practical, it is `CustomerID`.
- **String type:** Text data. DynamoDB uses a String type for names, emails, phone identifiers, cities, and the customer ID.
- **Region:** The AWS location where this table is created. Tables in different regions are separate resources.

## 11. Viva questions and answers

**Q1. What is Amazon DynamoDB?**  
Amazon DynamoDB is a managed NoSQL database service from AWS.

**Q2. What is a NoSQL database?**  
A database model that does not require all data to be stored in traditional relational tables with a fixed relational schema. DynamoDB stores items with attributes.

**Q3. What is a DynamoDB table?**  
A collection of items.

**Q4. What is an item?**  
A single record in a table, such as one customer's details.

**Q5. What is an attribute?**  
A named data field in an item, such as `CustomerName` or `City`.

**Q6. What is the partition key in this practical?**  
`CustomerID`, with data type String.

**Q7. Why must each `CustomerID` be unique?**  
Because it is the table's only primary-key attribute, so each item must have a distinct partition-key value.

**Q8. Why is `CustomerID` a String?**  
It is an identifier and may contain letters, such as `C001`.

**Q9. Why store a phone number as a String?**  
A phone number is an identifier, not a value used for arithmetic, and a String can preserve formatting and leading zeroes.

**Q10. What is an AWS Region?**  
A geographic AWS location containing services and resources. This practical uses Mumbai (`ap-south-1`).

**Q11. What does Active status mean?**  
The table is ready for normal operations.

**Q12. Can two items have the same partition-key value in this table?**  
No. With only a partition key, each item must have a unique key value.

**Q13. What is the difference between a table and an item?**  
The table is the collection; an item is one record inside it.

**Q14. Do we need EC2 for this practical?**  
No. The documented method for this practical uses the DynamoDB web console only.

**Q15. Do we need S3 for this practical?**  
No. S3 is not required by the documented steps for Customer Data Management.

**Q16. What is the purpose of the `Email` and `City` attributes?**  
They store additional customer details associated with each customer ID.

**Q17. How do you verify that the practical worked?**  
Confirm that `CustomerData` is Active and the three expected items are visible with their attributes.

**Q18. What should you check if the table is missing?**  
Check the selected AWS account/role and region first.

## 12. Final checklist

- [ ] Region is Mumbai (`ap-south-1`).
- [ ] Table name is exactly `CustomerData`.
- [ ] Partition key is exactly `CustomerID`.
- [ ] Partition key type is String.
- [ ] Table status is Active.
- [ ] Item `C001` is saved.
- [ ] Item `C002` is saved.
- [ ] Item `C003` is saved.
- [ ] Each item has `CustomerName`, `Email`, `Phone`, and `City`.
- [ ] Screenshots show the actual table and records.
- [ ] I can explain table, item, attribute, partition key, and region.
