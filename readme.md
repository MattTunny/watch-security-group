# Security Group Watcher for Microsoft Teams
 
I work for @AWSCloud & my opinions are my own.


# Basic Intro
SAM package for creating a cloudwatch event when ever someone modifies a security group and posts into MS Teams in near real time. (can replace teams for slack in last function).
This is a simple example however you could modify the lambda to take action on rules you don't want allowed in your organisation.

<br>

# Requirements

- [Python](https://www.python.org/downloads/)
- [AWS SAM CLI](https://github.com/awslabs/aws-sam-cli)
- AWS Account 
- MS Teams or Slack account with permissions to install webhooks
- One S3 bucket to store your files

# Demo

- I add a new rule in my security group to: Allow all traffic on all ports (sigh, if only people never did this)
![](https://github.com/MattTunny/watch-security-group/blob/master/pictures/create-new-rule.png)

- This triggers a cloudwatch event and MS Teams sends a notification
![](https://github.com/MattTunny/watch-security-group/blob/master/pictures/alert-1.png)

- Inside Teams:
![](https://github.com/MattTunny/watch-security-group/blob/master/pictures/alert-2.png)

# Install Steps:

## Clone repo
`git clone https://github.com/MattTunny/watch-security-group.git`

## Pre Account Setup

- Make a s3 bucket for your lambda's:
`aws s3 mb s3://your-unique-bucket-name --region ap-southeast-2`


## Create MS Teams Webhook
- Log into your MS Teams account and select the channel you want to install the webhook
- Select Connectors
![](https://github.com/MattTunny/watch-security-group/blob/master/pictures/create-webhook-1.png)

- Click Add/Manage on Incoming Webhook
![](https://github.com/MattTunny/watch-security-group/blob/master/pictures/create-webhook-2.png)

- Give your webhook a name and picture and click create
![](https://github.com/MattTunny/watch-security-group/blob/master/pictures/create-webhook-3.png)

- Copy your webhook url
![](https://github.com/MattTunny/watch-security-group/blob/master/pictures/create-webhook-4.png)

- Paste your webhook url into the default value in template.yaml and if you want to test locally paste it into vars.json
![](https://github.com/MattTunny/watch-security-group/blob/master/pictures/paste-webhook-1.png)

![](https://github.com/MattTunny/watch-security-group/blob/master/pictures/paste-webhook-2.png)


## Deploy App

- run sam package to s3 bucket you created earlier: 
```bash
sam package --template-file template.yaml --s3-bucket your-s3-bucket --output-template sam-output.yaml
```
- run sam deploy:
```bash
sam deploy --template-file sam-output.yaml --stack-name WatchSecurityGroup --capabilities CAPABILITY_IAM
```

## Testing App

Testing is really easy with sam-cli, you just give it the function and event you want to invoke.
> Note: you need to modify the "groupId" value in the sample events with a real security group for testing, also add a real webhook value in vars.json.

- run sam test:
```bash
sam local invoke -e events/event_iam.json WatchSecurityGroupFunction -n vars.json
```