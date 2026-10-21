# Automatic Deployment with GitHub Actions

The repository contains `.github/workflows/deploy.yml`, and the workflow has been run successfully. Pushing changes to `index.html` or `assets/**` on `main` will:

1. Authenticate to AWS using GitHub OIDC (no long-lived AWS access keys).
2. Upload `index.html` and sync the `assets/` folder to the existing private S3 bucket.
3. Request a CloudFront cache invalidation so visitors receive the updated files.

The deployment uses GitHub OIDC, a dedicated IAM role, repository variables, S3 sync, and CloudFront invalidation. Documentation-only changes do not trigger a website deployment.

## 1. Create or verify the GitHub OIDC provider in AWS

In the AWS Console, open **IAM → Identity providers**. If an OpenID Connect provider for GitHub Actions does not already exist, add one:

- Provider URL: `https://token.actions.githubusercontent.com`
- Audience: `sts.amazonaws.com`

Do not create a duplicate provider if it already exists.

## 2. Create a dedicated IAM policy

Create a policy such as `AwsCloudPortfolioGitHubDeployPolicy`. Replace `ACCOUNT_ID` and `DISTRIBUTION_ID` with your own account ID and CloudFront distribution ID before saving. The S3 bucket name and region below match this project.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListPortfolioBucket",
      "Effect": "Allow",
      "Action": ["s3:ListBucket"],
      "Resource": "arn:aws:s3:::oday-static-website-2026"
    },
    {
      "Sid": "DeployPortfolioObjects",
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:DeleteObject"],
      "Resource": "arn:aws:s3:::oday-static-website-2026/*"
    },
    {
      "Sid": "InvalidatePortfolioDistribution",
      "Effect": "Allow",
      "Action": ["cloudfront:CreateInvalidation"],
      "Resource": "arn:aws:cloudfront::ACCOUNT_ID:distribution/DISTRIBUTION_ID"
    }
  ]
}
```

The workflow syncs the `assets/` prefix with `--delete`, so `s3:DeleteObject` is needed only for that deployment path. Keep this bucket dedicated to the portfolio website.\n\n**Immutable subject claim note:** GitHub shows this repository's subject prefix as `repo:odayhaitham742-ops@244400858/aws-cloud-portfolio@1428466797`. The trust policy must use that exact prefix plus `:ref:refs/heads/main`; the older `repo:owner/repository:ref:...` format will not match this repository's current claim.

## 3. Create an IAM role trusted only by this repository

Create a role for web identity / GitHub Actions and attach the policy from Step 2. Use this trust policy, adjusting the account/provider ARN if required for your AWS account:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::ACCOUNT_ID:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
          "token.actions.githubusercontent.com:sub": "repo:odayhaitham742-ops@244400858/aws-cloud-portfolio@1428466797:ref:refs/heads/main"
        }
      }
    }
  ]
}
```

Copy the created role ARN. Do not create or store long-lived AWS access keys for this workflow.

## 4. Add GitHub repository configuration

Open the repository's **Settings → Secrets and variables → Actions**.

Create this **repository secret**:

- `AWS_ROLE_ARN`: the IAM role ARN from Step 3.

Create these **repository variables**:

- `AWS_REGION`: `us-east-1`
- `S3_BUCKET`: `oday-static-website-2026`
- `CLOUDFRONT_DISTRIBUTION_ID`: the ID shown on the CloudFront distribution details page (not the domain name and not the ARN).

Never put access keys, secret access keys, or session tokens into repository variables or files.

## 5. Run and verify the workflow

1. Open the repository's **Actions** tab.
2. Select **Deploy portfolio to AWS**.
3. Use **Run workflow** for the first test after all settings are saved, or push a small website change to `main`.
4. Check that all steps pass: checkout, OIDC credentials, S3 upload, and CloudFront invalidation.
5. Open the live site and verify the change. If the workflow fails, inspect the log and fix the specific missing permission or configuration; do not make the S3 bucket public.

## Deployment behavior and safety

- Changes to `index.html`, `assets/**`, or the workflow itself on `main` trigger deployment. Documentation-only changes do not upload the site.
- S3 Block Public Access should remain enabled. The workflow deploys to the private bucket and CloudFront remains the public endpoint.
- A CloudFront invalidation requests that cached paths be refreshed; AWS propagation can take time.
- GitHub Actions uses the configured OIDC provider, scoped IAM role, `AWS_ROLE_ARN` secret, and repository variables. A successful run has been verified.
