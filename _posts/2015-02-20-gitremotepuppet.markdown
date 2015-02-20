---
layout: post
title:  "GIT Push and Puppet Master"
date:   2015-02-20 16:00:00
categories: DevOps puppet git
tags: DevOps puppet git
author: patrick
description: "How to set up a remote git repo for puppetmaster"
share: true

---

## Goal: Be able to remote push to our puppetmaster using git

_Note: I'm assuming you have passwordless ssh set up to make this all really easy_

#####Setting up the bare repo:

1. Clone your puppet repo down to your local machine (normal git clone)
2. Create a local bare clone of that repo 
```
git clone --bare my_local_clone bare_clone.git
```
3. copy that bare clone to the remote server 
```
scp -r bare_clone.git user@git.example.com:/opt/git
```
##### Set up the git remote server

1. Set up the new remote on your local machine (inside your local repo full clone):

```
git remote add NAMEOFREMOTE username@remoteserver:/opt/git
```

2. On the remote, add this following file as `./hooks/post-receive` and `chmod 755` it:

```
    #!/usr/bin/env ruby
    # post-receive

    #From: http://krisjordan.com/essays/setting-up-push-to-deploy-with-git

    # 1. Read STDIN (Format: "from_commit to_commit branch_name")
    from, to, branch = ARGF.read.split " "

    # 2. Only deploy if master branch was pushed
    # Uncomment to ensure this only happens on master branch
    # if (branch =~ /master$/) == nil
    #     puts "Received branch #{branch}, not deploying."
    #     exit
    # end

    # 3. Copy files to deploy directory
    #you can change the deploy_to_dir to whatever is needed

    #deploy_to_dir = File.expand_path('../deploy')
    deploy_to_dir = '/etc/puppet/'


    `GIT_WORK_TREE="#{deploy_to_dir}" git checkout -f #{branch}`
    puts "DEPLOY: #{branch} (#{to}) copied to '#{deploy_to_dir}'"

    # 4.TODO: Deployment Tasks
    # i.e.: Run Puppet Apply, Restart Daemons, etc

```


##### Push to remote server

1. Push the master branch to the remote server
```
git push NAMEOFREMOTE master
```

##### How to test without needing to commit to local clone

1. get the last two commit ids on the remote bare clone 
```
git log -2 --format=oneline --reverse
```
2. now run the script (grad the two git hashes from the output of the previous command) (on the remote server inside the bare clone again)
```
echo "$FROM_ID $TO_ID master" | ./hooks/post-receive
```


##### Future to do:
- Have it so that when puppet master repo gits a merge CI kicks in (not sure what those tests look like or how to run)
- When tests pass, have it auto deploy to puppetmaster



####References
1. [Git Documentation](http://git-scm.com/book/ch4-2.html)

2. [Setting up push to deploy with git](http://krisjordan.com/essays/setting-up-push-to-deploy-with-git)
