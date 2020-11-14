---
layout:     post
title:      Git Command Cheat Sheet
subtitle:   
date:       2020-10-21
author:     yg
header-img: img/post-bg-5.jpg
catalog: true
tags:
    - Tools
---


### OVERVIEW
SVN and Git are both version control tool with some differences: SVN is a centralized version control tool as Git is distributed. More specifically, SVN acts like master-slave model. There will be a server running at somewhere in the world, and developer as a client has to checkout and commit the code from the server. For Git, there is no centralized server to manage the version. Instead, every developer runs Git tool on their device: every developer manage their changes on local environment (by Git) and only 'commit' their changes to remote repo when needed. 

### Basic Git Command
| Command                                      |  Comments                                                   |
|----------------------------------------------|-------------------------------------------------------------|
| git branch -a                                | check all branches, including remote branches               |
| git clone -b <remote_branch_name> <Repo_URL> | check out a specific branch <remote_branch_name>            |
| git diff --name-only                         | only show file name                                         |
| git diff --cached                            | diff that added                                             |
| git status                                   | show status of git                                          |
| git ls-files                                 | list files that git tracks                                  |
| git reset                                    | undo added                                                  |
| git commit --amend                           | rewriting the most recent commit message                    |
|----------------------------------------------|-------------------------------------------------------------|   
| git checkout -b <branch_name>                | create a new branch AND move to new branch at the same time |
| git merge <branch_name>                      | merge a branch into current branch                          |
| git rebase <branch_name>                     | rebase current_branch into top of <branch_name>             |

