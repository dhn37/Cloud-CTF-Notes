# GCP Cloud Security CTF Command Notes

---

## Phase 0 Identity and Context

### Who am I

```bash
gcloud auth list
```

Active account

```bash
gcloud config list account
```

---

### Active project

```bash
gcloud config list project
```

Set project

```bash
gcloud config set project PROJECT_ID
```

---

## IAM and Permissions

### Get IAM policy for project

```bash
gcloud projects get-iam-policy PROJECT_ID
```

Look for roles

* roles/owner
* roles/editor
* roles/viewer
* custom roles

---

### Describe current identity permissions

```bash
gcloud iam roles describe ROLE_NAME
```

For custom roles

```bash
gcloud iam roles describe ROLE_NAME --project PROJECT_ID
```

---

### Test permissions directly

```bash
gcloud iam list-testable-permissions \
projects/PROJECT_ID
```

---

## Service Accounts

### List service accounts

```bash
gcloud iam service-accounts list
```

---

### Describe service account

```bash
gcloud iam service-accounts describe SA_EMAIL
```

---

### Service account keys

```bash
gcloud iam service-accounts keys list \
--iam-account SA_EMAIL
```

If you can create a key you can fully impersonate the service account.

---

## Service Account Impersonation

### Impersonate service account

```bash
gcloud auth print-access-token \
--impersonate-service-account SA_EMAIL
```

Set for future commands

```bash
export GOOGLE_OAUTH_ACCESS_TOKEN=$(gcloud auth print-access-token --impersonate-service-account SA_EMAIL)
```

This is a common privilege escalation vector.

---

## Cloud Storage Buckets

### List buckets

```bash
gsutil ls
```

---

### List bucket contents

```bash
gsutil ls gs://bucket-name
```

---

### Download object

```bash
gsutil cp gs://bucket/flag.txt .
```

---

### Recursive download

```bash
gsutil cp -r gs://bucket .
```

---

### Check bucket IAM

```bash
gsutil iam get gs://bucket-name
```

Look for allUsers or allAuthenticatedUsers.

---

## Secret Manager

### List secrets

```bash
gcloud secrets list
```

---

### Access secret

```bash
gcloud secrets versions access latest \
--secret SECRET_NAME
```

Secrets often contain API keys credentials or project info.

---

## Compute Engine

### List instances

```bash
gcloud compute instances list
```

---

### Instance metadata

```bash
gcloud compute instances describe INSTANCE \
--zone ZONE
```

Check metadata and startup scripts.

---

### SSH into instance

```bash
gcloud compute ssh INSTANCE --zone ZONE
```

If SSH works you likely have powerful permissions.

---

### Metadata server from inside VM

```bash
curl http://metadata.google.internal/computeMetadata/v1/ \
-H "Metadata-Flavor: Google"
```

Service account tokens can be retrieved here.

---

## Cloud Functions

### List functions

```bash
gcloud functions list
```

---

### Describe function

```bash
gcloud functions describe FUNCTION
```

Check environment variables and service account.

---

## Cloud Run

### List services

```bash
gcloud run services list
```

---

### Describe service

```bash
gcloud run services describe SERVICE
```

Look for unauthenticated access and env vars.

---

## Kubernetes Engine GKE

### List clusters

```bash
gcloud container clusters list
```

---

### Get credentials

```bash
gcloud container clusters get-credentials CLUSTER
```

Then

```bash
kubectl get pods -A
```

If this works you often have cluster admin access.

---

## Logging and Monitoring

### List log sinks

```bash
gcloud logging sinks list
```

---

### Read logs

```bash
gcloud logging read "resource.type=gce_instance" --limit 50
```

Secrets sometimes leak in logs.

---

## Enumeration When IAM Is Restricted

If IAM listing is blocked test services directly.

```bash
gsutil ls
gcloud secrets list
gcloud compute instances list
gcloud functions list
gcloud run services list
```

Interpret results

* Works means permission exists
* Permission denied means blocked
* Not found means permission exists but resource missing

---

## Common GCP CTF Escalation Paths

* Service account impersonation allowed
* Service account key creation enabled
* Public GCS bucket with config files
* Cloud Function env vars contain secrets
* Compute metadata exposes tokens
* Editor role leads to full compromise

---

## Interview Quality Explanation

I first identify the active account and project then enumerate IAM bindings. If IAM access is restricted I probe high value services like storage secrets compute and functions to infer effective permissions and look for service account abuse paths.

---

## Fast GCP CTF Flow

```text
gcloud auth list
gcloud config list project
gsutil ls
gcloud secrets list
gcloud iam service-accounts list
gcloud compute instances list
```

---

If you want next I can

* Combine AWS Azure and GCP into one comparison sheet
* Create a cloud CTF escalation decision tree
* Run a full mock multi cloud interview scenario
* Turn these into printable interview notes

Just tell me.
