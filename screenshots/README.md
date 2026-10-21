# Screenshot Evidence

This folder stores sanitized screenshots from the actual deployment. Use these filenames so the captions in [Deployment Walkthrough](../docs/deployment-walkthrough.md) match the evidence.

1. `01-s3-bucket-created.png` — S3 bucket created in `us-east-1`.
2. `02-s3-public-access-blocked.png` — S3 Block Public Access settings.
3. `03-s3-website-files-uploaded.png` — `assets/` and `index.html) visible in S3 Objects.
4. `04-cloudfront-origin-private-access.png` — CloudFront origin settings with private S3 access enabled.
5. `05-cloudfront-distribution-created.png` — CloudFront distribution details and domain.
6. `06-cloudfront-default-root-object.png` — `index.html` configured as the default root object.
7. `07-cloudfront-website-live.png` — website rendered from the CloudFront HTTPS URL.
8. `08-s3-direct-access-denied.png` — direct S3 `index.html` request showing `AccessDenied`.

Only add screenshots that show the real state of the project. Crop or obscure AWS account IDs, ARNs containing account IDs, and unrelated account details where practical. Never include passwords, access keys, session tokens, or other credentials.

Use one screenshot per stage rather than many near-duplicates. Add the actual PNG files to this folder before marking the evidence complete.
