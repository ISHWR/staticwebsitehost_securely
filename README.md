# staticwebsitehost_securely
Steps to Host a Static Website on AWS (S3 + CloudFront + ACM + Route53)

✅ Implemented Policy

S3 bucket is private (public access blocked).

Content is accessible only via CloudFront.

Custom domain: ishwarbhumbak.com

SSL/TLS certificate from ACM

OAC (Origin Access Control) ensures CloudFront-only access

🔹 1. Purchase Domain

I purchased ishwarbhumbak.com via Route 53 ($15 + tax = $17.70).

You can buy .in domains for ~$8.

If the domain is purchased outside AWS → update the registrar’s nameservers to Route 53.

You can skip this step if you only want to access the site via the CloudFront URL

💡 Note: CloudFront already provides HTTPS for its default domain using a CloudFront-managed certificate. 
So your content is secure even without a custom domain, but you won’t have a custom URL.

🔹 2. Create S3 Bucket

Go to S3 → Create bucket

Bucket name = e.g. domain (ishwarbhumbak.com)

Region: choose any region I use ap-south-1

Keep Block Public Access enabled

No need to enable static website hosting (CloudFront will handle delivery).

🔹 3. Upload Content

Upload your index.html into the bucket.

No need to make files public.

🔹 4. Create ACM Certificate

Go to ACM (N. Virginia / us-east-1)

Request a public certificate for:

ishwarbhumbak.com

www.ishwarbhumbak.com

Validate with DNS CNAME in Route 53.

Wait for status = Issued.

🔹 5. Create CloudFront Distribution

Origin = your S3 bucket (REST endpoint)

Enable Origin Access Control (OAC)

Viewer Protocol Policy → Redirect HTTP → HTTPS

Allowed Methods → GET, HEAD

CNAMEs → ishwarbhumbak.com, www.ishwarbhumbak.com

Attach your ACM certificate

Default Root Object = index.html

Deploy distribution.

🔹 6. Update S3 Bucket Policy

Grant CloudFront access only:

Copy Policy from CloudFront>origin>origin access control section.

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "Service": "cloudfront.amazonaws.com" },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::ishwarbhumbak.com/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::YOUR_ACCOUNT_ID:distribution/DISTRIBUTION_ID"
        }
      }
    }
  ]
}


(Replace YOUR_ACCOUNT_ID & DISTRIBUTION_ID with your CloudFront values.)

🔹 7. Configure Route 53 (DNS)

A Record (Alias) → ishwarbhumbak.com → CloudFront distribution

CNAME / A Record → www.ishwarbhumbak.com → CloudFront


🔹 8. Test Website

Visit 👉 https://ishwarbhumbak.com

Your index.html should load securely via CloudFront

www.ishwarbhumbak.com should also work/redirect.

🎯 Final Result

✅ Fully private S3 bucket
✅ Secure HTTPS with ACM
✅ CloudFront as CDN layer
✅ Domain & DNS via Route 53

Website is now live: 🌐 https://ishwarbhumbak.com
