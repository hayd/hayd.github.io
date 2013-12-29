---
layout: post
title:  "Another git(hub) workflow"
date:   2013-04-19 22:05:17
categories: git github
---

Another git(hub) workflow[^1], along with some cheatsheet commands:

######[Rules](#rules):

1. [Populate upstream with pull requests](#only_pull_requests)
2. [Update local master from upstream](#master_is_upstream)
3. Update branches with rebase

######[Branch management](#branch_management):

- [Create a new branch](#create_new_branch)
- [Make a pull request](#make_pull_request)
- [Update branches](#update_branches)
- [Resolve merge conflicts](#merge_conflicts)
- [Tidying a branch (squashing commits)](#tidying)
- [Force pushing to github](#force_push)

######[Advanced](#advanced):

- [Add commits to another person pull request](#add_to_pull_request)
- [Troubleshooting](#trouble)
  - [Whoops: I've commited to master](#commited_to_master)
  - [Whoops: I've merged to local master](#merged_local_master)

######[Configuring git](#configure_git):

- [Initial set up](#init)
- [Managing remotes](#manage_remotes)
- [Abbreviating git commands (aliases)](#abbr)
- [Miscellaneous config option](#config-misc)

-----

<span id="rules"></span>
##Rules:

<span id="only_pull_requests"></span>
### Populate upstream with pull requests

#####Never push directly to upstream

Make use of github's pull request (to upstream):

- alerts everyone in the team that a merge is being requested
- a good oportunity for a peer review, and to keep everyone up to date with what you are doing
- it can be easily tested by everyone in the team (see manaing remotes for a trick here)

Once merged:

- everyone can see in the topology of the network that something been merged, who did it and who had a checked it.
- everyone can see your comments and discussion on the branch, so it's a good idea to be verbose.

*Note: many teams do push small commits directly upstream, and this does lead to a slightly cleaner network. However, I think it is good practice, and easier to set a blanket policy (which in practice this means: only do so if someone has borked something and you know what you're doing, see troubleshooting).*

<span id="master_is_upstream"></span>
### Update local master from upstream:
##### Your local master is a copy of upstream

*Everyone* has a backup of upstream from the last time they grabbed it

{% highlight sh %}
git checkout master  # got to the master branch
git fetch upstream  # get the latest
git merge upstream/master --ff-only  # it's important you're in your master branch
{% endhighlight %}

Note: the `--ff-only` (fast forward only) ensures the commits are added in the same order as upstream. This should be superfluous: so if it complains then you've done something wrong.

Usually you would push straight to origin (your gihub fork):

{% highlight sh %}
git push origin
{% endhighlight %}

*This means everyone can see the last time you fetched from upstream.*

<span id="branch_management"></span>
##Branch management

<span id="create_new_branch"></span>
### Create a new branch

For each **topic** of work, create a new branch (off of the the latest master).

{% highlight sh %}
git checkout master
git checkout -b describe-work-doing
# do some work
git commit -a -m 'describe what is in this commit'
git commit ...
git push origin describe-work-doing  # backup to github (for pull request)
{% endhighlight %}

##### Unrelated features/bugfixes in different branches

Note: It's a subjective call precisely what a "topic" is. For example, `add_feature_a` would be a good choice, but `my_days_work` would be terrible.

It might be you make an immediate pull request, or it could be that this particular job takes a while (see updating your branches), or you want to tidy up the commits (see squashing commits).

<span id="make_pull_request"></span>
### Making a pull request

Visit your branch on the github page and make a pull request. [^2]

- Add a concise desciption of the changes which you have made (link to any bugs which you have fixed)
- Mention to developers if they need to do anything once to see this change re-compile, db:migrate etc
- At mention developers whose opinion you would like

<span id="update_branches"></span>
### Updating your branches:

Once you've updated your local master (from upstream), you should update your other branches to see these changes, and ensure your code still works:

{% highlight sh %}
git checkout branch-name
git rebase master
{% endhighlight %}

Note: rebase rewinds your commits, applies the commits/updates from the latest master then reapplies your commits.  
*Some people favour merging here, but for one thing you get a cleaner (easier to reason about) network when rebasing.*

##### Update branches regularly

This means conflicts (if there are any) are resolved as they appear, rather than in one massive hit much later. *If you're having trouble you can ping the other dev while the change is fresh in their mind.*

There may be [merge-conflicts](#merge_conflicts) at this stage (if so, then you wouldn't have been able to merge in with the upstream codebase anyway, so it's usually best resolve them locally).

When pushing to github you'll need to do a [force update](#force_push).

<span id="merge_conflicts"></span>
### Resolving merge conflicts

This isn't usually too bad, if unsure cancel the rebase, see instructions in terminal...

##### Back up (optional)

If it's a particularly large update/merge conflict, it's often useful to create a backup branch with your changes as they are currently (before the conflict):

{% highlight sh %}
git rebase --abort  # give up on the rebase
git branch feature_x_backup  # make a backup branch
{% endhighlight %}

Attempt the rebase, and test that it's still working then you may delete the branch.


##### Warning

Don't use "yours"/"theirs". You'll lose code. Very often you'll have both made edits to the same file, and will want to keep them both.

##### Resolving

TODO

Resolve each file, and once resolved, mark this file as resolved (via `git add file_name`).


<span id="tidying"></span>
### Tidying a branch (squashing commits)

First check how many commits you want to squish (you'll have a chance to review later, but check with `git log`). Suppose you want to squash the last 3 commits [^3]:

{% highlight sh %}
git rebase -i HEAD~3
{% endhighlight %}

*this will open editor, change all but first pick to squash, and change the commit message. I promise this will make more sense once you've done it a few times...*

If you're squashing commits on a branch already on github, just like with normal rebasing, you'll need to [force update](#force_push).

<span id="force_push"></span>
###Force pushing to github

Here be dragons, use caution:

- If there are others *using* you branch (i.e. doing work off it), consider not doing this (THEN WHAT?)
- If you have a pull request open, ensure it won't be merged by another developer (close it or simply comment a warning saying you are rebasing)

Since you are rewriting history you need to force the push:

{% highlight sh %}
git push -f origin feature_x
{% endhighlight %}

Note: a github pull request will be updated with the new commits (and the old ones will be deleted).

<span id="advanced"></span>
##Advanced

<span id="add_to_pull_request"></span>
### Adding commits to a pull request

Usually you want to avoid this, but sometimes collobations is sometimes a necessary evil (good thing?).

*It's usually best to tell the developer that you're going to do something from their pull request (see [force push](#force_push)). At the very least, comment on the pull request to let them know.*

If you've set up pull requests, then checkout theirs (where `#` is the number from github) and make yourself a new branch from it:

{% highlight sh %}
git checkout upstream/pr/#
git checkout -b descriptive_branch_name
# do some work
{% endhighlight %}

You can now treat this as a regular branch (remember to close the old pull request). If the old pull request is merged it will cause problems.

<span id="trouble"></span>
### Troubleshooting

TODO
<span id="commited_to_master"></span>
##### Whoops: I've commited to master

Just make a branch from here, and continue working:

{% highlight sh %}
git checkout -b branch_name
{% endhighlight %}

To fix your master you'll have to delete the last (few?) commits:

{% highlight sh %}
git checkout master
git log  # see how many commits since the last pull request
git reset --HARD HEAD~3  # delete last 3 commits
{% endhighlight %}

and then [update from upstream](#master_is_upstream).

Note: Provided you do this in master, and have backed up your branch deleting the last few commits shouldn't be *too* dangerous. Nevertheless, breath holding is mandatory.

<span id="merged_local_master"></span>
##### Whoops: I've merged into my local master

We essentailly do the same as [above](#commited_to_master), that is, delete the last few commits and update from upstream.

<span id="configure_git"></span>
##Configuring git:

<span id="init"></span>
### Initial set up

If your the first person in the team to set up:

- Create a git repository for your work
- Create a (private or public) project github repository[^4], this should use an email different from any of the developers.
- Set up remote upstream, and (just this time) push directly to this (`git push upstream`).

Everyone else

- fork from the main project repository[^5].
- clone (from this fork!)
- Set up remotes (e.g. upstream)

<span id="manage_remotes"></span>
### Managing remotes

##### Add collaborators repos

We should have upstream as team/project, I like to inspect Bob's repo at bob/project (look up the url from their  github page):

{% highlight sh %}
remote add bob https://github.com/bob/project.git
{% endhighlight %}
    
Just like we do for upstream, I can have a look at what he's been doing (if he needs some advice):

{% highlight sh %}
git checkout bob/feature_y
{% endhighlight %}

##### Fetch all pull requests

For more easy checking of pull requests before merging, you can append:

{% highlight sh %}
[remote "upstream"]
  fetch = +refs/heads/*:refs/remotes/upstream/*
  fetch = +refs/pull/*/head:refs/remotes/upstream/pr/*
{% endhighlight %}


to your `.gitconfig` file. This means that when you fetch from upstream, it downloads the branches of every pull request as `upstream/pr/#` (where `#` is the pull request number from github).

{% highlight sh %}
git checkout upstream/pr/#
# test things
{% endhighlight %}

<span id="abbr"></span>
### Abbreviating git commands (aliases)

I like to save some typing and use the following aliases (add these to your `.gitconfig` file):

{% highlight sh %}
[alias]
  r = rebase
  f = fetch
  co = checkout
  c = commit
  a = add
  p = push
  s = status
  b = branch
  h = help
  hist = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short
{% endhighlight %}

Which enables you to write commands more concisely.

<span id="config-misc"></span>
### Miscellaneous
Add some colour, by adding this to your `gitconfig`:

{% highlight sh %}
[color]
    ui = true
{% endhighlight %}


 
[^1]: [Github-flow: How github manage their workflow](http://scottchacon.com/2011/08/31/github-flow.html)
 
[^2]: [Using pull requests](https://help.github.com/articles/using-pull-requests)

[^3]: [Rewriting hitory](http://git-scm.com/book/en/Git-Tools-Rewriting-History#Changing-Multiple-Commit-Messages)

[^4]: [Create a new repo](https://help.github.com/articles/creating-a-new-repository)

[^5]: [Fork a repo](https://help.github.com/articles/fork-a-repo)