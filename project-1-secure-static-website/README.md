# Project 1: Secure Static Website on AWS (S3 + CloudFront)

## Goal
Host a static HTML/CSS website on AWS using a secure, production-style setup that follows DevSecOps best practices.

## Architecture Overview
- Static site hosted in Amazon S3 (private bucket)
- Content served through Amazon CloudFront (CDN)
- HTTPS enforced at the edge
- Direct S3 object access blocked
- IAM used with least privilege (no root usage)

## AWS Services Used
- Amazon S3
- Amazon CloudFront
- AWS IAM

## Security Objectives
- ❌ No public S3 bucket access
- ✅ Serve content only via CloudFront
- ✅ Enforce HTTPS
- ✅ Use IAM user instead of root
- ✅ Prevent direct object access

## What Was Implemented

### 1. Static Website
- Built a basic HTML/CSS site
- Entry file: `index.html`

### 2. S3 Configuration
- Bucket created with **Block Public Access enabled**
- Files uploaded privately
- Bucket policy allows access **only from CloudFront**

📸 Screenshot:
![S3 Bucket Settings](screenshots/s3-bucket-settings.png)

### 3. CloudFront Configuration
- S3 used as origin
- Origin Access Control (OAC) enabled
- Default root object set to `index.html`
- Viewer protocol policy: Redirect HTTP to HTTPS

📸 Screenshot:
![CloudFront Settings](screenshots/cloudfront-settings.png)

---

## Issue Encountered

When accessing the CloudFront domain, the site returned:

AccessDenied (XML error)


### Root Cause
- `index.html` was stored inside a subfolder in the S3 bucket
- CloudFront was configured to look for `index.html` at the bucket root
- CloudFront cached the error response

---

## Resolution

- Moved `index.html` to the **bucket root**
- Confirmed CloudFront **Default Root Object = index.html**
- Created a **CloudFront invalidation (`/*`)** to clear cached errors
- Verified CloudFront → S3 access permissions

📸 Screenshot:
![Access Denied Test](screenshots/access-denied-test.png)

---

## Validation Tests

- ✅ CloudFront URL loads site correctly
- ❌ Direct S3 object URL returns AccessDenied
- ✅ HTTPS enforced
- ✅ No public bucket access

---

## DevSecOps Lessons Learned

- CloudFront caches errors aggressively
- S3 static hosting ≠ CloudFront origin behavior
- Folder structure matters for root objects
- Secure-by-default setups require explicit permissions
- IAM least privilege prevents accidental exposure

---

## Next Steps
- Add AWS WAF
- Enable logging
- Perform threat modeling for this architecture


