---
tool: aws_cli
name: AWS Command Line Interface (CLI)
excerpt_separator: <!--more-->
--- 

The [AWS Command Line Interface (CLI)](https://aws.amazon.com/cli/) is a unified tool to manage AWS services. Using the tool you can control multiple AWS services from the command line and automate them through scripts.
<!--more-->
# DynamoDB

## List tables

The command below will return a list of the tables in the DB at the end of the url.

{% highlight powershell %}

    aws dynamodb list-tables --endpoint-url http://localhost:4569   
{% endhighlight %}

