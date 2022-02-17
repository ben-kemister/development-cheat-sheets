---
name: (Windows) PowerShell
language: powershell
---

PowerShell is a task automation and configuration management framework from Microsoft, consisting of a command-line shell and associated scripting language.
<!--more-->

# Environmental Variables

Environment variables are global settings for your Linux, Mac, or Windows computer, stored for the system shell to use when executing commands. For more information see [HowTo: Set an Environment Variable in Windows - Command Line and Registry](http://www.dowdandassociates.com/blog/content/howto-set-an-environment-variable-in-windows-command-line-and-registry/).

## Print Environmental Variables

To list all environment variables.

{% highlight powershell %}

    PS > Get-ChildItem Env:
   
{% endhighlight %}

To print a particular environment variable:

{% highlight powershell %}

    PS > echo $Env:ProgramFiles
    C:\Program Files
   
{% endhighlight %}

## *Temporary* variables

You can set a *temporary* environmental variables using the following command:

{% highlight powershell %}

    PS > $Env:FOO = "hello world"
    PS > $Env:FOO
    hello world
   
{% endhighlight %}

YOu can Remove/Clear a *temporary* variable

 {% highlight powershell %}

    PS > $env:SLS_DEBUG = ""
    PS > $Env:SLS_DEBUG
    PS >

{% endhighlight %}

## *User* variables

You can set user environmental variables using the following command:

{% highlight powershell %}

    PS > setx GLOBAL_AGENT_HTTP_PROXY 'http://userId:password@proxy:8080'
    PS > $Env:GLOBAL_AGENT_HTTP_PROXY
    http://userId:password@proxy:8080
   
{% endhighlight %}
