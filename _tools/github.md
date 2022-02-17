---
tool: github
name: github
title: Github
tags:
 - git
 - github
--- 

Github is a popular public Source Code Repository (SRC) which was recently purchased by Microsoft?.
As the name suggests Github uses [git]({{ '/tools/git.html' | relative_url }}) for use of the repository.
<!--more-->
# Error "Your push would publish a private email address"

I found this solution on [StackOverFlow](https://stackoverflow.com/questions/43863522/error-your-push-would-publish-a-private-email-address).

1. Find your GitHub noreply address in your GitHub's Personal Settings → Emails. It's mentioned in the description of the Keep my email address private checkbox. Usually, it starts with a unique identifier, plus your username.

2. Set your email address for the single repository

``` bash
git config user.email "{ID}+{username}@users.noreply.github.com"
```

3. (Optional) Set your email address for **every** repository on your computer

``` bash
git config --global user.email "{ID}+{username}@users.noreply.github.com"
```

4. Reset the author information on your last commit

``` bash
git commit --amend --reset-author
```