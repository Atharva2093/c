# Practical 3 — Online Billing System Using Amazon DynamoDB

## 1. Aim

To create an Amazon DynamoDB table and store, view, and manage online billing records containing bill ID, customer name, product, quantity, amount, bill date, and payment status.

## 2. Scope and method

This practical follows the available Cloud Computing Practical Quick Guide:

- AWS service: **Amazon DynamoDB**
- Method: **AWS Management Console (browser interface)**
- AWS Region: **Asia Pacific (Mumbai), `ap-south-1`**
- Table name: **`OnlineBilling`**
- Partition key: **`BillID`**
- Partition-key type: **String**

The documented practical uses DynamoDB Console only. Do not add S3, EC2, CSV generation, a billing website, or application code unless the official question specifically requires them.

## 3. Requirements

- AWS account with permission to create and manage DynamoDB tables and items.
- Web browser and internet connection.
- AWS Management Console access.
- Region set to Mumbai (`ap-south-1`).

## 4. Sample billing data

Use these fictional records for practice.

| BillID | CustomerName | Product | Quantity | Amount | BillDate | PaymentStatus |
|---|---|---|---:|---:|---|---|
| B001 | Rahul Sharma | Keyboard | 1 | 799 | 2026-10-10 | Paid |
| B002 | Priya Patil | Mouse | 2 | 998 | 2026-10-11 | Pending |
| B003 | Aman Verma | USB Drive | 1 | 499 | 2026-10-12 | Paid |

### Attribute types to use

- `BillID`: String — partition key.
- `CustomerName`: String.
- `Product`: String.
- `Quantity`: Number.
- `Amount`: Number.
- `BillDate`: String, using `YYYY-MM-DD` format.
- `PaymentStatus`: String.

**Important:** `Quantity` and `Amount` must be stored as **Number** attributes, as required by the practical guide. The remaining attributes listed above are Strings. Each `BillID` must be unique because it is the table's only primary-key attribute.

The dates and amounts above are sample data for the practical; they are not live transactions or current prices.

## 5. Step-by-step procedure in AWS Console

### Step 1 — Sign in and select the correct region

1. Open the AWS Management Console.
2. Sign in with an authorized AWS account.
3. In the Region selector, choose **Asia Pacific (Mumbai) — `ap-south-1`**.
4. Confirm the selected region before creating the table. DynamoDB tables are regional.

### Step 2 — Open DynamoDB

1. Use the AWS Console search bar.
2. Search for **DynamoDB**.
3. Open **Amazon DynamoDB**.
4. Select **Tables** in the navigation panel if needed.

### Step 3 — Create the `OnlineBilling` table

1. Choose **Create table**.
2. In **Table name**, enter exactly:

   `OnlineBilling`

3. In **Partition key**, enter exactly:

   `BillID`

4. Set the partition-key type to **String**.
5. Do not add a sort key; this practical only specifies a partition key.
6. Keep the default table settings unless your instructor says otherwise.
7. Check the spelling of the table name and key.
8. Choose **Create table**.

### Step 4 — Wait for Active status

1. Return to the Tables list if necessary.
2. Find `OnlineBilling`.
3. Wait until its status shows **Active**.
4. Refresh the table list if necessary.

### Step 5 — Open the item explorer

1. Click the `OnlineBilling` table.
2. Open **Explore table items** or **Explore items** (the exact wording can vary with the console interface).
3. Choose **Create item**.

### Step 6 — Add bill B001

Create the following attributes:

| Attribute name | Type | Value |
|---|---|---|
| `BillID` | String | `B001` |
| `CustomerName` | String | `Rahul Sharma` |
| `Product` | String | `Keyboard` |
| `Quantity` | Number | `1` |
| `Amount` | Number | `799` |
| `BillDate` | String | `2026-10-10` |
| `PaymentStatus` | String | `Paid` |

1. Add `BillID` first as the required partition-key attribute.
2. Add each remaining attribute with the exact name and type shown above.
3. For `Quantity`, choose **Number**, then enter `1`.
4. For `Amount`, choose **Number**, then enter `799`.
5. Do not enter quotation marks around numeric values.
6. Review the fields, then choose **Create item** or **Save changes**, depending on the current editor.
7. Confirm that bill `B001` appears in the items list.

Equivalent DynamoDB low-level item representation:

```json
{
  "BillID": {"S": "B001"},
  "CustomerName": {"S": "Rahul Sharma"},
  "Product": {"S": "Keyboard"},
  "Quantity": {"N": "1"},
  "Amount": {"N": "799"},
  "BillDate": {"S": "2026-10-10"},
  "PaymentStatus": {"S": "Paid"}
}
```

This JSON uses DynamoDB's low-level typed-attribute format. It is a reference, not necessarily the format expected by the Console's form editor.

### Step 7 — Add bill B002

1. In **Explore table items**, choose **Create item**.
2. Add these attributes:

| Attribute name | Type | Value |
|---|---|---|
| `BillID` | String | `B002` |
| `CustomerName` | String | `Priya Patil` |
| `Product` | String | `Mouse` |
| `Quantity` | Number | `2` |
| `Amount` | Number | `998` |
| `BillDate` | String | `2026-10-11` |
| `PaymentStatus` | String | `Pending` |

3. Make sure `Quantity` and `Amount` are both set to **Number**.
4. Save/create the item.
5. Confirm that `B002` appears in the items list.

Equivalent DynamoDB low-level item:

```json
{
  "BillID": {"S": "B002"},
  "CustomerName": {"S": "Priya Patil"},
  "Product": {"S": "Mouse"},
  "Quantity": {"N": "2"},
  "Amount": {"N": "998"},
  "BillDate": {"S": "2026-10-11"},
  "PaymentStatus": {"S": "Pending"}
}
```

### Step 8 — Add bill B003

1. Choose **Create item** again.
2. Add these attributes:

| Attribute name | Type | Value |
|---|---|---|
| `BillID` | String | `B003` |
| `CustomerName` | String | `Aman Verma` |
| `Product` | String | `USB Drive` |
| `Quantity` | Number | `1` |
| `Amount` | Number | `499` |
| `BillDate` | String | `2026-10-12` |
| `PaymentStatus` | String | `Paid` |

3. Save/create the item.
4. Confirm that `B003` appears in the items list.

Equivalent DynamoDB low-level item:

```json
{
  "BillID": {"S": "B003"},
  "CustomerName": {"S": "Aman Verma"},
  "Product": {"S": "USB Drive"},
  "Quantity": {"N": "1"},
  "Amount": {"N": "499"},
  "BillDate": {"S": "2026-10-12"},
  "PaymentStatus": {"S": "Paid"}
}
```

### Step 9 — Verify all bills

1. Return to the `OnlineBilling` table's item explorer.
2. Refresh the items view if needed.
3. Confirm that `B001`, `B002`, and `B003` are visible.
4. Open each item to check its attributes.
5. Verify that `Quantity` and `Amount` are Number attributes and that the other fields are present.
6. Verify that the payment status shows `Paid` or `Pending` as entered.

## 6. Expected output

The expected result is:

- Table name: `OnlineBilling`
- Status: `Active`
- Partition key: `BillID` (String)
- Three bill records: `B001`, `B002`, and `B003`
- Every bill has customer name, product, quantity, amount, bill date, and payment status
- `Quantity` and `Amount` use Number type

The AWS Console layout and displayed metadata may vary. Verify the table name, key, item IDs, attributes, and attribute types.

## 7. Screenshots to capture

Capture real screenshots from your own AWS Console:

1. **Table list:** `OnlineBilling` showing `Active` status.
2. **Table key:** Details showing `BillID` as the String partition key.
3. **Items list:** Showing `B001`, `B002`, and `B003`.
4. **Bill details:** An opened item showing all fields.
5. **Number types:** If the interface makes types visible, capture `Quantity` and `Amount` as Number attributes.

If a practical-record PDF is required, include the title, aim, requirements, procedure, actual screenshots with captions, expected/observed output, result, and viva questions. Do not fabricate screenshots or results.

## 8. Record-book write-up

### Aim
To create an `OnlineBilling` table in Amazon DynamoDB and store online billing records using `BillID` as the partition key.

### Requirements
AWS account, internet connection, web browser, AWS Management Console, and permission to create DynamoDB tables and items.

### Procedure
1. Select Mumbai region (`ap-south-1`).
2. Open DynamoDB and create `OnlineBilling`.
3. Set `BillID` as a String partition key; do not add a sort key.
4. Wait for table status `Active`.
5. Add the three sample bill items with all required attributes.
6. Store `Quantity` and `Amount` as Number; store the other attributes as Strings.
7. View the items and verify their values and types.
8. Capture screenshots.

### Output
The `OnlineBilling` table was created and displayed three billing records with bill ID, customer name, product, quantity, amount, bill date, and payment status.

### Result
Billing records were successfully stored and viewed using Amazon DynamoDB.

## 9. Troubleshooting

### `OnlineBilling` is not visible
- Confirm the AWS account/role.
- Confirm the region is Mumbai (`ap-south-1`).
- Refresh the Tables list.

### Table creation is denied
- Your AWS identity may lack the required permissions. Use an authorized lab account or ask the account administrator.

### The table is still creating
- Wait and refresh until the table status becomes `Active`.

### A required key error appears
- Each item must contain `BillID`, with exact spelling and String type.

### Duplicate key error
- `BillID` must be unique for each item. Use a new ID such as `B004` for a new bill, or open the existing bill if you intended to edit it.

### Quantity or Amount is stored as text
- Edit the item and ensure `Quantity` and `Amount` are Number attributes, not String attributes.

### A record is missing from the list
- Confirm you saved it successfully.
- Refresh the item explorer.
- Check the correct table, region, and bill ID.

## 10. Important concepts

- **DynamoDB:** AWS managed NoSQL database service.
- **Table:** Collection of records.
- **Item:** One bill record.
- **Attribute:** A field in an item.
- **Partition key:** Attribute that identifies an item in this table; here it is `BillID`.
- **String (`S`):** Stores text.
- **Number (`N`):** Stores numeric values. DynamoDB's low-level JSON format represents numbers as strings inside the `N` field.
- **Region:** AWS location in which the table exists.

## 11. Viva questions and answers

**Q1. What is Amazon DynamoDB?**  
A managed NoSQL database service provided by AWS.

**Q2. What is the table name in this practical?**  
`OnlineBilling`.

**Q3. What is the partition key?**  
`BillID`, with type String.

**Q4. What is an item?**  
One record in the table, representing one bill.

**Q5. What is an attribute?**  
A named field in an item, such as `Product` or `Amount`.

**Q6. Why must each BillID be unique?**  
It is the table's only primary-key attribute, so every item must have a distinct value.

**Q7. Which fields are Number attributes?**  
`Quantity` and `Amount`.

**Q8. Why is Amount stored as a Number?**  
It represents a numeric monetary amount and is intended to be treated as a number rather than text.

**Q9. Why is BillDate stored as a String here?**  
The practical uses a simple date representation such as `2026-10-10`; the documented schema specifies BillDate as a text/date field, not a DynamoDB date type.

**Q10. What values can PaymentStatus contain in the sample?**  
`Paid` and `Pending`.

**Q11. What does Active status mean?**  
The table is ready for use.

**Q12. What is the difference between a table and an item?**  
A table contains many items; each item represents one bill.

**Q13. Do we need S3 or EC2 for this practical?**  
Not for the documented Console-only version.

**Q14. Which region is used?**  
Mumbai, `ap-south-1`.

**Q15. How do you verify that the practical succeeded?**  
Check that the table is Active and that all three bill items and their attributes are visible.

## 12. Final checklist

- [ ] Selected Mumbai region (`ap-south-1`).
- [ ] Created table `OnlineBilling`.
- [ ] Set `BillID` as String partition key.
- [ ] Waited until status became Active.
- [ ] Added bill `B001`.
- [ ] Added bill `B002`.
- [ ] Added bill `B003`.
- [ ] Set `Quantity` and `Amount` to Number.
- [ ] Verified all other required attributes.
- [ ] Captured real screenshots.
- [ ] Practised the viva questions.
