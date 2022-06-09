---
tool: git
name: git
tags:
 - git
--- 

Git  is a distributed version-control system for tracking changes in source code during software development. It is designed for coordinating work among programmers, but it can be used to track changes in any set of files. Its goals include speed, data integrity, and support for distributed, non-linear workflows.
<!--more-->
## Handy Commands

| Command                             | Description                                      |
| ----------------------------------- | ------------------------------------------------ |
| `git remote -v`                     | Prints the remotes for this git repo             |
| `git commit --amend --reset-author` | Reset the author information on your last commit |

## Troubleshooting

### git checkout/pull returns 'error: invalid path'

This is caused by git complaining about invalid paths, typically encountered on Windows.

The workaround is to set the git config flag: `git config core.protectNTFS false`

    Note: core.protectNTFS

    If set to true, do not allow checkout of paths that
    would cause problems with the NTFS filesystem, 
    e.g. conflict with 8.3 "short" names.
    Defaults to true on Windows, and false elsewhere.

