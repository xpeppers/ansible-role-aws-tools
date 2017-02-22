# Ansible Role for AWS tools

# Select tools to install
In vars file _global.yaml_ there are the variables to select which components install or not.
Override them with value false to not install that role. By default all variables are true and all components are installed
* _install\_awscli_: for the awscli
* _install\_logs_agent_: for cloudwatch agent
* _install\_custom_metrics_: for custom metrics script
* _install\_codedeploy_: for codedeploy agent
* _install\_cfn_bootstrap_: for cfn-bootstrap components

# How to use it manually
Add this repository in the _roles_ folder of your playbook and use it as normale role.

For example for an ubuntu instance:
```yaml
---

- hosts: all
  remote_user: ubuntu
  become: yes
  become_method: sudo

  roles:
    - ansible-role-aws-tools
```

# Roles
To ensure that the metrics, log, codedeploy agent work correctly assign to EC2 instance a role with the following permssion:

## Role for custom metrics
Cloudformation yaml format:

```yaml
- PolicyName: metrics
  PolicyDocument:
  Version: '2012-10-17'
  Statement:
  - Effect: Allow
    Action:
    - cloudwatch:PutMetricData
    - cloudwatch:GetMetricStatistics
    - cloudwatch:ListMetrics
    Resource:
    - '*'
```

JSON Format:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "cloudwatch:PutMetricData",
        "cloudwatch:GetMetricStatistics",
        "cloudwatch:ListMetrics"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

## Role for logs
Cloudformation yaml format:

```yaml
- PolicyName: logs
  PolicyDocument:
  Version: '2012-10-17'
  Statement:
  - Effect: Allow
    Action:
    - logs:CreateLogGroup
    - logs:CreateLogStream
    - logs:PutLogEvents
    - logs:DescribeLogStreams
    Resource:
    - arn:aws:logs:*:*:*
```

JSON format

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "logs:DescribeLogStreams"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:logs:*:*:*"
    }
  ]
}
```

## Role for codedeploy
```yaml
- PolicyName: s3-codedeploy
  PolicyDocument:
      Version: '2012-10-17'
      Statement:
      - Effect: Allow
        Action:
        - s3:Get*
        - s3:List*
        Resource:
        - arn:aws:s3:::bucket-name-for-codedeploy-archive/*
```

JSON Format:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "s3:Get*",
        "s3:List*"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::bucket-name-for-codedeploy-archive/*"
    }
  ]
}
```