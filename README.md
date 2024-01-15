# FREE AWS Cloud Project Bootcamp

![](https://codebuild.ca-central-1.amazonaws.com/badges?uuid=eyJlbmNyeXB0ZWREYXRhIjoidzQ5bVBva0pyU1lDODd1Uy96dXFxelNnTEh0dHFXUXNRR3hLT2RzRmVOaTZ5T3ZadHpSS29CazZ2SHBYckc0VXJEWEI2NFBKalMwcWM4RHh1Tk02b3RnPSIsIml2UGFyYW1ldGVyU3BlYyI6IjZTSzAxY2NiTU4rMmJJVGsiLCJtYXRlcmlhbFNldFNlcmlhbCI6MX0%3D&branch=main)

- Application: Cruddur
- Cohort: 2024-A1

This is the starting codebase that will be used in the FREE AWS Cloud Project Bootcamp 2024

![Cruddur Graphic](_docs/assets/cruddur-banner.jpg)

![Cruddur Screenshot](_docs/assets/cruddur-screenshot.png)

## Getting the AWS CLI Working

### Install AWS CLI

We have created a `.gitpod.yml` to include the following task.
It will install the AWS CLI when gitpod workspace is launched.

```sh
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
```
### Create a new User and Generate AWS Credentials

 - Go to the IAM User Console, create a new user and new group with `AdministratorAccess`.
 - Click on `Security Credentials` and `Create Access Key`. Download the CSV with the credentials.

### Set Env Vars

We'll tell Gitpod to remember these credentials if we relaunch our workspaces
```
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```

### Check that the AWS CLI is working and you are the expected user

```sh
aws sts get-caller-identity
```

You will see output like this:
```json
{
    "UserId": "AIFBZRJIQN2ONP4ET4EK4",
    "Account": "655602346534",
    "Arn": "arn:aws:iam::655602346534:user/andrewcloudcamp"
}
```
## Create an AWS Budget

We have created 2 files `budget.json` and `notifications-with-subscribers.json`.

In `budget.json`, we set the BudgetLimit, BudgetName, BudgetType, CostTypes, TimePeriod and TimeUnit.

In `notifications-with-subscribers.json`, we set the ComparisonOperator, NotificationType, Threshold and the email of the Subscribers.

Run the command in terminal to create the budget.

```sh
aws budgets create-budget \
    --account-id AccountID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```

## Creating a Billing Alarm

Create a SNS Topic which will return a TopicARN

```sh
aws sns create-topic --name billing-alarm
```

Create a subscription supply the TopicARN and our Email

```sh
aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint your@email.com
```

Go to AWS SNS Console, check the topic to verify notification is created.

### Create Alarm

We have created the `alarm-config.json`. Replace the AlarmActions with the AWS SNS arn and execute the command.

```sh
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm-config.json
```
