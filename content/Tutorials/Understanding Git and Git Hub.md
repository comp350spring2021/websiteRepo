---
title: Git and GitHub
---



{{< toc >}}

## Git and GitHub are not the same thing

First lets get into what git and GitHub are and what the differences are.

### Git

Git is a command line version control software that lets you track changes in your code through something called commits. Say you are working on a project, and you get to a point where your code works but you want to implement more features. You may want to save your project at this state, so you can go back to it. Well with git, you can make something called a **commit** that saves your work at that point.

Then you can go on and continue with your project and keep implementing features and committing the changes as you successfully complete features. However you've reached a point where the feature you've implemented breaks your program, and you want to go back to when your project worked. This is the beauty of git, you can revert back to one of your previous commits. There are other features within git as we will  **git** into later.

### GitHub

Github is for collaboration between developers.

Lets take the previous scenario where you are working on your project and you decide you need help. You can email all your source code to everyone else you want to work with, OR you can upload to GitHub and allow anyone who wants to add features to your project to **clone** your code on their own machines at home.

## Lets Start With the Basics of Git

First make sure you have Git,  This guide is written for those using Linux or Mac OS. If you're on windows you will need to download git, but the commands will be the same.

Check what version you have with:

```
Git --version
```

If you get an output  with a version number you have git installed, on mac if your don't have git already it will prompt you to install it. On Linux you will need to  use `sudo apt-get install git all` or use your package manager for your given distro.

### git init

Before you can do anything with git, you have to initialize the repository. Lets make a fake project.

![gitInit](/images/gitInit.png)

In this picture you can see that I have created a project that lives in the directory `my-git-project` with one python file that prints hello world., and I have initialized the repository. Git can now be used within this folder and will track the changes to all files within the directory.

### git add

Now that we have the repository set-up we can  start adding files to the staging area. When using git, every-time you make changes to a file git tracks those changes, and before we can make a commit we mst put the files into the staging area with `git add ` 

![gitaddgitstatus](/images/gitaddgitstatus.png)

In this picture you can see that I added the `helloWorld.py` to the staging area with `git add *` the `*` just means all the files that have been edited since the last commit or in this case since I initialized.

Then I used the command `git status` to see what files were in the staging area and you can see that `helloWorld.py` is not in the staging area. However after I created the `newFile.py`  and used `git status again`  you can see that `newFile.py` is NOT in the staging area because I did not use `git add ` to place it in the staging area.

Notes:

* You don't need to add all files to the staging area, there are other options that can be used to add single files to the staging area with `git add <filename>` 
* to remove files from the staging area use `git rm` 

### git commit, git log

git commit is the bread and butter of git. This basically lets us take a "snapshot" of our code at that moment of time. kind of like saving but we can revert back to this save spot later. lets use the command `git commit -m ` to commit our code to the master branch.

note: the `-m` stands for message, this allows us to attach a message to the commit, try to be descriptive with your messages.

![gitCommit](/images/gitCommit.png)

Above I committed the `helloWorld.py` , then added  `newFile.py` to the staging area and committed that file as well.

![addedLines](/images/addedLines.png)

![gitlogaddedlines](/images/gitlogaddedlines.png)

Now Ive added some code to `newFile.py` and committed the changes , but lets say that for some reason that I didn't want these changes anymore, because Ive decided to take the project in a new direction and I know that at the second commit I  had a clean `newFile.py` 

### git reset, git checkout

Notice in the picture above, each commit has a long string of characters next to it. These are unique hashes for each of our commits, you can think of them like nodes along the chain representing a previous "save" state. Say we wanted to get rid of the code that we wrote in the `newFile.py`.  To do that first we need to a `git reset` 

![gitReset](/images/gitReset.png)

Notice that when we do a git reset, we use the hash of the commit that we want to move to. However we need to do one more thing. If you were to open up `newFile.py` you might expect an empty folder with no code, however this is not the case, when I open up the new file my code still looks like this:

```python
print("is a new file")
print("im gonna remove these changes")

```

* git reset can be a dangerous command because it completely gets rid of the all the commits after the one you reset it to. This is why it does not change the files, or in this case why the lines of code were still in `newFile.py` after it was reset. For example say you did the reset on accident or reset to the wrong commit, you could `git ad .` then `git commit -m` to not completely lose the code you wrote.  To actually change the files within your working directory keep reading.

We need one more step to change the files in the working directory, the `git checkout`, a couple things to note  about git checkout here:

* After this all the changes to `newFile.py` were gone and because it was an empty file before the commit, its empty again now

* Notice that I used the hash that the HEAD was currently out

* I specify what files I wanted to change with `-- newFile.py`, if you forget this you will get into a detached head state which can be hard to fix

  

![git-checkout](/images/git-checkout.png)

NOTE: to do both of the above in one step do `git resest <hash> --hard` . More dangerous this way but faster.

### git revert

git revert is another way to  revert your code back to the following. Im gonna make this part quick, heres the changes i made:

* `helloWorld.py` changed to look like this:

  ```python
  for i in range(5):
  	print("hello world")
  
  
  ```

* Committed changes to `helloWorld.py`

* `newFile.py` looks like this:

  ``` python
  print("hey im new file")
  ```

* committed changes to `newFile.py`

my git log after all of the following now looks like this:

![git-revert-log](/images/git-revert-log.png)

lets revert to the  `3fdcdd2` hash with `git revert 3fdcdd2` and we have the following `git log` :

 ![git-revert-2](/images/git-revert-2.png)

Notice that the commits aren't gone with revert, but instead another is added with the message "revert ...." this is because we are simply undoing the commit at that hash so my files now look like this:

`newFile.py`

```python
print("hey im new file")

```



`helloWorld.py`

```python
print("hello world")

```

Notice that the changes I made to the `newFile.py` stayed while the changes I made to the `helloWorld.py` were undone. this

## git branch

branching is where the real power of git really lies, This allows you to take your project in different directions without messing up other branches. Up until now we've been working with git in a very linear fashion, lets try branching.

### Creating a branch

use the following commands to create a branch and witch to the branch. Lets pretend we've had an idea for a new feature.

`git branch myNewFeature`

`git checkout myNewFeature` 

Now I have the following `git log --oneline`:

![git-branch-1](/images/git-branch-1.png)

you can see here that the HEAD points at the new branch `myNewFeature` . Anything we did before on the master branch we can now do on our new branch. for example, lets create a new feature file.

![newfeatureadded](/images/newfeatureadded.png)

above you can see that our new feature has been added to the new branch. If we like the new feature, and are ready to commit it to the master branch we can merge them together.

 ### Merging

Because we may have multiple branches we may want to see all of the branches currently open. To do that use `git branch`. Notice the star, that tells you what branch you are currently on.

![viewingBranches](/images/viewingBranches.png)

Now on to merging `myNewFeature` with `master`. First you need to switch to the master branch, then merge the desired branch into master.

![mergedBranches](/images/mergedBranches.png)

You will notice here that the `myNewFeature` branch is still there, you can either keep developing on that branch or delete it with `git branch -d myNewFeature` 

## GitHub

Now lets move on to GitHub.

The core functionality of GitHub lies with its ability to allow you to fork other peoples repositories, and then start working on them on your own machine. were going to do a quick tutorial on how we can fork our submitter code.

### Forking 

Go to the submitter repository shown below.

![githubbranches](/images/githubbranches.png)

Then go ahead and click the fork button and choose to fork the project to your own personal machine. Ive already done so, which is why you can see the little one by the fork button in the top right.

Now I need to go to my forked repository on my own personal GitHub account to clone the  repo to my computer to get to work.

* notice the forks on the left menu, when we bring the repository onto our own machines all of these branches will exist.

### git clone

There are multiple ways to clone a repository to your machine, I will show you how using git and the terminal.

![githubclone](/images/githubclone.png)

you can see that I am on my personal GitHub looking at my forked version of the Comp350-Submitter repo, and notice that little link underneath clone, thats the link I am going to use with GitHub to clone the repository, shown below.

![gitcloneterminal](/gitcloneterminal.png)

Because we cloned this repo from GitHub it is already a git repository, so there is no need to use `git init`. Cd into the project folder wherever you cloned it, and do a `git branch -a`

![projectBranches](/images/projectBranches.png)

notice that all of the branches exist, but these are known as remote branches, you cant  make commits to these branches. We need to make our own local branch, that tracks a remote branch. I am part of the front end team so I did the following:

![gittracking](/images/gittracking.png)

![brancheswithtracking](/images/brancheswithtracking.png)

Now I have my own front end branch to work so lets try to make some changes.

### git push

![readmechange](/images/readmechange.png)

After committing these changes to my local front end branch I was able to push them to my forked version of the GitHub repository, on my personal GitHub account, with `git push`

![pushingrepo](/images/pushingrepo.png)

and here are the changes on the GitHub repo

![githubshowing changes](/images/githubshowing changes.png)

If I am really happy with my changes I can go and create a pull request for my changes to be put into the official submitter repository. To do this got to the submitter repository on the organization and  hit **new pull request**, and compare across forks. you should get a page that looks like this:

![pullrequest](/images/pullrequest.png)

I can then hit create pull request and someone will review my changes and either confirm or accept them. If they accept then the changes will be added into the organizations code base.