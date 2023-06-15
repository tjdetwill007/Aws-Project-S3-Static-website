## Deploying a static website on s3 bucket using AWS CLI.

#### This is a demo project where I delpoyed a static website on s3 bucket using AWS CLI. I will be using two methods to host the website using s3. First method will be by using s3 bucket ACL and granting public access read permission. The second method will be by giving permissions using  bucket policy only.

#### Resources used in this project:
-   AWS management cosole
-   IAM
-   S3
-   Terminal
-   AWS CLIv2

### Project Architecture:
![Project Diagram](https://github.com/faysalmehedi/aws-ha-app-deployment-demo/blob/main/ha-web-app-diagram.svg)

### What I did:

-   Log in to your AWS management console.
-   Now we have to generate an access key to get the programatic access in our local computer terminal:
    -  Either you can use your root account to generate the access key `(Not recommend by aws)` or you can create a new iam user and generate the access key for that account.
    -  Go on search tab and search for `iam`.
    -  On left hand side navigation panel click on `users`.
    -  Click on `Add users`, and give the name to user as you desire `projectuser`.
    -  Tick on `Provide user access to the AWS Management Console - optional` if you want to give management console access to the user.
    -  Select `I want to create an IAM user`, `Custom password` and give the password you desire then click `Next`.
    -  Select `Attach policies directly`, from the list of policies select `AmazonS3FullAccess` and then `Next` and click `create user`.
    -  In `Console sign-in details` click on `Download CSV file` which has the login credentials and then return to user list.
    -  In the user list select the user that we created, and then click on `Security Credentials`.
    -  Scroll down and then click on `Create Access Key` thereafter select `Command Line Interface (CLI)`, check the I understand.... and procced further on `Next`. In last Page Click `Create access Key`.
    - Download csv file of your key because you won't be able to retrive it afterwards once you lost it.

   #### Downloading Aws CLI v2 and configure in our local system.
   ##### Note: I am using Linux, if you are using windows please follow aws documentation to install it.

-   Open your terminal and paste following commands:
    ```
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

    unzip awscliv2.zip

    sudo ./aws/install 
    ```
-   Type `aws --version` to check if the aws was successfully installed.
-   Type `aws configure` hit enter.
    -   Paste the access key, and the Secret access key from the csv file that we downloaded.
    -   Give the region code in which you want to deploy your resources. `us-west-1`
    -   Keep default output format to `json`.
__________________________________________________________________________________________________________________________
## Method 1

#### Creating s3 bucket with ACL enabled.

-   Type following commands on terminal:
    -   ```
        aws s3api create-bucket --bucket <demoproject123321> --object-ownership BucketOwnerPreferred --create-bucket-configuration LocationConstraint=<us-west-1>
        ```
        Replace \<demoproject123321\> with the unique name that you want to give to your bucket. Remember the bucket names are globally unique.Also replace \<us-west-1\> with your region code.
        Here we created a s3 bucket which has ACL enabled in it.
    -   ```
        aws s3 website s3://<demoproject123321>/ --index-document index.html
        ```
        Here we enabled the static website hosting on s3.
    -   ```
        aws s3api put-public-access-block --bucket <demoproject123321> --public-access-block-configuration "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false"
        ```
        Here we disabled the Block all public access on bucket.
    -   Now download the static website content from my repository or you can use any static website of your own.
    -   ```
        aws s3 cp <PATH WHERE STATIC WEBSITE CONTENT IS IN LOCAL SYSTEM> s3://<demoproject123321>/ --recursive --acl public-read
        ```
        Here we uploaded the static website files recursively to our bucket having public read access permission on object acl.
    -   Access your webiste using static website enpoint url : 
        ```
        http://<demoproject123321>.s3-website-<us-west-1>.amazonaws.com/
        ```
        Replace \<demoproject123321\> and \<us-west-1\> with your bucket name and aws region.

__________________________________________________________________________________________________________________________
## Method 2

#### Creating s3 bucket with ACL disabled and allow permission to access website using bucket policy.

-   Type the following commands:
    -   ```sh 
        aws s3api create-bucket --bucket <demoproject123321> --region <us-west-1> --create-bucket-configuration LocationConstraint=<us-west-1>
        ```
        Replace \<demoproject123321\> with the unique name that you want to give to your bucket. Remember the bucket names are globally unique.Also replace \<us-west-1\> with your region code.
        Here we created a bucket with ACL disabled and no permission are allowed till now no the bucket for outside users.
    -   ```sh 
        aws s3 website s3://<demoproject123321>/ --index-document index.html
        ```
        Here we enabled the static website hosting on s3.
    -   ```sh 
        aws s3api put-public-access-block --bucket <demoproject123321> --public-access-block-configuration "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false"
        ```
        Here we disabled the Block all public access on bucket.
    -   Create Bucket Policy:
        ```sh
        aws s3api put-bucket-policy --bucket <demoproject123321> --policy '{
          "Id": "Policy1686383920695",
          "Version": "2012-10-17",
          "Statement": [
            {
              "Sid": "Stmt1686383918951",
              "Action": [
                "s3:GetObject"
              ],
              "Effect": "Allow",
              "Resource": "arn:aws:s3:::<demoproject123321>/*",
              "Principal": "*"
            }
          ]
        }'
        ```
        Here we applied the bucket policy to our bucket which allows public users to access its object.
    -   ```sh
        aws s3 cp <PATH WHERE STATIC WEBSITE CONTENT IS IN LOCAL SYSTEM> s3://<demoproject123321>/ --recursive
        ```
        Here we uploaded the static website files recursively to our bucket.

    -   Access your webiste using static website enpoint url : 
        ```
        http://<demoproject123321>.s3-website-<us-west-1>.amazonaws.com/
        ```
        Replace \<demoproject123321\> and \<us-west-1\> with your bucket name and aws region.

## Conclusion:

We successfully deployed a static website on aws s3 bucket by using aws cli to get error free and fast deployement within minutes.

## About me:

Hi my name is **Deepak Kumar**, this project is done by me. I took help from official aws documentation page to know about several aws cli commands for s3. I got to learn many things while doing this project, and i hope this will be helpful for everyone who all are new to Aws like me.

    

    

  



