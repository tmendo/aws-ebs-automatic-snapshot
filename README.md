Bash script for automatic EBS volume snapshots on Amazon Web Services (AWS)
===================================

forked from https://github.com/CaseyLabs/aws-ec2-ebs-automatic-snapshot-bash

What is different from the original one?
-------

- limited to a specific instance ID
- snapshots retention changed to 31 days
- less permissions for the IAM user
- added filter to limit to the user own snapshots (probably not needed)

Also, I had an excuse to play a little bit with github :)


How it works
-------

manageSnapshot will:
- Gather a list of all volume IDs attached to the specific instance
- Take a snapshot of each attached volume
- Mark the snapshot with a tag for easy identification
- Delete all marked snapshots (taken by the script) that are older than 31 days



Instalation
-------

- create an IAM user
- set policy for IAM user
- install aws cli
- set up cron for the script
- chmod +x the script

Create an IAM user and set (at least) the following policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt11111111",
            "Effect": "Allow",
            "Action": [
                "ec2:CreateSnapshot",
                "ec2:DeleteSnapshot",
                "ec2:DescribeSnapshots",
                "ec2:CreateTags"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```
At the time of writing it was not possible to grant resource level permissions to these operations, hence the *


For the cron job you can use something like this:
```
cat /etc/cron.weekly/aws-auto-backup

#!/bin/bash

set -e

su - ubuntu -c '/home/ubuntu/bin/manageSnapshot'
```

