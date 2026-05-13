# About this Project
This project automates AWS cloud cost optimization by identifying and deleting 
stale EBS snapshots that are no longer associated with any active EC2 instance. 
Built using AWS Lambda (Python), IAM policies, and EventBridge for daily scheduling.
## Architecture 

```
EventBridge (Daily Cron)
        │
        ▼
  Lambda Function
  (cost-optimization-ebs-snapshot)
        │
        ├── ec2:DescribeSnapshots   → List all snapshots
        ├── ec2:DescribeInstances   → List running instances
        ├── ec2:DescribeVolumes     → Get attached volumes
        └── ec2:DeleteSnapshot      → Delete stale snapshots
```
### Step 1 — Created EBS Snapshot (Manual Test)
Created a test EBS snapshot manually in AWS Console to simulate a stale snapshot 
that would be cleaned up by the automation.
<img width="1366" height="609" alt="Screenshot 2026-05-13 142119" src="https://github.com/user-attachments/assets/cdd5976c-79e7-4475-bd51-1ff3a9687cf0" />


### Step 2 — Built Lambda Function
Created an AWS Lambda function named `cost-optimization-ebs-snapshot` using 
Python 3.12 runtime to automatically detect and delete unused EBS snapshots.
<img width="1364" height="540" alt="Screenshot 2026-05-13 142535" src="https://github.com/user-attachments/assets/237bcc78-bdcf-4602-a05f-f71b73f7b17b" />


### Step 3 — Faced IAM Permission Errors
While testing the Lambda function, encountered UnauthorizedOperation errors 
for ec2:DescribeSnapshots and ec2:DescribeInstances — because no IAM permissions 
were attached to the Lambda execution role.
<img width="1096" height="467" alt="Screenshot 2026-05-13 142928" src="https://github.com/user-attachments/assets/bda5519f-a0f7-4f9a-873e-ce4151723781" />
<img width="727" height="397" alt="Screenshot 2026-05-13 144155" src="https://github.com/user-attachments/assets/084e5af9-5385-4864-bfc4-a6feffc70389" />


### Step 4 — Created IAM Policy
Created a custom IAM policy named `cost-optimization-ebs` with least-privilege 
permissions: DescribeSnapshots, DescribeInstances, DescribeVolumes, and DeleteSnapshot.
<img width="1359" height="550" alt="Screenshot 2026-05-13 144546" src="https://github.com/user-attachments/assets/0b32a4d9-0c6d-496f-ad4f-2cd08ac5999d" />
<img width="1358" height="609" alt="Screenshot 2026-05-13 143835" src="https://github.com/user-attachments/assets/11aa1593-0d4a-4076-947c-edaff262dc9f" />


### Step 5 — Attached Policy to Lambda Role
Attached the custom IAM policy to the Lambda execution role 
`cost-optimization-ebs-snapshot-role` so the function could access EC2 resources.
<img width="1355" height="540" alt="Screenshot 2026-05-13 144706" src="https://github.com/user-attachments/assets/5bee6b1b-d5f1-4070-942e-51ee74f7a3c1" />
<img width="1348" height="563" alt="Screenshot 2026-05-13 144029" src="https://github.com/user-attachments/assets/bfa22bd8-4dd1-44a1-b9c1-df3d5c7ea5ac" />


### Step 6 — Lambda Test Succeeded
After attaching the correct permissions, re-ran the Lambda test — 
Status changed to Succeeded. The function ran in ~3400ms and used 104MB memory.

<img width="1040" height="452" alt="Screenshot 2026-05-13 144741" src="https://github.com/user-attachments/assets/d32335b9-20eb-4e0c-a2fe-8452a851c9f5" />


### Step 7 — Snapshot Deleted Successfully
Verified in the EC2 Snapshots console that the stale snapshot 
`snap-035f769cc1fecdb95` was automatically deleted by the Lambda function.
<img width="1364" height="559" alt="Screenshot 2026-05-13 145119" src="https://github.com/user-attachments/assets/4f30ea18-a032-49a1-94c5-698adbf51897" />

