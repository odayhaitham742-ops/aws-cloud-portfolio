# Deployment Notes

## Stage 1 — Create the S3 bucket

- **Bucket:** `oday-static-website-2026`
- **Region:** US East (N. Virginia), `us-east-1`
- **Bucket type:** General purpose
- **Object ownership:** ACLs disabled
- **Versioning:** Disabled for this learning project
- **Default encryption:** SSE-S3
- **Block Public Access:** On
- **Bucket policy:** None configured yet
- **Result:** Bucket created successfully

## Stage 2 — Website files

Pending: add and test the static website files, then upload them to S3.

## Stage 3 — CloudFront

Pending: create a distribution with the S3 bucket as origin, configure OAC, add a restricted bucket policy, set `index.html` as the default root object, and verify HTTPS access.

## Validation

Record the CloudFront URL, test outcomes, and any issues here only after the tests have actually been completed.

## Screenshots

Store sanitized screenshots in `screenshots/`. Before committing, hide AWS account identifiers and never include credentials, access keys, session tokens, or other secrets.
