---
title: "Git Exp."
date: "2013-04-08"
categories: 
  - "linux-admin"
tags:
  - "Git"
---

Supposing that you had a git server, we can use exist git as your own working bare.  
**[On your own git server] Create a repo.** Clone an exist git.

$ git clone git@exist-server:exist.git

Make its master branch writable.

$ echo -e "\[receive\]ntdenyCurrentBranch = ignore" >> exist.git/.git/config

  
**[On your client] Create a branch & make a change.** Clone exist.git as your own git-src, in which you can see the old commits and branch.

$ git clone git@your-server:exist.git
$ cd exist

We create a new branch based on its master.

$ git checkout -b mybranch
$ echo "Add README" > README
$ git add README
$ git commit -m "Add READEME in ROOT"

Since this is our first commit, we should push our branch to origin. Next time you should just type 'git push'

$ git push origin mybranch

  
**\[On your own git server\] An update in master.** Let's get some new commits.

$ git pull

Here we get something like this... [![git2](/blog/images/git21.png)](http://69.164.197.168/wp-content/uploads/2013/04/git21.png)  
Now we get commit0 and commitA on mybranch, with commit0 and commit1 on branch master. What we want is something like this. [![git3](/blog/images/git3.png)](http://69.164.197.168/wp-content/uploads/2013/04/git3.png)  
**\[On your client\] Merge commit1 to mybranch** Way 1.Just merge them from your own git server

$ git pull origin master

Way 2.Pull commit1 to local master, then rebase **_or_** merge it to mybranch Merge:

$ git merge master

Rebase:

$ git rebase master

Final step:

$ git push

  
_There's a little difference between merge and rebase in history. Reference: [http://gitbook.liuhui998.com/4\_2.html](http://gitbook.liuhui998.com/4_2.html "GitBook - Rebase")_
