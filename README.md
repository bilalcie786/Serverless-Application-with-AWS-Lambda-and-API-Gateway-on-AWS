# Serverless-Application-with-AWS-Lambda-and-API-Gateway-on-AWS

Overview
Serverless computing is a Cloud Computing model in which a cloud provider such as AWS automatically manages the provisioning and allocation of compute resources. In Contrast with traditional cloud computing where the user is responsible for directly managing the underlying infrastructure.

In AWS, one can deploy a "serverless" Application Architecture using AWS API Gateway that exposes these Services via an API that can be consumed publicly. In this post, we will cover this architecture as:

Web Client --> API Gateway --> Lambda --> S3

Prerequisites
In order to follow this tutorial, you will need:

AWS Account
Terraform Installed
AWS CLI
IAM Credentials with Suitable Access to create Lambda, S3, API Gateway calls. Simplest approach is to use, Administrative access to the target AWS Account.
In the following section we will manually emulate an automatic build process using the zip command line tool and the AWS CLI. The latter must also have access to your AWS credentials, and the easiest way to achieve this is to provide them via environment variables so that they can be used by both the AWS CLI and Terraform.

Warning: Following this tutorial will create objects in your AWS account that will cost you money against your AWS bill.

Building the Lambda Function Package
AWS Lambda expects a function's implementation to be provided as an archive containing the function source code and any other static files needed to execute the function.

Terraform is not a build tool, so the zip file must be prepared using a separate build process prior to deploying it with Terraform. For a real application we recommend automating your build via a CI system, whose job is to run any necessary build actions (library installation, compilation, etc), produce the deployment zip file as a build artifact, and then upload that artifact into an Amazon S3 bucket from which it will be read for deployment.

For the sake of this tutorial we will perform these build steps manually and build a very simple AWS Lambda function. Start by creating a new directory called example that will be used to create the archive, and place in it a single source file. We will use the JavaScript runtime in this example, so our file is called main.js.

The above is the simplest possible Lambda function for use with API Gateway, returning a hard-coded "Happy New Year!" response in the object structure that API Gateway expects.

Follow the steps below to zip and upload the file to S3:

From your command prompt, change to the directory containing that file.
zip up main.js and put the resulting file (example.zip) alongside your current working directory. Change directory back to your original.
Create an S3 bucket.
Copy example.zip on your local computer to the S3 bucket.
Verify the bucket has the example.zip
Create the Lambda Function. Copy the content from the lambda.tf file
Run terraform init which in this case will install the AWS provider.
Apply the configuration by running terraform apply
After the lambda function is created sucessfully, try invoking it using the following aws cli:
_aws lambda invoke --region=us-east-1 --function-name=ServerlessExample output.txt_

Configure the API Gateway by copying the contents from the api_gateway.tf file and place it on the same directly as lambda.tf
Create an API Gateway "deployment" in order to activate the configuration and expose the API at a URL that can be used for testing. You don't need to do anything here as the api_gateway.tf file already contains this step. This is just for information.
**terraform apply **
After the step above, the new object will be visibile in the API Gateway console.
Note: The integration with the Lambda function is not functional yet because API Gateway does not have the necessary access to invoke the function. The next step will address this, making the application fully-functional.

Allow API Gateway to access Lambda. Again, you don't need to so anything here as this is already done for you in lambda.tf
terraform apply
Finally, load the URL in your browser that was given in the terraform output from the terraform apply. Output should be similar to the one below.
Apply complete! Resources: 8 added, 0 changed, 0 destroyed.

Outputs:

base_url = "https://y4w3r4g2wg.execute-api.us-east-2.amazonaws.com/test"

Happy Terraforming!
