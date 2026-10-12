# Practical 5 — Employee Record Management Using EC2, S3, and DynamoDB

## 1. Aim

To create and manage employee records using AWS services: store structured employee information in DynamoDB, upload an employee record file to Amazon S3, and perform the operations from an Amazon EC2 instance.

## 2. Services used

- **Amazon EC2:** Virtual server used to run AWS CLI commands.
- **Amazon S3:** Object storage used to store the employee record file.
- **Amazon DynamoDB:** NoSQL database used to store structured employee details and the file's S3 location.
- **AWS IAM role:** Gives the EC2 instance permission to use the required AWS services without embedding long-term access keys in commands or code.
- **AWS CLI:** Command-line interface used to access AWS services.

## 3. Requirements and important notes

- AWS account with permission to create EC2, IAM roles, S3 buckets, and DynamoDB tables.
- Region: Mumbai (`ap-south-1`), unless your official question specifies another region.
- An Amazon Linux EC2 instance.
- An IAM role attached to EC2 with the required S3 and DynamoDB permissions.
- An S3 bucket with a globally unique name.
- A DynamoDB table named `Employees` with partition key `EmployeeID` of type String, unless the official question specifies different names.
- Access to EC2 through EC2 Instance Connect or another authorized method.

**Use the exact names in your college question if they differ from this guide.** The steps below are a sample implementation. Keep S3 Block Public Access enabled; public access is not needed for this practical.

## 4. Sample employee record

| Attribute | Example value | Type |
|---|---|---|
| `EmployeeID` | `E101` | String; partition key |
| `Name` | `Rahul Sharma` | String |
| `Department` | `IT` | String |
| `Position` | `Developer` | String |
| `Email` | `rahul.sharma@example.com` | String |
| `DocumentPath` | `s3://YOUR-BUCKET-NAME/employee.txt` | String |

Replace the example email and other data if your teacher gives a required sample. `DocumentPath` is a text attribute that records the object's S3 URI; DynamoDB does not automatically upload or validate that file.

## 5. Step 1 — Create or verify the DynamoDB table

1. Sign in to the AWS Management Console.
2. Select Mumbai (`ap-south-1`) unless your lab specifies another region.
3. Search for **DynamoDB** and open it.
4. Select **Tables** → **Create table**.
5. Enter table name `Employees`.
6. Set partition key to `EmployeeID` and type to **String**.
7. Do not add a sort key unless the question requires one.
8. Keep the default settings unless the lab instructions say otherwise.
9. Choose **Create table** and wait until the status is **Active**.

If the table already exists, verify its name, region, and partition key rather than creating a duplicate.

## 6. Step 2 — Create or identify an S3 bucket

1. Open **S3** in the AWS Console.
2. Choose **Create bucket** if the lab has not already provided one.
3. Enter a globally unique bucket name. For example, you may use a name patterned like `employee-records-practical-<unique-suffix>`, replacing the placeholder with a unique suffix. This example is not guaranteed to be available.
4. Choose the required region. For this guide, use `ap-south-1`.
5. Keep **Block all public access** enabled.
6. Create the bucket.
7. Copy its exact bucket name into your notes; you will replace `YOUR-BUCKET-NAME` in the commands below.

If your college has already provided a bucket, use it instead of creating a new one. Do not make the bucket public to simplify uploading.

## 7. Step 3 — Create an IAM role for EC2

EC2 should use an instance role rather than hardcoded access keys.

1. Open **IAM** in the AWS Console.
2. Choose **Roles** → **Create role**.
3. Select **AWS service** as the trusted entity and choose **EC2** as the use case.
4. Add permissions sufficient for the required operations on the intended DynamoDB table and S3 bucket. Prefer a least-privilege policy restricted to the required resources and actions.
5. Give the role a recognizable name, such as `EmployeeRecordEC2Role`.
6. Create the role.
7. Return to **EC2** → **Instances**, select your instance, and choose **Actions** → **Security** → **Modify IAM role** (the exact console wording may vary).
8. Attach `EmployeeRecordEC2Role` and save.

### Permission guidance

For this example, the instance needs DynamoDB permissions for the required table operations, such as `dynamodb:PutItem` and `dynamodb:GetItem` on the `Employees` table. It may need `dynamodb:DescribeTable` if you use a command that describes the table.

For S3, uploading and listing objects may require `s3:PutObject` on the intended bucket's object ARN and `s3:ListBucket` on the bucket ARN. Download verification additionally needs `s3:GetObject`. Use your lab administrator's approved policy. Do not grant unrestricted administrator access solely to make the practical work.

If you are not allowed to create IAM roles or policies, ask the lab administrator to attach an approved role.

## 8. Step 4 — Launch or connect to EC2

If your lab already has a suitable instance, use it. Otherwise:

1. Open **EC2** in the AWS Console and select the same region.
2. Choose **Launch instances**.
3. Name the instance `Employee-Record-Practical`.
4. Select an Amazon Linux AMI approved by your lab.
5. Select an instance type allowed by the lab; a small general-purpose instance is normally sufficient for CLI work.
6. Configure a key pair if required by your connection method.
7. Do not open unnecessary inbound ports. For SSH, restrict port 22 to your own public IP; alternatively use an authorized EC2 Instance Connect setup.
8. Under advanced details or the instance's security settings, ensure the `EmployeeRecordEC2Role` IAM role is attached.
9. Launch and wait until the instance is **Running** and status checks pass.
10. Select the instance → **Connect** and use an authorized connection method.

Run the following commands in the **EC2 terminal**:

```bash
aws --version
aws sts get-caller-identity
```

Expected:
- The first command prints the AWS CLI version.
- The second prints JSON containing the AWS account and the identity being used. With an instance role, the ARN generally refers to an assumed role.

If `aws` is not found, follow your lab's approved AWS CLI installation steps for the AMI in use. Do not assume Ubuntu package commands apply to Amazon Linux.

## 9. Step 5 — Create the employee file on EC2

In the EC2 terminal, create a sample file:

```bash
cat > employee.txt <<'EOF'
Employee ID: E101
Name: Rahul Sharma
Department: IT
Position: Developer
Email: rahul.sharma@example.com
EOF
```

View it:

```bash
cat employee.txt
```

Expected output:

```text
Employee ID: E101
Name: Rahul Sharma
Department: IT
Position: Developer
Email: rahul.sharma@example.com
```

This file is a simple example document for demonstrating S3 object storage.

## 10. Step 6 — Upload the file to S3

Replace `YOUR-BUCKET-NAME` with the exact bucket name you created or were given.

```bash
aws s3 cp employee.txt s3://YOUR-BUCKET-NAME/employee.txt --region ap-south-1
```

Expected: output indicating that `employee.txt` was uploaded.

Verify the object:

```bash
aws s3 ls s3://YOUR-BUCKET-NAME/ --region ap-south-1
```

Expected: the listing contains `employee.txt`.

Optional download verification:

```bash
aws s3 cp s3://YOUR-BUCKET-NAME/employee.txt employee-downloaded.txt --region ap-south-1
cat employee-downloaded.txt
```

This optional download requires read permission on the object.

## 11. Step 7 — Store employee details and S3 location in DynamoDB

The `Employees` table should have `EmployeeID` as a String partition key. Replace `YOUR-BUCKET-NAME` with the actual bucket name before running the command.

```bash
aws dynamodb put-item \
  --table-name Employees \
  --item '{
    "EmployeeID": {"S": "E101"},
    "Name": {"S": "Rahul Sharma"},
    "Department": {"S": "IT"},
    "Position": {"S": "Developer"},
    "Email": {"S": "rahul.sharma@example.com"},
    "DocumentPath": {"S": "s3://YOUR-BUCKET-NAME/employee.txt"}
  }' \
  --region ap-south-1
```

Expected: a successful `put-item` operation usually returns no output. Confirm success by retrieving the item.

### Type notation

- `S` means String.
- `N` means Number.
- `DocumentPath` is a String containing the S3 URI; it is not a special DynamoDB link type.
- `put-item` can replace an existing item with the same primary key. Use a different `EmployeeID` if you must preserve an existing record.

## 12. Step 8 — Retrieve the employee record

Run:

```bash
aws dynamodb get-item \
  --table-name Employees \
  --key '{"EmployeeID":{"S":"E101"}}' \
  --region ap-south-1
```

Expected: JSON output containing `EmployeeID`, `Name`, `Department`, `Position`, `Email`, and `DocumentPath`.

If there is no `Item` in the output, check the table name, region, partition-key value, and whether the insert succeeded.

## 13. Step 9 — Verify in the AWS Console

### Verify DynamoDB

1. Open DynamoDB in the same region.
2. Open the `Employees` table.
3. Select **Explore table items**.
4. Find the item with `EmployeeID = E101`.
5. Confirm the employee attributes and `DocumentPath`.

### Verify S3

1. Open S3.
2. Open the exact bucket used in the CLI command.
3. Confirm that `employee.txt` appears in the object list.

The object in S3 and the employee item in DynamoDB are separate resources. The DynamoDB `DocumentPath` attribute records where the file is stored; it does not itself store the file.

## 14. Expected output

The practical is successful when:

1. The EC2 terminal can run AWS CLI commands using the attached IAM role.
2. `employee.txt` is uploaded to the intended S3 bucket.
3. The S3 listing shows `employee.txt`.
4. `get-item` returns employee E101 from DynamoDB.
5. The DynamoDB item includes the correct S3 URI in `DocumentPath`.
6. Both the object and item are visible in their respective AWS Consoles.

## 15. Screenshots/evidence

Capture screenshots from your own AWS environment:

1. EC2 instance in Running state.
2. EC2 terminal showing `aws --version` and successful `aws sts get-caller-identity`.
3. Terminal showing the successful S3 upload and S3 object listing.
4. Terminal showing `get-item` returning employee E101.
5. S3 Console showing `employee.txt` in the correct bucket.
6. DynamoDB Console showing the `Employees` table item and its attributes.

Never include access keys, secret keys, session tokens, private-key contents, or other credentials in screenshots or your report.

## 16. Record-book write-up

### Aim
To manage employee records using Amazon EC2, Amazon S3, and Amazon DynamoDB.

### Requirements
AWS account, EC2 instance, IAM instance role, AWS CLI, S3 bucket, and DynamoDB table.

### Procedure
1. Create or verify the `Employees` DynamoDB table with `EmployeeID` as a String partition key.
2. Create or identify an S3 bucket and keep public access blocked.
3. Attach an IAM role to EC2 with the required S3 and DynamoDB permissions.
4. Connect to EC2 and verify AWS CLI and identity.
5. Create the sample employee file `employee.txt`.
6. Upload the file to S3 using `aws s3 cp` and verify with `aws s3 ls`.
7. Insert the employee attributes and S3 URI into DynamoDB using `aws dynamodb put-item`.
8. Retrieve the item using `aws dynamodb get-item`.
9. Verify the file and database item in the AWS Console.

### Output
The employee file was uploaded to S3, and the employee details with the file's S3 URI were stored and retrieved from DynamoDB.

### Result
Employee record management was demonstrated using EC2, S3, and DynamoDB.

## 17. Troubleshooting

### `Unable to locate credentials`
The CLI cannot find credentials. Verify that the correct IAM role is attached to the EC2 instance and that the instance can obtain role credentials. Do not paste long-term access keys into the command line.

### `AccessDeniedException` from DynamoDB
The role may lack the required action or the policy may reference the wrong table or region. Ask the AWS account administrator to check the intended resource permissions.

### S3 upload returns `AccessDenied`
Check the bucket name and region, and confirm that the role allows `s3:PutObject` for the target object path. Listing the bucket may separately require `s3:ListBucket`.

### `NoSuchBucket`
Check that you replaced `YOUR-BUCKET-NAME` with the exact bucket name. S3 bucket names are globally unique and spelling matters.

### `ResourceNotFoundException`
Confirm that `Employees` exists in `ap-south-1` and is Active.

### `ValidationException` for the key
Confirm that `EmployeeID` is the partition key and is a String. The key supplied to `get-item` must match the table's key name and type.

### `get-item` returns no item
Verify that `put-item` ran successfully, the `EmployeeID` is exactly `E101`, and both commands use the same table and region.

### File exists in S3 but not in DynamoDB
S3 and DynamoDB operations are independent. Uploading a file does not automatically create a database item; run the `put-item` command separately.

## 18. Cost and security precautions

- Use your college's AWS account and follow its lab rules.
- Do not create extra resources if the lab has already provided them.
- Stop the EC2 instance when you are finished if you need to keep it for later. Compute charges generally stop while it is stopped, but attached EBS storage and other resources may still incur charges.
- Do not delete resources if you need the data for later practicals.
- Keep S3 Block Public Access enabled.
- Do not grant broad administrator permissions just to fix an error.
- Never embed long-term AWS access keys in scripts or practical files.

## 19. Viva questions and answers

**Q1. What is Amazon EC2?**  
A cloud service that provides virtual servers.

**Q2. What is Amazon S3?**  
An object storage service used to store files and objects.

**Q3. What is Amazon DynamoDB?**  
A managed NoSQL database that stores items with attributes.

**Q4. Why are both S3 and DynamoDB used?**  
S3 stores the employee file, while DynamoDB stores structured employee details and the file's S3 URI.

**Q5. What is an IAM role?**  
An AWS identity that grants permissions to a trusted service or workload, such as an EC2 instance.

**Q6. Why attach a role to EC2?**  
It provides authorized AWS access without putting long-term access keys in code.

**Q7. What is the partition key in this example?**  
`EmployeeID`, of type String.

**Q8. What does `put-item` do?**  
It writes an item to DynamoDB and can replace an existing item with the same primary key.

**Q9. What does `get-item` do?**  
It retrieves an item using its primary key.

**Q10. What does `aws s3 cp` do?**  
It copies a file to or from S3.

**Q11. What does `aws s3 ls` do?**  
It lists S3 buckets or objects at the specified location, subject to permissions.

**Q12. What is `DocumentPath`?**  
A String attribute that records the S3 URI of the employee file.

**Q13. Does DynamoDB store the file when `DocumentPath` is saved?**  
No. The file remains in S3; DynamoDB stores only the URI string in this example.

**Q14. Why should the S3 bucket not be public?**  
Employee information can be sensitive. Public access is unnecessary for this practical and increases exposure risk.

**Q15. What is least privilege?**  
Giving a role only the permissions necessary for its task.

**Q16. Why can an S3 upload succeed while the DynamoDB operation fails?**  
They are separate services with separate operations and permissions.

**Q17. How do you verify the database record?**  
Run `get-item` and inspect the item in DynamoDB Console.

**Q18. How do you verify the uploaded file?**  
Run `aws s3 ls` for the bucket and confirm the object in S3 Console.

## 20. Final checklist

- [ ] Confirmed exact table name and attributes from the college question.
- [ ] Selected the intended AWS region.
- [ ] Created or verified `Employees` with `EmployeeID` as a String partition key.
- [ ] Created or identified the S3 bucket.
- [ ] Kept S3 Block Public Access enabled.
- [ ] Attached the correct IAM role to EC2.
- [ ] Verified AWS CLI and identity.
- [ ] Created `employee.txt`.
- [ ] Uploaded and verified the file in S3.
- [ ] Inserted employee details and S3 URI into DynamoDB.
- [ ] Retrieved the record with `get-item`.
- [ ] Verified both resources in the AWS Console.
- [ ] Captured screenshots without credentials.
- [ ] Reviewed the viva questions.
- [ ] Checked AWS resources and cost before finishing.
