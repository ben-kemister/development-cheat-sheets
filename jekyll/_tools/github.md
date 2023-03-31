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

## Token authentication

Sometime in 2021? Github stated to enforce the use of tokens for CLI and API calls, see [here for more info](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/).

What this means is that you can no longer use a simple git command line client and username/passwords to pull/push to git, you need to setup [personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token), a credential manager or use another application to interact with Github.

### Setup for Raspberry Pi's

Given the above authentication restrictions, one method of interacting with Github on a Raspberry Pi is to [install the Github cli](https://github.com/cli/cli/blob/trunk/docs/install_linux.md), then authenticate with Github using `gh auth login`.

Following that you can then interact with GitHub using your *standard* git cli client.

See [Caching your GitHub credentials in Git](https://docs.github.com/en/get-started/getting-started-with-git/caching-your-github-credentials-in-git) for more information.

## Troubleshooting

### Error "Your push would publish a private email address"

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