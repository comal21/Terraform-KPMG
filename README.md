## Task-1: Creating S3 bucket using Terraform

Create a New Configuration File
```
vi s3.tf
```
Add the below given lines, by pressing "INSERT"
```
provider "aws" {
  region = "us-east-2"
}

# Create S3 bucket
resource "aws_s3_bucket" "my_bucket_labten" {
  bucket = "terraform-lab-ten-kpmg"  # Change this to your desired bucket name
  aws_s3_bucket_acl    = "public-read"  # Use 'public-read' if you want to enable ACLs for public access

  tags = {
    Name        = "My S3 Bucket"
    Environment = "Dev"
  }
}

# Disable block public access
resource "aws_s3_bucket_public_access_block" "public_access_block" {
  bucket = aws_s3_bucket.my_bucket_labten.id

  block_public_acls   = false
  block_public_policy = false
  ignore_public_acls  = false
  restrict_public_buckets = false
}

# Enable versioning
resource "aws_s3_bucket_versioning" "versioning" {
  bucket = aws_s3_bucket.my_bucket_labten.bucket

  versioning_configuration {
    status = "Enabled"
  }
}

```
Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform fmt
```
```
terraform validate
```
```
terraform plan
```
```
terraform apply
```
Go to the S3 bucket and click on terraform > remotestate > In Properties Copy the Object URL and paste it in Browser. (By default it shows Access Denied)

To view the content of the file, in S3 Bucket tab, Click on permission and click on Edit under Access control list (ACL) > Everyone (public access) > Check "Read" then check I understand the effects of these changes on this object and then Click on Save changes

Refresh the Object URL Page in the browser (or again Copy-paste the object URL into the web browser).
Now, You should be able to access the state file and View the resources. (It shows the attributes of a single resource in the Terraform state of aws_instance.terraform-remoteState.)

