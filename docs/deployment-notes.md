# Deployment Notes

This document records the AWS steps completed for the portfolio. The screenshot-by-screenshot explanation is in [Deployment Walkthrough](deployment-walkthrough.md).

## Stage 1 — Create the S3 bucket

- **Bucket:** `oday-static-website-2026`
- **Region:** US East (N. Virginia), `us-east-1`
- **Bucket type:** General purpose
- **Object ownership:** ACLs disabled
- **Versioning:** Disabled for this learning project
- **Default encryption:** SSE-S3
- **Block Public Access:** Enabled
- **Result:** Bucket created successfully

## Stage 2 — Upload website files

Uploaded:
- `index.html` at the bucket root
- `assets/css/style.css`
- `assets/js/script.js`

The S3 Objects page showed the `assets/` folder and `index.html` object after upload.

## Stage 3 — Create CloudFront distribution

- **Distribution name:** `aws-cloud-portfolio-cdn`
- **Origin:** `oday-static-website-2026.s3.us-east-1.amazonaws.com`
- **Origin access:** CloudFront access to the private S3 origin enabled during setup
- **Origin Shield:** Disabled
- **WAF/security protections:** Not enabled for this small learning portfolio to avoid unnecessary cost
- **Default root object:** `index.html`
- **CloudFront domain:** https://d4onyd9a8q3si.cloudfront.net
- **Billing mode shown in console:** Pay-as-you-go

## Validation status

- [x] Website files uploaded to S3.
- [x] CloudFront distribution created.
- [x] `index.html` set as the default root object.
- [x] Website confirmed to load from the CloudFront HTTPS URL.
- [x] Direct S3 `index.html` URL tested in Incognito; S3 returned `AccessDenied`, indicating this object is not publicly readable through that URL.
- [ ] Verify navigation and static assets on desktop and mobile.
- [x] Sanitized screenshots uploaded to the repository.\n- [ ] Configure GitHub Actions AWS OIDC access before automatic deployment can run.

## Cost and security notes

- Pay-as-you-go does not guarantee that every usage pattern is free. Monitor AWS Billing and current pricing.
- WAF was not enabled; do not claim the project is protected by AWS WAF.
- Never publish AWS access keys, session tokens, passwords, or other credentials.
- Keep S3 Block Public Access enabled and review the bucket policy if direct S3 access is ever allowed unexpectedly.
- Sanitize screenshots before publishing them: obscure account identifiers and any secrets.

## Evidence

See [deployment walkthrough](deployment-walkthrough.md) for screenshot-by-screenshot explanations. The GitHub Actions workflow at `.github/workflows/deploy.yml` has been tested successfully. It uses GitHub OIDC and the dedicated IAM role to upload `index.html` and sync `assets/` to the private S3 bucket, then requests a CloudFront invalidation. The current trust policy must match the repository's immutable GitHub OIDC subject claim; see the [automation guide](github-actions-deployment.md).
