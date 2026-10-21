# Deployment Notes

This document records the actual deployment steps completed for the AWS Cloud Portfolio.

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

Uploaded the following website content to the bucket:

- `index.html` at the bucket root
- `assets/css/style.css`
- `assets/js/script.js`

The S3 Objects page showed the `assets/` folder and `index.html` object after upload.

## Stage 3 — Create CloudFront distribution

- **Distribution name:** `aws-cloud-portfolio-cdn`
- **Origin:** `oday-static-website-2026.s3.us-east-1.amazonaws.com`
- **Origin access:** CloudFront access to the private S3 origin enabled during setup
- **Origin Shield:** Disabled
- **WAF/security protections:** Not enabled to avoid unnecessary cost for this learning portfolio
- **Default root object:** `index.html`
- **CloudFront domain:** https://d4onyd9a8q3si.cloudfront.net
- **Billing mode shown in console:** Pay-as-you-go

The CloudFront distribution was created successfully. The website was then opened using the CloudFront HTTPS URL, and the user confirmed that it loads correctly.

## Architecture

1. Visitors connect to the CloudFront domain over HTTPS.
2. CloudFront retrieves static content from the S3 origin using the configured private-origin access.
3. The S3 bucket's Block Public Access setting remains enabled.

## Validation status

- [x] Website files uploaded to S3.
- [x] CloudFront distribution created.
- [x] `index.html` set as the default root object.
- [x] Website confirmed to load from the CloudFront HTTPS URL.
- [ ] Attempt direct access to an S3 object URL and confirm it is denied.
- [ ] Check navigation and static assets on desktop and mobile.
- [ ] Add sanitized AWS screenshots to the repository.

## Cost and security notes

- Pay-as-you-go does not mean all usage is guaranteed to be free. Monitor AWS Billing and usage.
- WAF was not enabled for this small portfolio; this is a cost-conscious learning choice, not a claim that the site has WAF protection.
- Do not publish AWS access keys, session tokens, passwords, or other credentials.
- Before committing screenshots, crop or blur account identifiers and any sensitive console details.
- Keep the bucket private. If a direct S3 URL is publicly accessible, stop and review the bucket policy and public-access settings.

## Screenshots

Save sanitized evidence in the `screenshots/` directory and update the checklist once each item has been verified.
