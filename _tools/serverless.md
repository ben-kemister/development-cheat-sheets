---
tool: serverless
name: Serverless
excerpt_separator: <!--more-->
--- 

[Serverless](https://serverless.com) is a framework which assist with building and running serverless functions (i.e. lambda).
<!--more-->
# View Debug Logs

If something goes wrong when you are trying to run `serverless` it will advise 

`For debugging logs, run again after setting the "SLS_DEBUG=*" environment variable.`

I found it a little hard to find out how to do this on Windows, it turns out you need to run the following command in powershell

{% highlight powershell %}

   $env:SLS_DEBUG="*"
   
{% endhighlight %}

# Environmental Variables

You can set `serverless` environmental variables in the `serverless.yml` file in two places, in the `functions` section or the globally in the `provider` section.

## Function Environment variables

{% highlight yml %}

   service: service-name

   provider:
   name: aws
   stage: dev

   functions:
   hello:
      handler: handler.hello
      environment:
         SYSTEM_URL: http://example.com/api/v1

{% endhighlight %}

## Global Environmental variables


{% highlight yml %}

provider:
  name: aws
  stage: dev
  environment:
    SYSTEM_ID: jdoe

functions:
  hello:
    handler: handler.hello
    environment:
      SYSTEM_URL: http://example.com/api/v1

{% endhighlight %}
