# Day 1 — Static Website Hosting with Amazon S3

## Objective

Host a simple static website on Amazon S3 and make its web content publicly readable. The site has a landing page and a custom error page.

## Services and concepts

- Amazon S3 bucket and objects
- S3 static website hosting
- Bucket policies and least privilege
- S3 Block Public Access settings
- S3 website endpoint testing

## Architecture

```text
Browser --> S3 static website endpoint --> S3 bucket
                                      ├── index.html
                                      └── error.html
```

## Files in this folder

| File | Purpose |
| --- | --- |
| `index.html` | Default landing page |
| `error.html` | Page shown for an unavailable path |

## Implementation

### 1. Create the bucket

1. In the AWS Console, open **Amazon S3** and choose **Create bucket**.
2. Choose the lab Region.
3. Enter a globally unique name, such as `yourinitials-website-yourzipcode-randomnumber`.
4. Keep the remaining defaults and create the bucket.

> Bucket names cannot be changed after creation. Do not put sensitive data in a public website bucket.

### 2. Enable static website hosting

1. Open the new bucket and select the **Properties** tab.
2. In **Static website hosting**, choose **Edit**.
3. Enable it and select **Host a static website**.
4. Set the index document to `index.html`.
5. Set the error document to `error.html`.
6. Save changes and copy the displayed **Bucket website endpoint**.

### 3. Permit public reads

1. Open the bucket's **Permissions** tab.
2. In **Block public access (bucket settings)**, choose **Edit**.
3. Clear **Block all public access**, acknowledge the warning, and save.
4. In **Bucket policy**, add the following policy after replacing `YOUR-BUCKET-NAME` with the actual bucket name:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadWebsiteObjects",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
    }
  ]
}
```

This policy grants anonymous users read-only access to objects in this one bucket. It does not grant upload or delete permission.

### 4. Upload the site

1. Open the bucket's **Objects** tab and choose **Upload**.
2. Select `index.html` and `error.html` from this folder.
3. Upload both files.

### 5. Validate

1. Open the bucket website endpoint. The `index.html` page should load.
2. Add `/does-not-exist.html` to the endpoint URL. The `error.html` page should load.
3. Capture screenshots for your GitHub README after removing any account-specific information.

## Security and Solutions Architect notes

- Public-read S3 is appropriate only for deliberately public, non-sensitive web assets.
- Keep public access blocked for normal data buckets.
- S3 static website endpoints are intended for basic static hosting; production workloads commonly use CloudFront in front of a private S3 origin, with HTTPS, a custom domain, and AWS WAF where appropriate.
- S3 provides default encryption at rest for new uploads, but encryption does not make an object private when its bucket policy permits public reads.

## Cleanup

If this is your own AWS account, delete the objects and then delete the S3 bucket when finished. A lab-provided account may remove resources automatically when the lab ends.

## Evidence to add

- Screenshot of the S3 static website endpoint showing the landing page
- Screenshot of the custom error page
- Optional screenshot of the bucket's static website hosting configuration

Do not commit AWS access keys, secret keys, account IDs, or sensitive URLs.
