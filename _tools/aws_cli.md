---
tool: aws_cli
name: AWS Command Line Interface (CLI)
--- 

The [AWS Command Line Interface (CLI)](https://aws.amazon.com/cli/) is a unified tool to manage AWS services. Using the tool you can control multiple AWS services from the command line and automate them through scripts.
<!--more-->
# Configuration

## Quickly Configuring the AWS CLI
If you just want to use some simple commands, maybe with something like [localstack](https://github.com/localstack/localstack), then you can use the `aws configure` command:

{% highlight powershell %}

    PS> aws configure
    AWS Access Key ID [None]: accessKeyId
    AWS Secret Access Key [None]: secretAccessKey
    Default region name [None]: ap-southeast-2
    Default output format [None]:
    PS>
{% endhighlight %}

# DynamoDB

## List tables

The command below will return a list of the tables in the DB at the end of the url.

{% highlight powershell %}

    aws dynamodb list-tables --endpoint-url http://localhost:4569   
{% endhighlight %}

# SQS (Simple Queue Service)

CLI commands for the SQS can be found at the [cli reference](https://docs.aws.amazon.com/cli/latest/reference/sqs/).

## List Queues

The `aws sqs list-queues` will return a list of the queues available at the given endpoint.

{% highlight powershell %}

    PS> aws sqs list-queues --endpoint-url http://localhost:4576
    {                                                                                                                                                                    
        "QueueUrls": [
            "http://localhost:4576/queue/FileQueue.fifo"
        ]
    }
{% endhighlight %}

## Queue Attributes (including message count)

The `aws sqs get-queue-attributes` will return the attributes for a given queue, including a `ApproximateNumberOfMessages` property.

{% highlight powershell %}

    PS> aws sqs get-queue-attributes --queue-url http://localhost:4576/queue/FileQueue --attribute-names All --endpoint-url http://localhost:4576
    {
        "Attributes": {
            "VisibilityTimeout": "30",
            "DelaySeconds": "0",
            "ReceiveMessageWaitTimeSeconds": "0",
            "ApproximateNumberOfMessages": "0",
            "ApproximateNumberOfMessagesNotVisible": "0",
            "ApproximateNumberOfMessagesDelayed": "0",
            "CreatedTimestamp": "1585549003",
            "LastModifiedTimestamp": "1585549003",
            "QueueArn": "arn:aws:sqs:us-east-1:000000000000:FileQueue",
            "FifoQueue": "false",
            "ContentBasedDeduplication": "true"
        }
    }
{% endhighlight %}

## Purge Messages

The `aws sqs purge-queue` will delete all messages on the queue.

{% highlight powershell %}

    PS> aws sqs purge-queue --queue-url http://localhost:4576/queue/FileQueue --endpoint-url http://localhost:4576
    PS> 
{% endhighlight %}

## Send Message to Queue

The `aws sqs send-message` will add a message to the queue.

{% highlight powershell %}

    PS> aws sqs send-message --message-body "{}" --queue-url http://localhost:4576/queue/FileQueue --endpoint-url http://localhost:4576
    {
        "MD5OfMessageBody": "99914b932bd37a50b983c5e7c90ae93b",
        "MessageId": "d9f0cade-1427-4950-81db-cbe14e5df786"
    }
{% endhighlight %}
