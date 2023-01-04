# Terraform-AWS-S3-DynamoDB-Statelocking

**To migrate a Terraform state file to an S3 bucket and use a DynamoDB table to manage locking for multiple users, you will need to perform the following steps:**

Create an S3 bucket to store the state file.
Create a DynamoDB table to manage locking of the state file. You can use the following code to create the table:


```
resource "aws_dynamodb_table" "terraform_state_lock" {
  name           = "terraform-state-lock"
  read_capacity  = 20
  write_capacity = 20
  hash_key       = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }

  ttl {
    attribute_name = "TTL"
    enabled       = true
  }
}

```

Configure the S3 bucket and DynamoDB table to allow read/write access to the IAM user or role that will be running the Terraform commands.
Create a new Terraform configuration file (e.g. "backend.tf") and add the following code to it:


```
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "path/to/state/file.tfstate"
    region = "ap-south-1"

    dynamodb_table = "terraform-state-lock"
  }
}
```


Replace ```"my-terraform-state"``` with the name of your S3 bucket, and ```"path/to/state/file.tfstate"``` with the desired file path and name for your state file. Specify the correct region for your S3 bucket and the name of your DynamoDB table.

Run the following command to migrate the state file to the S3 bucket and create the DynamoDB table:

``terraform init``
This will initialize the Terraform backend and migrate the state file to the S3 bucket. It will also create the DynamoDB table if it does not already exist.

To switch to using the state file in the S3 bucket for all future Terraform operations, run the following command:

```terraform workspace select default```
This will select the default workspace, which uses the state file in the S3 bucket as its source.

Note: If you are using Terraform workspaces, you will need to use the terraform workspace select command to switch to the desired workspace before running any other Terraform commands.

With these steps, you should now be able to use the S3 bucket and DynamoDB table to store and manage the Terraform state file for multiple users.
