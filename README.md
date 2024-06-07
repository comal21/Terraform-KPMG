## Task-1: Creating S3 bucket using Terraform
```
cd ~
mkdir S3-Lab && cd S3-Lab
```
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
resource "aws_s3_bucket" "my_simple_bucket" {
  bucket = "my-simple-bucket-name-labten"  # Change this to your desired globally unique bucket name

  tags = {
    Name        = "My Simple S3 Bucket"
    Environment = "Dev"
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
Go to the S3 bucket 
"Enable versioning"
Go to Block public access (bucket settings) -> Uncheck "block public access" and Type "confirm"

## Task 2: Configure Remote State

```
mkdir remote-state-lab && cd remote-state-lab
```
Create a New Configuration File
```
vi instance.tf
```
Add the below given lines, by pressing "INSERT"
```
resource "aws_instance" "terraform-remoteState" {
  ami           = var.AMIS[var.AWS_REGION]
  instance_type = "t2.nano"
}

output "ip" {
  value = aws_instance.terraform-remoteState.public_ip
}
```
Save the file using "ESCAPE + :wq!"
```
vi vars.tf
```
In vars.tf file ensure to replace your region and Include your region's Ubuntu AMI ID to the list.
```
variable "AWS_REGION" {
  default = "us-east-2"
}

variable "AMIS" {
  type = map(string)
  default = {
    us-east-2 = "ami-0e83be366243f524a"
    us-west-2 = "ami-02e7fad8336aa2c57"
    eu-west-1 = "ami-029f9476"
  }
}
```
Once done, save the file and follow further steps.
```
vi provider.tf
provider "aws" {
  region = var.AWS_REGION
}
```
Now, Create a New Configuration File for storing "terraform.tfstate" file in the backend. (ie. Amazon S3.)
```
vi backend.tf
```
Add the given lines, by pressing "INSERT" and add your bucket's region and Bucket Name
```
terraform {
  backend "s3" {
    region = "<Replace your s3 bucket region>"
    bucket = "<Replace your s3 bucket name>"
    key    = "terraform/remotestate"
  }
}
```
cat backend.tf
```
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
Use the terraform destroy command to clean the infrastructure used in this lab,
```
terraform destroy
```
Once done, Remove the directory and Zip file using "rm -rf"
```
cd ~
rm -rf S3-Lab
```
Note: Also Ensure to delete the S3 Bucket (To delete, first empty the Bucket and then Delete it.)
