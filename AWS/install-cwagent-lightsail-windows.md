
## How to install Cloudwatch Agent in AWS LightSail Instance (Windows)

### Create IAM User to be used by CloudWatch agent

1. [Follow this guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-iam-roles-for-cloudwatch-agent.html#create-iam-roles-for-cloudwatch-agent-users) to create a new IAM user and generate access/secret keys

2. [Follow this guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-premise.html#install-CloudWatch-Agent-iam_user-SSM-onprem) to configure the IAM access/secret keys into LightSail instance

### Install and start Cloudwatch Agent

1. RDP into your LightSail instance

2. Download the cloudwatch agent package from below path and install it

```
https://amazoncloudwatch-agent.s3.amazonaws.com/windows/amd64/latest/amazon-cloudwatch-agent.msi
```

3. Go to following path where the agent is installed - `C:\Program Files\Amazon\AmazonCloudWatchAgent`

4. Create a new JSON file as cloudwatch agent configuration `config.json` with following content

```
{
    "metrics": {
        "aggregation_dimensions": [
            [
                "InstanceId"
            ]
        ],
        "append_dimensions": {
            "AutoScalingGroupName": "${aws:AutoScalingGroupName}",
            "ImageId": "${aws:ImageId}",
            "InstanceId": "${aws:InstanceId}",
            "InstanceType": "${aws:InstanceType}"
        },
        "metrics_collected": {
            "LogicalDisk": {
                "measurement": [
                    "% Free Space"
                ],
                "metrics_collection_interval": 60,
                "resources": [
                    "*"
                ]
            },
            "Memory": {
                "measurement": [
                    "% Committed Bytes In Use"
                ],
                "metrics_collection_interval": 60
            }
        }
    }
}
```

5. Start the cloudwatch agent using following command 
```
"C:\Program Files\Amazon\AmazonCloudWatchAgent\amazon-cloudwatch-agent-ctl.ps1" -a fetch-config -m onPrem -s -c file:"C:\Program Files\Amazon\AmazonCloudWatchAgent\config.json"
```

6. Wait for few minutes before the agent starts pushing metrics to CloudWatch console

### View the Metrics in CloudWatch

1. Open the [CloudWatch console](https://console.aws.amazon.com/cloudwatch/)
2. In the navigation pane, under Metrics, choose All Metrics
3. In the Custom namespaces section, choose CWAgent
4. Sort by ImageID, InstanceType, instance, objectname, or InstanceID.