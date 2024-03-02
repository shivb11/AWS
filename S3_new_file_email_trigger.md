# S3 File Error Handling Automation

This Error handling function will trigger an email to requested email recipients when a file is been moved to certain folder in S3 


## Step 1: Create IAM role

Create IAM role which have S3 read only and SES full access

## Step 2: AWS SES Config
* Identify the Source Email Address (this is the email address from which Amazon will send the email to requested recipients).

* Go to Amazon SES and add the email address under identity configuration. 

* This email address need to be verified (i.e. a verification link will be sent to that email id to confirm) 

## Step 3: Create Lambda function 

* Provide appropriate Function name

* Select Python 3.9 as runtime code

* Change the default execution role to the newly created IAM role in Step1.

* Click Create function 

Add the following code in the code snippet window 
Python 3.9 code:

```python
import json
import boto3

client = boto3.client('ses', region_name='us-west-2')

def lambda_handler(event, context):
    
    response = client.send_email(
    Destination={
        'ToAddresses': ['RecipientEmailAddress']
    },
    Message={
        'Body': {
            'Text': {
                'Charset': 'UTF-8',
                'Data': 'This is the message body in text format.',
            }
        },
        'Subject': {
            'Charset': 'UTF-8',
            'Data': 'Test email',
        },
    },
    Source='SourceEmailAddress'
    )
    
    print(response)
    
    return {
        'statusCode': 200,
        'body': json.dumps("Email Sent Successfully. MessageId is: " + response['MessageId'])
    }
```
Please change the following details in above code

* `RecipientEmailAddress` - to the actual recipient’s address.

* `SourceEmailAddress` - the email address which we added to SES service

* `Body` - the body of the email which is being sent

* `Subject` - the subject of the email which is being sent

## Step 4: Add Triggers

* Click Add trigger on the left of the lambda function (every S3 folder need to have different triggers and same lambda function can work)

* Select S3 as source

* Provide the correct path (of Not processed folder) to the bucket field 

* Select Add

## Step 5: Deploy 

Deploy the Lambda function and it will start sending email when triggered. 

# Monitoring
Click on Monitor tab in the lambda function and it will procide all the cloudwatch metrics

# Error log
In the Monitor tab, click the `View Cloudwatch logs` to see the detail log of the failed attempt and will provide the respective errors 
