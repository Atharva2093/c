# Practical 7 — Library Management Using EC2, S3, and DynamoDB

## 1. Aim

To demonstrate a simple library management workflow using Amazon EC2, Amazon S3, and Amazon DynamoDB: store book details in DynamoDB, upload a book-related file or image to S3, and record the S3 object location in the database.

## 2. Match to the college practical statement

The college list describes a library that stores book details and issues records in DynamoDB, stores book-related files/images in S3, and uses EC2 to run a simple library management application.

This guide demonstrates the core cloud-service operations through AWS CLI commands run from EC2. It does not create a full web application because the provided statement does not specify a programming language or framework. If your instructor requires an actual application or Boto3 code, follow that additional requirement.

Use the exact names and fields from the official question if they differ from this example.

## 3. AWS services used

- **Amazon EC2:** Virtual server for running AWS CLI commands.
- **Amazon DynamoDB:** Stores structured book records.
- **Amazon S3:** Stores book-related documents, cover images, or sample files.
- **IAM role:** Gives EC2 permissions to access the required resources without hardcoded long-term access keys.
- **AWS CLI:** Command-line tool used to perform the operations.

## 4. Example design

### DynamoDB table

Table name: `LibraryBooks`

Partition key: `BookID` (String)

| Attribute | Example | Type |
|---|---|---|
| `BookID` | `B102` | String (partition key) |
| `Title` | `Python Programming` | String |
| `Author` | `John Smith` | String |
| `Category` | `Computer Science` | String |
| `Status` | `Available` | String |
| `IssuedTo` | `None` | String |
| `DocumentPath` | `s3://YOUR-BUCKET-NAME/B102-book.txt` | String |

`DocumentPath` stores the S3 URI as text. The file itself remains in S3.

## 5. Requirements

- AWS account with permissions for EC2, IAM, S3, and DynamoDB.
- AWS Region: Mumbai (`ap-south-1`), unless the college specifies another.
- An Amazon Linux EC2 instance.
- An IAM role attached to EC2 with permissions for the required table and bucket.
- DynamoDB table `LibraryBooks` with `BookID` as a String partition key.
- S3 bucket with a globally unique name.
- An authorized way to connect to EC2.

## 6. Step 1 — Create or verify the DynamoDB table

1. Sign in to the AWS Management Console.
2. Select Mumbai (`ap-south-1`) unless your instructor specifies another region.
3. Open **DynamoDB** → **Tables** → **Create table**.
4. Enter `LibraryBooks` as the table name.
5. Enter `BookID` as the partition key and select **String**.
6. Do not add a sort key unless the official question requires one.
7. Keep the default settings unless your lab specifies otherwise.
8. Choose **Create table** and wait for status **Active**.

If the table already exists, verify its name, region, and key rather than creating a duplicate.

## 7. Step 2 — Create or identify an S3 bucket

1. Open **S3** in the AWS Console.
2. Create a bucket if your college has not already provided one.
3. Choose a globally unique name, for example a name patterned like `library-practical-<unique-suffix>`. Replace the placeholder with a unique suffix that follows S3 naming rules; the example is not guaranteed to be available.
4. Use the region required by your lab; this guide uses `ap-south-1`.
5. Keep **Block all public access** enabled.
6. Create the bucket and note its exact name.

If the lab has provided a bucket, use it instead of creating another one. Do not make book files public just to demonstrate upload.

## 8. Step 3 — Create or verify the EC2 IAM role

The EC2 instance needs permission to use DynamoDB and S3.

1. Open **IAM** in the AWS Console.
2. Choose **Roles** → **Create role**.
3. Choose **AWS service** and **EC2** as the use case.
4. Add permissions needed for the intended DynamoDB table and S3 bucket. Use least privilege and restrict policies to the relevant resources.
5. Name the role, for example `LibraryEC2Role`.
6. Create the role.
7. Open **EC2** → **Instances**.
8. Select the instance, choose **Actions** → **Security** → **Modify IAM role** (the exact label may vary).
9. Attach `LibraryEC2Role` and save.

### Permission guidance

The role may need:
- `dynamodb:PutItem` and `dynamodb:GetItem` on the `LibraryBooks` table.
- `s3:PutObject` on the intended bucket's object ARN.
- `s3:ListBucket` on the bucket ARN for listing objects.
- `s3:GetObject` only if downloading/verifying the uploaded object.
- `dynamodb:DescribeTable` only if you run a command that needs it.

If you cannot create roles or policies, ask the lab administrator to attach an approved role. Do not grant unrestricted administrator access just to avoid permission errors. Do not place long-term AWS access keys in scripts.

## 9. Step 4 — Launch or connect to EC2

Use an existing suitable lab instance if available. Otherwise:

1. Open **EC2** in the AWS Console and select the required region.
2. Choose **Launch instances**.
3. Enter a name such as `Library-Management-Practical`.
4. Select an Amazon Linux AMI approved by your lab.
5. Select an instance type allowed by the lab.
6. Configure a key pair only if your chosen connection method requires it.
7. Do not open unnecessary inbound ports. If using SSH, restrict port 22 to your own public IP. Alternatively, use a supported, authorized EC2 Instance Connect setup.
8. Attach `LibraryEC2Role`.
9. Launch and wait until the instance is Running and status checks pass.
10. Select the instance → **Connect** and use an authorized connection method.

Run the commands below in the **EC2 terminal**.

## 10. Step 5 — Verify AWS CLI and identity

Run:

```bash
aws --version
aws sts get-caller-identity
```

Expected:
- The first command prints the AWS CLI version.
- The second prints JSON containing the account and current identity. With an EC2 role, the ARN generally refers to an assumed role.

Optional:

```bash
aws configure get region
```

If no default region is configured, that is not necessarily an error; the commands below explicitly use `--region ap-south-1`.

If AWS CLI is missing, follow your lab's approved installation procedure for the Amazon Linux AMI. Do not assume Ubuntu package commands apply to Amazon Linux.

## 11. Step 6 — Create a sample book-related file

Create a text file to represent a book record or related document. If your teacher specifically asks for an image, use an authorized sample image file and replace the filename in the upload command.

```bash
cat > B102-book.txt <<'EOF'
Book ID: B102
Title: Python Programming
Author: John Smith
Category: Computer Science
EOF
```

Verify the contents:

```bash
cat B102-book.txt
```

Expected output:

```text
Book ID: B102
Title: Python Programming
Author: John Smith
Category: Computer Science
```

This text file is a simple substitute for a book-related file in the demonstration. It is not an actual cover image.

## 12. Step 7 — Upload the file to S3

Replace `YOUR-BUCKET-NAME` with the actual bucket name.

```bash
aws s3 cp B102-book.txt s3://YOUR-BUCKET-NAME/B102-book.txt --region ap-south-1
```

Expected: output indicating that `B102-book.txt` was uploaded.

Verify the object:

```bash
aws s3 ls s3://YOUR-BUCKET-NAME/ --region ap-south-1
```

Expected: the listing includes `B102-book.txt`.

Optional download verification:

```bash
aws s3 cp s3://YOUR-BUCKET-NAME/B102-book.txt B102-book-downloaded.txt --region ap-south-1
cat B102-book-downloaded.txt
```

This optional step requires `s3:GetObject` permission.

## 13. Step 8 — Insert the book record into DynamoDB

Replace `YOUR-BUCKET-NAME` with the real bucket name. Confirm that `LibraryBooks` has `BookID` as its String partition key.

```bash
aws dynamodb put-item \
  --table-name LibraryBooks \
  --item '{
    "BookID": {"S": "B102"},
    "Title": {"S": "Python Programming"},
    "Author": {"S": "John Smith"},
    "Category": {"S": "Computer Science"},
    "Status": {"S": "Available"},
    "IssuedTo": {"S": "None"},
    "DocumentPath": {"S": "s3://YOUR-BUCKET-NAME/B102-book.txt"}
  }' \
  --region ap-south-1
```

Expected: a successful `put-item` usually returns no output. Verify the record with `get-item`.

- `S` means String.
- `DocumentPath` is the S3 URI stored as a String attribute.
- `put-item` may replace an existing item with the same primary key. Use a different `BookID` if you need to preserve existing data.

## 14. Step 9 — Retrieve the book record

Run:

```bash
aws dynamodb get-item \
  --table-name LibraryBooks \
  --key '{"BookID":{"S":"B102"}}' \
  --region ap-south-1
```

Expected: JSON output containing the book attributes and `DocumentPath`.

If no `Item` is returned, check the table name, region, `BookID`, and whether the insert succeeded.

## 15. Step 10 — Verify in the AWS Console

### DynamoDB
1. Open DynamoDB in the same region.
2. Open the `LibraryBooks` table.
3. Choose **Explore table items**.
4. Find `BookID = B102`.
5. Confirm the title, author, status, and `DocumentPath`.

### S3
1. Open S3.
2. Open the bucket used in the upload command.
3. Confirm that `B102-book.txt` appears in the object list.

The S3 file and DynamoDB item are separate resources. Uploading the file does not automatically create the database item.

## 16. Expected output

The practical is successful when:
- The EC2 instance can run AWS CLI commands using its IAM role.
- `B102-book.txt` is uploaded and listed in S3.
- `get-item` returns the `B102` book record.
- The DynamoDB record contains the correct S3 URI.
- The item and object are visible in their respective AWS Consoles.

## 17. Screenshots/evidence

Capture screenshots from your own environment:

1. EC2 instance in Running state.
2. EC2 terminal showing AWS CLI version and caller identity.
3. Terminal showing the sample book file.
4. Terminal showing S3 upload and listing.
5. Terminal showing `get-item` output.
6. S3 Console showing `B102-book.txt`.
7. DynamoDB Console showing the `LibraryBooks` item.

Do not expose AWS access keys, secret keys, session tokens, or private-key contents.

## 18. Record-book write-up

### Aim
To implement a basic library management record system using EC2, S3, and DynamoDB.

### Requirements
AWS account, EC2 instance, IAM role, AWS CLI, S3 bucket, and DynamoDB table.

### Procedure
1. Create or verify the `LibraryBooks` table with `BookID` as a String partition key.
2. Create or identify an S3 bucket and keep public access blocked.
3. Attach an IAM role to EC2 with the required DynamoDB and S3 permissions.
4. Connect to EC2 and verify AWS CLI and identity.
5. Create a sample book-related file.
6. Upload the file to S3 and verify it.
7. Insert the book attributes and S3 URI into DynamoDB.
8. Retrieve the item using `get-item`.
9. Verify both the database record and S3 object in the AWS Console.

### Output
A book-related file was uploaded to S3, and its book details and S3 URI were stored and retrieved from DynamoDB.

### Result
Basic library record management was demonstrated using EC2, S3, and DynamoDB.

## 19. Troubleshooting

### `Unable to locate credentials`
Verify that the correct IAM role is attached to EC2 and the instance can obtain role credentials. Do not paste long-term access keys into commands.

### DynamoDB `AccessDeniedException`
Check the role's permissions for the correct table and actions. Ask the account administrator to verify the resource ARN.

### S3 upload returns `AccessDenied`
Check the bucket name and role permissions. Uploading needs `s3:PutObject`; listing can separately need `s3:ListBucket`.

### `NoSuchBucket`
Replace `YOUR-BUCKET-NAME` with the exact bucket name.

### `ResourceNotFoundException`
Verify that `LibraryBooks` exists in the region specified in the command and is Active.

### Key validation error
Check that the table's partition key is `BookID` of type String.

### `get-item` returns no record
Check that the insert succeeded, the ID is exactly `B102`, and the table and region match.

### The file is in S3 but not in DynamoDB
The operations are independent. Run the DynamoDB `put-item` command separately.

## 20. Cost and security precautions

- Use your college's AWS account and follow its rules.
- Reuse provided lab resources when possible.
- Keep S3 Block Public Access enabled.
- Do not upload copyrighted, private, or sensitive documents unless authorized.
- Stop EC2 when finished if you need to preserve it for later; attached EBS storage and other resources may still incur charges.
- Do not delete resources needed for later practicals.
- Use least-privilege IAM permissions and never hardcode long-term access keys.

## 21. Viva questions and answers

**Q1. What is a library management system?**  
A system for keeping track of books and details such as title, author, availability, and borrower.

**Q2. What is Amazon EC2?**  
A service that provides virtual servers in AWS.

**Q3. What is Amazon S3?**  
An object storage service for files and objects.

**Q4. What is Amazon DynamoDB?**  
A managed NoSQL database that stores items and attributes.

**Q5. Why are S3 and DynamoDB both used in this practical?**  
S3 stores book-related files, while DynamoDB stores structured book details and the file's S3 URI.

**Q6. What is the example table name?**  
`LibraryBooks`, unless the official question specifies another.

**Q7. What is the partition key?**  
`BookID`, of type String.

**Q8. What does `put-item` do?**  
It writes an item to DynamoDB and can replace an item with the same primary key.

**Q9. What does `get-item` do?**  
It retrieves an item by its primary key.

**Q10. What does `aws s3 cp` do?**  
It copies a local file to S3 or downloads an S3 object.

**Q11. What does `DocumentPath` store?**  
The S3 URI of the related file, stored as a String.

**Q12. Does storing an S3 URI in DynamoDB upload the file?**  
No. The file must be uploaded to S3 separately.

**Q13. Why attach an IAM role to EC2?**  
It grants authorized AWS access without storing long-term access keys in the application or shell commands.

**Q14. Why keep S3 Block Public Access enabled?**  
It helps prevent accidental public exposure of stored files.

**Q15. What does `Status` represent?**  
Whether a book is available or issued in this simplified example.

**Q16. What does `IssuedTo: None` mean?**  
No borrower is recorded for the sample book.

**Q17. What is least privilege?**  
Granting only the permissions needed for the task.

**Q18. How do you confirm the practical worked?**  
The file appears in S3, and the corresponding book record is returned by `get-item` and visible in DynamoDB Console.

## 22. Final checklist

- [ ] Confirmed exact table name, key, and attributes from the college question.
- [ ] Selected the intended AWS region.
- [ ] Created or verified `LibraryBooks`.
- [ ] Created or identified the S3 bucket.
- [ ] Kept S3 Block Public Access enabled.
- [ ] Attached the correct IAM role to EC2.
- [ ] Verified AWS CLI and identity.
- [ ] Created a sample book-related file.
- [ ] Uploaded and verified the file in S3.
- [ ] Inserted book details and S3 URI into DynamoDB.
- [ ] Retrieved the record and verified it in the Console.
- [ ] Captured screenshots without exposing credentials.
- [ ] Reviewed viva questions and checked resource costs.
