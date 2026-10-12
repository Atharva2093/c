# Practical 5 — Student Records Management Using EC2, S3, and DynamoDB

## 1. Aim

To store student details in Amazon DynamoDB, upload student-related documents to Amazon S3, and use an Amazon EC2 instance to run commands for accessing these cloud services.

## 2. Scope from the college practical list

The college problem statement says that a college wants to store student details in DynamoDB, upload student documents to S3, and use an EC2 instance to run a simple application for managing student records.

This guide demonstrates the cloud resources and commands required for a beginner lab. It uses AWS CLI from EC2 rather than building a web application, because the practical sheet does not specify a programming language or framework. If your instructor explicitly requires a coded application, follow that requirement as well.

Use exact table names, key names, attributes, and region from your instructor if they differ from the examples below.

## 3. AWS services used

- **Amazon EC2:** A virtual server used to run AWS CLI commands.
- **Amazon DynamoDB:** Stores structured student details.
- **Amazon S3:** Stores student documents or sample files.
- **AWS IAM role:** Allows the EC2 instance to access required AWS services without hardcoded long-term access keys.
- **AWS CLI:** Runs cloud-service operations from the EC2 terminal.

## 4. Example design

### DynamoDB table

Example table name: `StudentRecords`

Partition key: `StudentID` (String)

| Attribute | Example | Type |
|---|---|---|
| `StudentID` | `S101` | String, partition key |
| `Name` | `Rahul Sharma` | String |
| `Course` | `Computer Engineering` | String |
| `Year` | `3` | Number |
| `DocumentPath` | `s3://YOUR-BUCKET-NAME/student-S101.txt` | String |

### S3 object

Example object: `student-S101.txt`

It contains a few lines of sample student information. `DocumentPath` records the object's S3 URI in DynamoDB; it does not place the file itself inside DynamoDB.

## 5. Requirements

- AWS account with permission to use EC2, IAM, DynamoDB, and S3.
- Region: Mumbai (`ap-south-1`), unless your college specifies another region.
- An Amazon Linux EC2 instance.
- An EC2 IAM role with permissions for the intended DynamoDB table and S3 bucket.
- A DynamoDB table with the required partition key.
- An S3 bucket with a globally unique name.
- An authorized method to connect to EC2.

## 6. Step 1 — Create the DynamoDB table

1. Sign in to the AWS Management Console.
2. Select Mumbai (`ap-south-1`) unless your college specifies another region.
3. Search for **DynamoDB** and open it.
4. Select **Tables** → **Create table**.
5. Enter `StudentRecords` as the table name, unless your official question specifies another.
6. Enter `StudentID` as the partition key and select **String**.
7. Do not add a sort key unless the question requires one.
8. Keep default settings unless your lab specifies otherwise.
9. Choose **Create table** and wait for the status to become **Active**.

If the table already exists, verify its key and region instead of creating a duplicate.

## 7. Step 2 — Create an S3 bucket

1. Open **S3** in the AWS Console.
2. Choose **Create bucket** if your college has not provided a bucket.
3. Choose a globally unique bucket name. For example, use a name patterned like `student-records-practical-<unique-suffix>`, replacing the placeholder with a unique suffix that meets S3 naming rules. The example name is not guaranteed to be available.
4. Select the required region, `ap-south-1` for this guide.
5. Keep **Block all public access** enabled.
6. Create the bucket and record its exact name.

If your college has provided a bucket, use that bucket instead. Do not make student documents public for this practical.

## 8. Step 3 — Create or verify an IAM role for EC2

The EC2 instance needs permission to use DynamoDB and S3.

1. Open **IAM** in the AWS Console.
2. Select **Roles** → **Create role**.
3. Choose **AWS service** as the trusted entity and **EC2** as the use case.
4. Add the permissions needed for the practical. Prefer least-privilege permissions restricted to the intended table and bucket.
5. Name the role, for example `StudentRecordsEC2Role`.
6. Create the role.
7. Open **EC2** → **Instances**.
8. Select the instance, choose **Actions** → **Security** → **Modify IAM role** (console wording can vary).
9. Attach `StudentRecordsEC2Role` and save.

### Permission guidance

For this example, the role may need:
- `dynamodb:PutItem` and `dynamodb:GetItem` on the `StudentRecords` table.
- `s3:PutObject` on the target bucket's object ARN.
- `s3:ListBucket` on the bucket ARN if using `aws s3 ls`.
- `s3:GetObject` if you perform the optional download verification.
- `dynamodb:DescribeTable` only if you run a command requiring it.

Ask your AWS administrator to configure the approved permissions if you cannot manage IAM. Do not grant unrestricted administrator access merely to avoid permission errors. Do not embed AWS access keys in commands or code.

## 9. Step 4 — Launch or connect to EC2

If your lab has a suitable EC2 instance, use it. Otherwise:

1. Open **EC2** in the AWS Console in the intended region.
2. Choose **Launch instances**.
3. Name the instance `Student-Records-Practical`.
4. Choose an Amazon Linux AMI approved by your lab.
5. Select an instance type allowed by the lab.
6. Configure a key pair only if your connection method requires one.
7. Do not open unnecessary inbound ports. For SSH, restrict port 22 to your own public IP; alternatively use an authorized EC2 Instance Connect setup supported by your environment.
8. Attach the `StudentRecordsEC2Role` IAM role.
9. Launch the instance and wait for **Running** status and passing status checks.
10. Select the instance → **Connect**, then use an authorized connection method.

Run the remaining commands in the **EC2 terminal**.

## 10. Step 5 — Verify AWS CLI and identity

Run:

```bash
aws --version
```

Expected: AWS CLI version information.

Then:

```bash
aws sts get-caller-identity
```

Expected: JSON showing the account and identity used by the CLI. With an EC2 role, the ARN generally refers to an assumed role.

Optional region check:

```bash
aws configure get region
```

If it returns no value, continue using `--region ap-south-1` on each command.

If AWS CLI is missing, follow the lab's approved installation instructions for your Amazon Linux AMI. Do not assume Ubuntu package commands apply to Amazon Linux.

## 11. Step 6 — Create a student document on EC2

Run:

```bash
cat > student-S101.txt <<'EOF'
Student ID: S101
Name: Rahul Sharma
Course: Computer Engineering
Year: 3
EOF
```

Display the file:

```bash
cat student-S101.txt
```

Expected output:

```text
Student ID: S101
Name: Rahul Sharma
Course: Computer Engineering
Year: 3
```

This is a sample document for the S3 upload demonstration. Do not use real student personal data in a practice account.

## 12. Step 7 — Upload the student document to S3

Replace `YOUR-BUCKET-NAME` with the actual bucket name.

```bash
aws s3 cp student-S101.txt s3://YOUR-BUCKET-NAME/student-S101.txt --region ap-south-1
```

Expected: output indicating that the file was uploaded.

Verify:

```bash
aws s3 ls s3://YOUR-BUCKET-NAME/ --region ap-south-1
```

Expected: the listing includes `student-S101.txt`.

Optional: download the file to verify it can be retrieved:

```bash
aws s3 cp s3://YOUR-BUCKET-NAME/student-S101.txt student-downloaded.txt --region ap-south-1
cat student-downloaded.txt
```

This optional step requires `s3:GetObject` permission.

## 13. Step 8 — Store student details in DynamoDB

Replace `YOUR-BUCKET-NAME` with the actual S3 bucket name before running the command. Confirm the table is named `StudentRecords` and has `StudentID` as a String partition key.

```bash
aws dynamodb put-item \
  --table-name StudentRecords \
  --item '{
    "StudentID": {"S": "S101"},
    "Name": {"S": "Rahul Sharma"},
    "Course": {"S": "Computer Engineering"},
    "Year": {"N": "3"},
    "DocumentPath": {"S": "s3://YOUR-BUCKET-NAME/student-S101.txt"}
  }' \
  --region ap-south-1
```

Expected: successful `put-item` normally returns no output. Verify by retrieving the item.

### Attribute types

- `S` means String.
- `N` means Number.
- `DocumentPath` is a String containing the S3 URI.
- `put-item` can replace an existing item with the same primary key. Use a different `StudentID` if you must preserve an existing record.

## 14. Step 9 — Retrieve the student record

Run:

```bash
aws dynamodb get-item \
  --table-name StudentRecords \
  --key '{"StudentID":{"S":"S101"}}' \
  --region ap-south-1
```

Expected: JSON output containing `StudentID`, `Name`, `Course`, `Year`, and `DocumentPath`.

If the output contains no `Item`, verify the table name, region, key, and insert operation.

## 15. Step 10 — Verify in AWS Console

### DynamoDB

1. Open DynamoDB in the same region.
2. Open `StudentRecords`.
3. Choose **Explore table items**.
4. Find `StudentID = S101`.
5. Confirm the attributes and S3 URI.

### S3

1. Open S3.
2. Open the exact bucket used in the command.
3. Confirm `student-S101.txt` appears.

The S3 object and DynamoDB item are separate. Uploading the document does not automatically create the DynamoDB item; both operations are required.

## 16. Expected output

The practical is successful when:

- EC2 is accessible and AWS CLI commands work.
- `student-S101.txt` is uploaded and listed in S3.
- `get-item` returns the student record.
- DynamoDB's `DocumentPath` attribute contains the correct S3 URI.
- The file and item are visible in their respective AWS Consoles.

## 17. Screenshots/evidence

Capture screenshots from your own environment:

1. EC2 instance in Running state.
2. Terminal showing `aws --version` and `aws sts get-caller-identity`.
3. Terminal showing the student file contents.
4. Terminal showing the S3 upload and object listing.
5. Terminal showing `get-item` returning the student record.
6. S3 Console showing `student-S101.txt`.
7. DynamoDB Console showing the `StudentRecords` item.

Never expose AWS access keys, secret keys, session tokens, private key contents, or other credentials in screenshots.

## 18. Record-book write-up

### Aim
To manage student records using Amazon EC2, Amazon S3, and Amazon DynamoDB.

### Requirements
AWS account, EC2 instance, IAM role, AWS CLI, S3 bucket, and DynamoDB table.

### Procedure
1. Create or verify the `StudentRecords` table with `StudentID` as a String partition key.
2. Create or identify an S3 bucket with public access blocked.
3. Attach an IAM role to EC2 with the necessary S3 and DynamoDB permissions.
4. Connect to EC2 and verify the AWS CLI and current identity.
5. Create `student-S101.txt`.
6. Upload the document to S3 and verify it using `aws s3 ls`.
7. Insert the student details and S3 URI into DynamoDB.
8. Retrieve the record using `aws dynamodb get-item`.
9. Verify both the file and the database item in the AWS Console.

### Output
The student document was uploaded to S3 and the corresponding student details and S3 URI were stored and retrieved from DynamoDB.

### Result
Student record management was demonstrated using EC2, S3, and DynamoDB.

## 19. Troubleshooting

### `Unable to locate credentials`
Verify that the EC2 instance has the correct IAM role attached and can obtain role credentials. Do not paste long-term access keys into commands.

### DynamoDB `AccessDeniedException`
The role may not have the required action on the correct table. Ask the account administrator to verify the policy and table ARN.

### S3 `AccessDenied`
Check the bucket name and the role's object permissions. Listing a bucket may require `s3:ListBucket`, separate from `s3:PutObject`.

### `NoSuchBucket`
Replace `YOUR-BUCKET-NAME` with the actual bucket name. Bucket names are globally unique and spelling must match exactly.

### `ResourceNotFoundException`
Confirm that `StudentRecords` exists in the region specified in the command and is Active.

### Key `ValidationException`
Confirm that `StudentID` is the partition key and its type is String.

### `get-item` returns no record
Verify that the insert succeeded and that the table, region, and ID are correct.

### S3 upload succeeds but DynamoDB item is missing
S3 and DynamoDB operations are independent. Run the `put-item` command separately.

## 20. Cost and security precautions

- Use your college AWS account and follow its rules.
- Reuse lab resources where possible.
- Keep S3 Block Public Access enabled.
- Do not store real student personal data in a practice bucket.
- Stop EC2 when finished if you need to preserve it for later. Compute charges generally stop while it is stopped, but attached EBS storage and other resources may still incur charges.
- Do not delete resources if they are needed for later practicals.
- Use least-privilege IAM permissions and never hardcode long-term access keys.

## 21. Viva questions and answers

**Q1. What is Amazon EC2?**  
A cloud service that provides virtual servers.

**Q2. What is Amazon S3?**  
An object storage service used to store files and objects.

**Q3. What is Amazon DynamoDB?**  
A managed NoSQL database service that stores items with attributes.

**Q4. Why are S3 and DynamoDB both used?**  
S3 stores the student document, while DynamoDB stores structured student details and the document's S3 URI.

**Q5. What is the example table name?**  
`StudentRecords`, unless the official question specifies another name.

**Q6. What is the partition key in this example?**  
`StudentID`, of type String.

**Q7. What does `put-item` do?**  
It writes an item to DynamoDB and may replace an existing item with the same primary key.

**Q8. What does `get-item` do?**  
It retrieves an item by its primary key.

**Q9. What does `aws s3 cp` do?**  
It copies a file to or from S3.

**Q10. What does `DocumentPath` store?**  
The S3 URI for the document, as a String attribute.

**Q11. Does storing an S3 URI in DynamoDB upload the file?**  
No. The file must be uploaded to S3 separately.

**Q12. Why attach an IAM role to EC2?**  
It provides AWS permissions without embedding long-term access keys in code.

**Q13. Why should S3 Block Public Access remain enabled?**  
It helps prevent accidental exposure of student documents.

**Q14. What is least privilege?**  
Granting only the permissions necessary for the task.

**Q15. How do you verify the practical worked?**  
The file is visible in S3, and the corresponding student item is visible in DynamoDB and returned by `get-item`.

## 22. Final checklist

- [ ] Confirmed the exact table name and fields from the college question.
- [ ] Selected the intended AWS region.
- [ ] Created or verified `StudentRecords`.
- [ ] Created or identified the S3 bucket.
- [ ] Kept S3 Block Public Access enabled.
- [ ] Attached the correct IAM role to EC2.
- [ ] Verified AWS CLI and caller identity.
- [ ] Created the sample student file.
- [ ] Uploaded and verified the file in S3.
- [ ] Inserted the student record and S3 URI into DynamoDB.
- [ ] Retrieved the record and verified it in the Console.
- [ ] Captured screenshots without exposing credentials.
- [ ] Reviewed the viva questions and checked AWS resource costs.
