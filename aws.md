
#  AWS CLI CTF COMMAND NOTES (COMPLETE)

---

##  PHASE 0 — Identity & Context (ALWAYS FIRST)

### Who am I?

```bash
aws sts get-caller-identity
```

✔ User vs Role
✔ Account ID
✔ ARN (needed for assume-role & simulation)

---

### Current region

```bash
aws configure get region
```

Try others if empty:

```bash
export AWS_DEFAULT_REGION=us-east-1
```

---

##  IAM (If allowed)

### Get user

```bash
aws iam get-user
```

### List user policies

```bash
aws iam list-attached-user-policies --user-name USER
aws iam list-user-policies --user-name USER
```

### Read inline policy

```bash
aws iam get-user-policy \
--user-name USER \
--policy-name POLICY
```

---

### Roles

```bash
aws iam list-roles
aws iam get-role --role-name ROLE
```

### Role policies

```bash
aws iam list-attached-role-policies --role-name ROLE
aws iam list-role-policies --role-name ROLE
```

---

### Managed policy → actual permissions

```bash
aws iam get-policy --policy-arn ARN
aws iam get-policy-version \
--policy-arn ARN \
--version-id v1
```

 Look for:

* `Action: "*"`
* `Resource: "*"`
* `iam:PassRole`
* `sts:AssumeRole`

---

### Simulate permissions (if allowed)

```bash
aws iam simulate-principal-policy \
--policy-source-arn YOUR_ARN \
--action-names s3:ListAllMyBuckets iam:PassRole sts:AssumeRole
```

---

##  STS / ROLE ASSUMPTION (BIG CTF WIN)

### Assume role

```bash
aws sts assume-role \
--role-arn arn:aws:iam::ACCOUNT:role/ROLE \
--role-session-name ctf
```

Export creds:

```bash
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...
```

---

##  S3 (MOST IMPORTANT SERVICE)

### List buckets

```bash
aws s3 ls
```

### List bucket contents

```bash
aws s3 ls s3://bucket-name
```

### Download object

```bash
aws s3 cp s3://bucket/flag.txt .
```

### Recursive download

```bash
aws s3 cp s3://bucket . --recursive
```

---

### Bucket permissions

```bash
aws s3api get-bucket-acl --bucket bucket
aws s3api get-bucket-policy --bucket bucket
```

 CTF patterns:

* Can read objects but not list
* Public bucket
* `.env`, `backup.zip`, `flag.txt`

---

##  Secrets Manager (HIGH VALUE)

### List secrets

```bash
aws secretsmanager list-secrets
```

### Read secret

```bash
aws secretsmanager get-secret-value \
--secret-id SECRET_NAME
```

 Secrets often contain:

* DB creds
* API keys
* Another role ARN
* External ID

---

##  SSM Parameter Store

### List params

```bash
aws ssm describe-parameters
```

### Read param

```bash
aws ssm get-parameter \
--name param-name \
--with-decryption
```

---

##  EC2

### List instances

```bash
aws ec2 describe-instances
```

### User data

```bash
aws ec2 describe-instance-attribute \
--instance-id i-xxx \
--attribute userData
```

---

### Metadata (if shell access)

```bash
curl http://169.254.169.254/latest/meta-data/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

 Steal role creds → pivot

---

##  Lambda

### List functions

```bash
aws lambda list-functions
```

### Get function details

```bash
aws lambda get-function \
--function-name NAME
```

### Environment variables

```bash
aws lambda get-function-configuration \
--function-name NAME
```

 Lambda role often over-privileged

---

##  CloudWatch Logs

### List log groups

```bash
aws logs describe-log-groups
```

### Streams

```bash
aws logs describe-log-streams \
--log-group-name NAME
```

### Read logs

```bash
aws logs get-log-events \
--log-group-name NAME \
--log-stream-name STREAM
```

 Secrets in logs = classic mistake

---

##  CloudTrail

```bash
aws cloudtrail describe-trails
aws cloudtrail get-trail-status --name NAME
```

Check if logging is disabled or readable.

---

##  ECR (Containers)

```bash
aws ecr describe-repositories
aws ecr list-images --repository-name NAME
```

If docker allowed → pull image → inspect.

---

##  RDS

```bash
aws rds describe-db-instances
```

Check for:

* Public access
* Weak creds (from Secrets Manager)

---

##  ENUMERATION WHEN IAM IS BLOCKED (VERY IMPORTANT)

Try **capability probing**:

```bash
aws s3 ls
aws ec2 describe-instances
aws lambda list-functions
aws secretsmanager list-secrets
```

Interpret results:

| Result       | Meaning                                |
| ------------ | -------------------------------------- |
| Works        | Permission exists                      |
| AccessDenied | Blocked                                |
| NotFound     | Permission exists but resource missing |

---

##  COMMON CTF ESCALATION PATHS

1. Secrets Manager → creds → assume role
2. S3 → config → role ARN
3. Lambda env vars → admin key
4. EC2 metadata → role creds
5. iam:PassRole → Lambda/EC2 → admin

---

##  INTERVIEW GOLD LINE (USE THIS)

> “When IAM enumeration is blocked, I rely on service-level probing to infer effective permissions and look for privilege escalation via roles, secrets, or storage misconfigurations.”

This sounds **senior**.

---

## ONE-PAGE LAST-MINUTE FLOW

```text
sts get-caller-identity
↓
test s3 / secrets / lambda
↓
find creds or role
↓
assume role
↓
dump data
```

---

If you want next:

*  AWS privilege escalation playbook
*  Mock AWS CTF lab walkthrough
*  Printable 1-page cheat sheet
*  Azure & GCP equivalent command notes

Just say what you want.
