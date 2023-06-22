# **TABLE OF CONTENTS**

* [1 - Getting Started](#getting-started)
	* [1.1 - About Version Control](#about-version-control)
	* [1.2 - What is Git?](#what-is-git)
	* [1.3 - First Time Git Setup](#first-time-git-setup)
	* [1.4 - Getting Help](#getting-help)
* [2 - Git Basics](#git-basics)
	* [2.1 - Getting a Git Repository](#getting-a-git-repository)
	* [2.2 - Recording Changes to the Repository](#recording-changes-to-the-repository)
	* [2.3 - Viewing the Commit History](#viewing-the-commit-history)
	* [2.4 - Undoing Things](#undoing-things)
	* [2.5 - Working with Remotes](#working-with-remotes)
* [3 - Git Branching](#git-branching)




# GETTING STARTED

# About Version Control

* Version control is a system that records changes to a file or set of files over time so that you can recall specific version later. 

## Local Version Control Systems

* Brute Force Approach - 
	* Time-stamped directories are set in place and files are copied into them. 
	* Pros:
		* It is a simple approach
	* Cons:
		* Error prone as you might forget which directory you are in and accidentally write to the wrong file
		* Copy files you didn't mean to.

* Local Version Control System - Simple database that kept all the changes to files under revision control. For example, RCS (kept patch sets (difference between files) in a special format on disk so as to re-create what any file looked like at any point of time by adding up all the patches).
	* Cons:
		* Single point of failure - Since the entire history of the project is in a single place, you risk losing everything.

		* Difficult to administer as each contributor has its own database

![Local Version Control Diagram](image-5.png)

## Centralized Version Control Systems

* People found the need to collaborate with developers on other systems. For example, CVS, Subversion, Peforce. They have a single server that contains all the versioned files, and a number of clients that check out files from that central place. 

* Pros:
	* Everyone knows to a certain degree what everyone else on the project is doing.
	* Administrators have fine-grained control over who can do what
	* Easier to administer a CVCS than to deal with local databases on every client.

* Cons:
	* Single point of failure that the centralized server represents - If that server goes down for an hour, then during that hour nobody can collaborate at all or save versioned changes to anything they are working on. 
	* If the hard disk the central database is on become corrupted, and proper backups are not kept, you lose absolutely everything.

![Centralized Version Control Diagram](image-6.png)

## Distributed Version Control System

* Clients don't just check out the latest snapshot of the files, rather they fully mirror the repository, including its full history. 

* Pros:
	* If any server dies, and these system were collaborating via that server, any of the client repositories can be copied back up to the server to restore it. Every clone is really a full backup of all the data. For example, Git, Mercurial, Bazaar.

![Distributed Version Control Diagram](image-7.png)

# What is Git

* Most systems store information as a list of file-based changes (CVS, Subversion) think of information they stores as a set of files and the changes made to each file over time (`delta-based` version control).

![Storing data as changes to a base version of each file](image-8.png)

* Git thinks of its data as a series of snapshots of a miniature filesystem. With Git, every time you commit, or save the state of your project, Git basically take a picture of what all your files look like at that moment and stores a reference to that snapshot. To be efficient, if files have not changed, Git doesn't store the file again, just a link to the previous identical file it has already stored. Git thinks about its data more like a **stream of snapshots**. 

![Storing data as snapshots of the project over time](image-9.png)

## Nearly Every Operation is Local

* Most operations in Git only need local files and resources to operate. Generally no information is needed from another computer on your network. Whereas if you are using a CVCS, most operations have that network latency overhead.

* For example, to browse the history of the project, Git doesn't need to go out to the server to get the history and display it for you - it simply reads it directly from your local database. This means you see the project history almost instantaneously, instead of having to ask a remote server to do it.

## Git has Integrity

* Everything in Git is checksummed before it is stored and is then reffered to by that checksum. This means its impossible to change the contents of any file and directory without Git knowing it.

* Git uses SHA-1 has for checksumming. This is a 40-character string composed of hexadecimal characters (0-9 and a-f) and calculated based on the contents of the file or directory structure in Git. Git stores everything in its database by the hash value of its contents.

## Git generally Only Adds Data

* Most of the action you do in Git only `add` data to the Git database. Therefore it is hard to get the system to do anything that is not undoable or to make it erase data in any way.

## The Three States

* Git has three main states that your files can reside in: 
	* `modified` - Means that you have changed the file but not have committed it to your database yet. If a file has changed since it was checked out but has not been staged, it is modified
	* `staged` - Means you have marked a modified file in its current version to go into your next commit snapshot. If a file has been modified and was added to the staging area, it is staged. 
	* `committed` - Means that the data is safely stored in your local database. If a particular version of a file is in the Git directory, it is considered committed.

* This leads us to the three main sections of a Git Projects - 
	* The Working Tree - A single checkout of one version of the project. These files are pulled out of the compressed database in the Git directory and placed on the disk for you to use or modify
	* The Staging Area - Is a file, generally contained in your Git directory, that stores information about what will go into your next commit. Its technical name in Git parlance is the "index".
	* The Git directory - Is where Git stores the metadata and object database for your project. It is what is copied when you `clone` a repostory from another computer. 

![Working tree, staging area, and Git directory](image-10.png)

* The basic Git workflow works like this:
	* You modify files in your working tree.
	* You selectively stage just those changes you want to be part of your next commit, which adds `only` those changes to the staging area.
	* You do a commit, which takes the files as they are in the staging area and stores that snapshot permanently to your Git directory.

# First-Time Git Setup

* `git config` - Lets you get and set configuration variables and control all aspects of how Git looks and operates. These variables can be stored in three different places:
	1. `[path]/etc/gitconfig` file: Contains values applied to every user on the system and all their repositories. If you pass the `--system` to `git config`, it reads and write from this file specifically. Because this is a system configuration file, you would need administrative or superuser privilege to make changes to it. 
	2. `~/.gitconfig` or `~/.config/git/config` file: Values specific personally to you, the user. You can make Git read and write to this file specifically by passing the `--global` option, and this affects `all` of the respositories you work with on your system.
	3. `config` file in the Git directory (that is, `.git/config`) of whatever repository you're currently using: Specific to that single repository. You can force Git to read from and write to this file with the `--local` option, but that is in fact the default. You need to be located somewhere in a Git repository for this option to work properly.

* Each level overrides values in the previous level

* You can view all of your settings and where they are coming from using:
```
$ git config --list --show-origin
```

## Your Identity

* This first thing you should do when you install Git is to set your username and email address. This is important because every Git commit uses this information, and it's immutably baked into your commits you start creating:
```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

## Your Editor

* You can configure the default text editor that will be used when Git needs you to type in a message. If not configured, Git uses your system's default editor.
```
$ git config --global core.editor emacs
```

## Your Default Branch name

* By default, Git will create a branch called `master` when you create a new repository with `git init`. To set `main` as the default branch name do:
```
$ git config --global init.defaultBranch main
```

## Checking your Settings

* `git config --list` - If you want to check your configuration settings

* You may see keys more than once, because Git reads the same key from different files. Git uses the last value for each unique key it sees. 

* `git config <key>` - To check what Git thinks a specific key's value is:
```
$ git config user.name
```

# Getting Help

* `git <verb> --help` - You can get the comprehensive manual page (manpage) help for any of the Git commands by:
```
$ git config --help
```

* If you just need a quick refresher on the available options of a Git command, use `-h` option
```
$ git add -h
```

# GIT BASICS

# Getting a Git Repository

## Getting a Git Respository

* You can obtain a Git Repository in one of two ways:
	* You can take a local directory that is currently not version control, and turn it into a Git repository
	* You can `clone` an existing Git repository from elsewhere.

### Initializing a Repository in an Existing Directory

* If you have a project directory that is currently not under version control and you want to start controlling it with Git, you first need to go to that project's directory and type:
```
$ git init
```

* This creates a new subdirectory named `.git` that contains all of your necessary repository files - a Git repository skeleton. At this point, nothing in your project is tracked yet. If you want to start version-controlling existing files (as opposed to an empty directory), you should probably begin tracking those files and an initial commit. You can accomplish this with a few `git add` commands that specify the files you want to track, followed by a `git commit`.

### Cloning an Existing Respository

* `git clone <url>` - If you want to get a copy of an existing Git repository - for example, a project you'd like to contribute to. Instead of getting just a working copy, Git receives a full copy of nearly all data that the server has. Every version of every file for the history of the project is pulled down by default when you run `git clone`. 
```
$ git clone https://github.com/libgit2/libgit2
```

* This creates a directory called `libgit2`, initializes a `.git` directory inside it, pulls down all the data for that repository, and checks out a working copy of the latest version. If you want to clone the repository into a directory named something other than `libgit2`, you can specify the new directory name as an additional argument:
```
$ git clone https://github.com/libgit2/libgit2 mylibgit
```

* Git has a number of different transfer protocols you can use (HTTPS, SSH).

# Recording Changes to the Repository

* Typically, you'll want to start making changes and committing snapshots of those changes into your repository each time the project reaches a state you want to record.

* Each file in your working directory can be in one of two states:
	* **Tracked** - Files that were in the last snapshot, as well as any newly staged files; they can be unmodified, modified, or staged. Trackes files are files that Git knows about.
	* **Untracked** - Everything other than tracked files - Any files in your working directory that were not in your last snapshot and not in your staging area. Git won't start including it in your commit snapshots until you explicitly tell it to do so.

* When you first clone a repository, all of your files will be tracked and unmodified because Git just checked them out and you haven't edited anything. As you edit files Git seems them as modified, because you've changed them since your last commit. As you work, you selectively stages these  modified files and then commit all those staged changes, and the cycle repeats.

![image](https://github.com/rsing148/CommunityClassroom-Git/assets/127006601/41a407bb-45fb-4bc2-b53e-d2bde7f91114)

## Checking the Status of your Files

* `git status` - To get which files are in which state. 

![Git status](image-23.png)

* This means that you have a clean working directory; in other words, none of your tracked files are modified. Git also doesn't see any untracked files, or they would be listed here. The command tells you which branch you are on, and informs you that it has not diverged from the same branch on the server.

* After you add a file to your project, a simple `README` file. If this file didn't exist before and you run `git status`, you see your untracked file under the "Untracked Files" heading in your status output. 

![Git status after adding new Untracked File](image-24.png)

## Tracking New Files

* `git add` - In order to begin tracking a new file. It takes a path or a directory; if its a directory, the command adds all the files in that directory recursively. IT means "add precisely this content to the next commit". For instance, to begin tracking the `README` file run
```
$ git add README
```

* If you run status command again, you can see that your README file is now tracked and staged to be committed. You can tell it's staged because it appears under "Changes to be committed" heading. If you commit at this point, the version of the file at that time you ran `git add` is what will be in the subsequent snapshot. 

![Git status after staging new untracked file](image-25.png)

## Staging Modified Files

* If you change a previously tracked file called `CONTRIBUTING.md` and then run your `git status` command again, you will see the file appears unders a section "Changes not staged for commit" - which means that a file that is tracked has been modified in the working directory but not yet staged. 

![Modified tracked file](image-26.png)

To stage it, you run the `git add` command. 

![Alt text](image-27.png)

After staging the file if we make another change to the same file, it will list as both staged and unstaged. It turns out that Git stages a file exactly as it is when you run the `git add` command. If you commit now, the version of `CONTRIBUTING.md` as it was when you last ran the `git add` command is how it will go into the commit, not the version of the file as it looks in your working directory when you run `git commit`. 

![Modified already staged file](image-28.png)

If you modify a file after you run `git add`, you have to run `git add` again to stage the latest version fo the file.

## Short Status

* `git status --short` command shows changes in more compact way. The output has 2 columns - The left-hand column indicates the status of the staging area and the right-hand column indicates the status of the working tree.

![git status -s](image-29.png)

* Symbols denote:
	* `??` - Untracked files
	* `A` - files that have been added to staging area
	* `M` - Modified files
	* `MM` - Modified, staged and again modified files (so there are changes that are both staged and unstaged)

## Ignoring File

* For files which you don't want Git to automatically add or even show as being untracked (such as log files or files produced by the build system) - You can create a file listing patterns to match them names `.gitignore`.

* Rules for the patterns you can put in the `.gitignore` file are :
	* Blank line(s) are starting with `#` are ignored.
	* Standard glob patterns work, and will be applied recursively throughout the entire working tree.
	* You can start patterns with a forward slash (`/`) to avoid recursively
	* You can end patterns with a forward slash (`/`) to specify a directory
	* You can negate a pattern by starting it with an exclamation point (`!`).

* Glob patterns are simplified regular expressions that shells use (Refer [this](https://github.com/github/gitignore) for Github `.gitignore` file templates): 
	* An asterisk (`*`) matches zero or more characters
	* `[abc]` matches any character inside the brackets (in this case a, b, or c)
	* `?` - matches a single character
	* Brackets enclosing characters separated by a hyphen (`[0-9]`) matches any character between them (in this case 0 through 9).
	* You can use 2 * to match nested directories (`a/**/z` would match `a/z`, `a/b/z`, `a/b/c/z` and so on).

![Example Gitignore file](image-33.png)

* Its is also possible to have additional `.gitignore` files in subdirectories of tthe repository. The rules in these nested `.gitignore` files apply only to the files under the directory where they are located. 

## Viewing Your Staged and Unstaged Changes

* `git diff` - When you wnat to know exactly what you changed, not just which files were changed. It shows you the exact lines added and removed - the patch.

* To see what you've changed but not yet staged, type `git diff` with no argument. The command compares what is in your working directory with what is in your staging area. The result tell you the chagnes you've made that you haven't yet staged.
```
$ git diff
```

![git diff](image-34.png)

* If you want to see what you've staged that will go into your next commit use the command `git diff --staged`. This command compares your staged changed to your last commit.
```
$ git diff --staged
```

![git diff --staged](image-35.png)

* `git diff` shows you only the changes that are still unstaged, not the changed made since your last commit. If you've staged all of your changes, it will give no output.

## Committing Your Changes

* Anything that is unstaged (any files you have created or modified that you haven't run `git add` on since you edited them) won't go into this commit. They will stay as modified files on your disk.

* Type `git commit` would open an editor of choice and will show a default template of a commit message containing the output of `git status`, which is commented out and will be ignored (using #). You can add your message after this, and keep this intact so that it will help you remember what you're committing. You can put `-v` option to the `git commit` command for add the diff of your change in the editor.

![git commit](image-36.png)

* You can use `-m` flag with `commit` command so that you can type your commit message inline
```
$ git commit -m "Story 182: Fix benchmarks for speed
```

![git commit -m](image-37.png)

* You can see that the commit has given some output about itself - which branch you committed to (`master`), what SHA-1 checkshum the commit has, how many files were changed, and statistics about line added and in the commit.

## Skipping the Staging Area

* Adding a `-a` option to the `git commit` command makes Git automatically stage every file that is already tracked before doing the commit, letting you skip the `git add` part
  
## Removing Files

* To remove a file(s) from Git, you have to remove it from your tracked files and then commit. This can be done using `git rm` command, and also removed the file from your working directory so you don't see it as an untracked file the next time around. You can pass files, directories, and file-glob patterns.

* If you simply remove the file from your working directory, it shows up under the "Changes not staged for commit" (unstaged) area of `git status`.

![Image](image-11.png)

* But if you run `git rm` it stages the file's removal. The next time you commit, the file will be gone and no longer tracked. If you modifed the file or had already added it to the staging area, you much force the removal with the `-f` option. 

![Image](image-12.png)

* You may want to keep the file in your working tree but remove it from your staging area (not have Git track it anymore). This is useful if you forget to add something to your `.gitignore` file and accidentally staged it. To do this, use the `--cached` option:
```
$ git rm --cached README
```

## Moving Files

* `git mv` - If you want to rename a file in Git.
```
$ git mv file_from file_to
```

![git mv](image-38.png)

* Git recognises that a file has been rename. If you run `git status`, it will show you that a file has been renamed. This is equivalent to running something like:
```
$ mv file_from file_to
$ git rm file_from
$ git add file_to
```

# Viewing the Commit History

* `git log` - To view the several commits made. By default with no arguments it will list the commits made in that repository in reverse chronological order (most recent commits will show up first).
```
$ git log
```

* `-p` or `--patch` - Shows the difference (the `patch` output) introduced in each commit. It shows the same information but with a diff directly following each entry. This is very helpful for code review or to quickly browser what happened during a series of commits that a collaborator has added. 
```
$ git log -p
```

* `--stat` - See some abbreviated stats for each commit. It prints below each commit a list of of modified files, how many files were changed, and how many lines in those files are added and removed. It also puts a summary of the information at the end. 
```
$ git log --stat
```

* `--pretty` - Changes the log output to formats other than the default. 
	* The `oneline` value for this option prints prints each commit on a single line, which is useful if you're looking at a lot of commits
	```
	$ git log --pretty=oneline
	```

	* `short`, `full`, `fuller` - Show the output in less or more information
	* `format` - Allows you to specify your own log output format. This is especially useful when you're generating output for machine parsing - because you specify the format explicitly, you know if won't change with updates to Git

(Note - Author is the person who originally wrote the work, whereas the committer is the person who last applied the work. If you send in a patch to a project and one of the core members applies the patch, both of you get credit - you as the author, and the core member as the committer)

* `--graph` - It adds a ASCII graph showing your branches and merge history
```
$ git log --pretty=oneline --graph
```

## Limiting Log Output

* `-<n>` - if n is an integer, this option shows the last `n` commits

* `--since` and `--until` - Gets the list of commits made in the last two weeks
```
$ git log --since=2.weeks
```

* `--author` - Allows you to filter on a specific author

* `--grep` - Lets you search for keywords in the commit messages.

* `-S` - Takes a string and shows only those commits that changed the number of occurences of that strings. For instance, if you wanted to fined the last commit that added or removed a reference to a specific function, you could call:
```
$ git log -S function_name
```

* `--` - Filter as a path. If you specify a directory or file name, you can limit the log output to commits that introduces a change to those files. This is always the last option and is generally preceded by double dashes (--) to separate the paths from the options:
```
$ git log -- path/to/file
```

# Undoing Things

* If you make a commit too early and possibly forget to add some files, or you mess up your commit message, and you want to redo that commit, make the additional changes you forgot, stage them, and commit again using the `--amend` option - This command takes your staging area and uses it for the commit. If you've made no changes since your last commit (for example, you run the this command immediately after your previous commit), then your snapshot will look exactly the same and all you'll change is your commit message. 
```
$ git commit --amend
```

* It basically replaces the old commit with a new commit. It's as if the previous commit never happened, and it won't show up in your repository history.

* Only amend commits that are still local and have not been pushed somewhere. Amending previously pushed commits and force pushing the branch will cause problems for your collaborators.

## Unstaging a Staged File

* Let's say you've changed two files and want to commit them as two separate changes, but you accidentally type `git add *` and stage both of them. To unstage a file (file will become just a modified file) you can run the command :
_OLD METHOD_
```
$ git reset HEAD <filename>
```

_NEW METHOD_
```
$ git restore --staged <filename>
```

## Unmodifying a Modified File

* If you don't want to keep your changes to a file, run the following command ot unmodify it (the file will return back to what it looked like whne you last committed or initially cloned)
_OLD METHOD_
```
$ git checkout -- <filename>
```

_NEW METHOD_
```
$ git restore <filename>
```

# Working with Remotes

* Remote Repositories are versions of your project that are hosted on the Internet or network somewhere with read-only or read-write for you. 

## Showing your Remotes

* `git remote` - To see which remote servers you have configured.
* _NOTE_: `origin` - that is the default name Git gives to the server you cloned from.

* `-v` option - Shows you all the URLs that Git has stored for the shortname to be used when reading and writing to that remote, or all the URLs in case of multiple remotes
![git remote -v command](image-30.png)

## Adding Remote Repositories

* `git remote add <shortname> <url>` - To add a new remote Git repository as a shortname you can reference easily

![Git remote add url command](image-31.png)

## Fetching and Pulling from your Remotes

* `git fetch <remote>` - Get all the data that the remote project has but you don't have yet. After you do this, you should have references to all the branches from that remote. The output of this command will show where each branch of the remote is accessible locally. You can merge it into one of your branches, or you can check out a local branch at that point if you want to inspect it.

![git fetch command](image-32.png)

* If you clone a repository, the command automatically adds that remote repository under the name 'origin'. So `git fetch origin` fetches any new work that has been pushed to that server since you cloned (or last fetched from) it. It only downloads the data to your local repository - it doesn't automatically merge it with any of the work or modify what you're currently working on. You have to merge it manually into your work when you are ready.

* If your current branch is set up to track a remote branch, you can use `git pull` command to automatically fetch and then merge that remote branch into your current branch. By default, the `git clone` comand automatically sets up your local `master` branch to trach the remote `master` branch on the server you cloned from (or whatever the default branch is called there). Running `git pull` generally fetches data from the server you originally cloned from and automatically tries to merge it into the code you're currently working on.

## Pushing to your Remotes

* `git push <remote> <branch>` - When you have your project that you want to share, you have to push it upstream. 
```
$ git push origin master
```

* This command only works if you have cloned from a server to which you have write access and if nobody has pushed in the meanwhile. If you and someone else clone at the same time and they push upstream and then you push upstream, your push will be rejected. You will have to fetch their work first and incorporate it in yours before you will be allowed to push. 

## Inspecting a Remote

* `git remote show <remote>` - Show information about a particular remote. It lists the URL for the remote repository as well as the tracking branch information. If you are on the master branch and you run `git pull`, it will automatically merge the remote's `master` branch into the local one after it has been fetched. It also lists all the remote references it has pulled down. It also shows which branch is automatically pushed to when you run `git push` while on certain branches. It also show you which remotes branches on the server you don't have yet, which remote branch you have that have been removed from the srever, and multiple local branches that are able to merge automatically with their remote-tracking branch when you run `git pull`.

![Git Remote Show Image](image-13.png)

##  Renaming and Removing Remotes

* `git remote rename <original_name> <new_name>` - Rename a remote's shortname from original_name to new_name.

* `git remote rm <shortname>` - Remove a remote shortname 

# GIT BRANCHING

# Branches in a Nutshell

* Branching means you diverge from the main line of development and continue to work without messing with that main line. 

* When you make a commit, Git stores a commit object that contains a pointer to the snapshot of the content you staged. This object also contains the author's name and email address, the message that you typed, and pointers to the commit or commits that directly came before this commit (parent). 
	* Let's say you have a directory containing three files, and you stage them all and commit. Stagin the file computes a checksum for each one (the SHA-1 hash), stores the version of each file in the Git repository (blobs), and adds that checksum to the staging area.
	* When you create the commmit by running `git commit`, Git checksums each subdirectory (in this case, the root project directory), and stores them as a tree object in the Git repository. Git then creates a commit object that has the metadata and a pointer to the root project tree so it can re-create that snapshot when needed. The Git repository now has 5 objects: three blobs (each representing the contents of one of the three files), one tree that lists the contents of the directory and specifies which file names are stored as which blobs, and one commit with the pointer to that root tree and all the commit metadata 
	
	* If you make some changes and commit again, the next commit stores a pointer to the commit that came immediately before it.

	* A branch in Git is simply a movable pointer to one of these commits. The default branch name in Git is `master`. As you start making commits, you are given a `master` branch that points to the last commit you made. Every time you commit, the `master` branch pointer moves forward automatically

# Creating a New Branch

* When you create a new branch, you are creating a new pointer for you to move around. You can do this using the command `git branch <branchname>`.  This creates a new pointer to the same commit you're currently on. 

* How does Git know which branch you're currently on? It keeps a special pointer called `HEAD`. In Git, this is a pointer to the local branch you're currently on. You can see this by running:

```
$ git log --oneline --decorate
```

# Switching Branches

* To switch to an existing branch run the `git checkout <branchname>` command. This moves `HEAD` to point to the new branch.

* If you make further commits, the new branch will move forward, but your master branch still points to the commit you were on when you ran `git checkout` to switch branches. 

* If you switch back to `master` branch, the HEAD pointer moves back to point to the `master` branch and it reverst the files in your working directory back to the snapshot that `master` points to. It essentially just rewinds the work you've done in your testing branch so you can go in a different direction. 

* When you create and switch to a branch, did some work on it, and then switched back to the main branch and did some other work. Both of these changes are isolated in separate branches: you can switch back and forth between the branches and merge them together when you're ready.

* Run `git log --oneline --decorate --graph --all` to print out the history of your commits, showing where your branch pointers are and how your history has diverged. 

* You can use `git switch` instead of `git checkout`

# Basic Branching and Merging

* Let's say you are working on your project and have a couple of commits already on the `master` branch. You've decided to work on issue#53. So you create a new branch and switch to it.

```
$ git checkout -b iss53
```

* You work on your website and make some commits. Doing so moves the `iss53` branch forward, because you have it checked out (that is, your HEAD is pointing to it).

* Now you get a call that there is an issue with the website, and you need to fix it immediately. So you have to switch to the `master` branch (Before doing that if your working directory or staging area has uncommited changes that conflict with the branch you're checking out, Git won't let you switch branches. It's best to have a clean working state when you switch branches. So first commit all the changes made in the new branch and then checkout to `master`).

* When you switch branches, Git resets your working directory to look like it did the last time you committed on that branch. Now you create a `hotfix` branch on which to work until its completed. You can run your tests, make sure the hotfix is what you want, and finally merge the `hotfix` branch back into your `master` branch to deploy to production with `git merge` command

```
$ git checkout master
$ git merge hotfix
```

* Because the hotifx you merged in was directly ahead of the commit you are on, Git simply moves the pointer forward. When you merge one commit with a commit that can be reached by following the first commit's history, Git simplifies things by moving the pointer forward because there is no divergent work to merge together - this is called a "fast-forward".

* After the fix is deployed, you are ready to switch back to the work you were doing before you were interrupted. But first you'll delete the `hotfix` branch as you no longer need it - the `master` branch points at the same place. Run the command

```
$ git branch -d hotfix
```

* Now you can swtich back to your work-in-progress branch on issue#53 and continue working on it. Its worth noting here that the work you did in your `hotfix` branch is not contained in the files in your `iss53` branch. If you need to pull it in, you can merge your `master` branch into your `iss53` branch by running `git merge master` or you can wait to integrate those changes until you decide to pull the `iss53` branch back into `master` later.

# Basic Merging

* Suppose you've decide that your issue#53 work is completed and readly to be merged into your `master` branch. In order to do that, you'll merge your `iss53` branch into `master` using `git merge` command

```
$ git checkout master
$ git merge iss53
```

* Since your development history has merged from some older point, and the commit on the branch you are on isn't a direct ancestor of the branch you're merging in, Git has to do some work. Git creates a new snapshot that results from the three-way merge (two snapshots pointed to by the branch tips, and the common ancestor of the two) and automatically creates a new commit that points to it. This is referred to as a merge commit, and is special in that is has more than one parent. 

* Now that your work is merged in, you have no further need for the `iss53` branch. You can close the issue and delete the branch

# Basic Merge Conflicts

* If you have changed the same part of the same file differently in the two branches you're merging (or fix for issue#53 modifies the same part of a file as `hotfix` branch), Git won't be able to merge them cleanly. Git pauses the process while you resolve the conflict. 

* If you want to see which files are unmerged at any point after a merge conflict, you can run `git status`. Anything that has merge conflicts and hasn't been resolved is listed as unmerged. Git adds standard conflic-reseolution markes to the files that have conflicts, so you can open them manually and resolve those conflicts. 

* For example, the version in `HEAD` (your `master` branch because that was what you had checkout out when you ran your merge command) is the top part of the block (everything above the `=======`), while the version in your `iss53` branch looks like everything in the bottom part. You have to replace everything within the lines containing `<<<<<` and `>>>>>>>` (including them) in each of the conflicting files. After that you run `git add` on each file to mark it as resolved. You can run `git status` again to verify that all conflicts have been resolved.

* After staging your merge conflict resolutions, you can type `git commit` to finalize the merge commit.

# Branch Management

* `git branch` - Get a list of your current branches

`*` character prefixes the branch that you are currently checked out to (that is, the branch the `HEAD` points to). This means if you commit at this point, that branche will be moved forward with you new work. 

* `git branch -v` - See the last commit on each branch

* `--merged` and `--no-merged` - Filter the list of branches to the ones which have or have not been merged yet into the branch that you are currently on. If you run `git branch --merged`, the list of branches without `*` prefix are generally fine to delete because you have already incorporated their work into another branch, so you are not going to lose anything.

* Trying to delete a branch in the list provided by `git branch --no-merged` will fail and will have to be forced because you will lose the work done on this branch. 

## Changing a Branch Name

* To rename a branch locally run `git branch --move` command

```
$ git branch --move bad-branch-name corrected-branch-name
```

* To see the corrected branch on the remote, push it:

```
$ git push --set-upstream origin corrected-branch-name
```

* The branch with the bad name is also present if you run `git branch --all`, but you can delete it

```
$ git push origin --delete bad-branch-name
```

## Changing the master branch name

* To rename your local `master` branch into `main`, run

```
$ git branch --move master main
```

* To let others see the new `main` branch, you need to push it to the remote. 

``` 
$ git push --set-upstream origin main
```

* After you make certain that the `main` branch performs just as the `master` branch, you can delete the `master` branch

```
$ git push origin --delete master
```

# Branching Workflows

## Branching Workflows

### Long Running Branches

* Many Git developers have a workflow that embraces this approach, such as having only code that is entirely stable in their `master` branch - code that has been or will be released. They have another parallel branch named `develop` that they work from or use to test stability, and whenever ite gets to a stable state, it can be merged into `master`. Its used to pull in topic branches when they are ready, to make sure they pass all the tests and don't introduce bugs. Some larger projects also have a `proposed` branch that has integrated branches that may not be ready to go into the `next` or `master` branch. The idea is that your branches are at various levels of stability; when they reach a more stable level, they're merged into the branch above them. 

![A "Silo" view of progressive-stability branching](image.png)

### Topic Branches

* A topic branch is a short-lived branch that you create and use for a single particular feature or related work. For eg. the `iss53` and `hotfix` branches. You did few commits on them and directly after merging them into your main branch. This technique allows you to context-switch quickly and completely.

# 3.5 - Remote Branches{#Chapter3.5}

* Remote references are references (pointers) in your remote repositoreis, including branches, tags and so on. You can get a full list of remote references explicitly with `git ls-remote <remote>` or `git remote show <remote>` for remote branches as well as more information. 

* Remote tracking branches are references to the state of remote branches. They are local references that you can't move. Git moves them for you whenver you do any network communication, to make sure they accurately represent the state of the remote repository. Think of them as bookmarks, to remind you where the branches in your remote repositories were the last time you connected to them.

* Remote-tracking branch names take the form `<remote>/<branch>`. If you wanted to see what the `master` branch on your `origin` remote looked like as of the last time you communicated with it, you would check the `origin/master` branch. 

* Let's say you have a Git server on your network at `git.ourcompany.com`. If you clone from this Git's `clone` command automatically names it `origin` for you, pulls down all its data, creates a pointer to where its `master` branch is, and names it `origin/master` locally. Git also gives you your own local `master` branch starting at the same place as origin's `master` branch, so you have something to work from.

![Server and Local Repositories after cloning](image-1.png)

* If you do some work on your local `master` branch, and in the meantime, someone else pushed to `git.ourcompany.com` and updates its `master` branch, then your histories move forward differently. Also, as long as you stay out of contact with your `origin` server, your `origin/master` pointer moves nowhere.

![Local and Remote work can diverge](image-2.png)

* To sync your work with a given remote, you run a `get fetch <remote>` command (in our case, `git fetch origin`). This command looks up which server `origin` is (in this case, its `git.ourcompany.com`), fetches any data from it that you don't have yet, and updates your local database, moving your `origin/master` pointer to its new, more up-to-date position.

![`git fetch` updates your remote-tracking branches](image-3.png)

* Let's assume you have another internal Git server that is used only for development by one of your sprint teams. This server is at `git.team1.ourcompany.com`. You can add it as a new remote reference to the project you are currently working on by running the `git remote add` command. Name this remote `teamone` which will be the shortname for that whole URL.

* Now you can run `git fetch teamone` to fetch everything the remote `teamone` server has that you don't have yet. because that server has a subset of the data you `origin` server has right now, Git fetches no data but sets a remote-tracking branch called `teamone/master` to point to the commit that `teamone` has as its `master` branch. 

![Remote tracking branch for teamone/master](image-4.png)

## Pushing

* When you wwant to share a branch with the world, you need to push it to a remote to which you have write acess. Your local branches aren't automatically synced to the remote you write to - you have to explicitly push the branches you want to share. That way, you can use private branches for work you don't want to share, and push up only the topic branches you want to collaborate on.

* If you have a barnch named `serverfix` that you want to work on with others, you can push it up the same way you pushed the first branch. Run `git push <remote> <branch>`. So in our case

```
$ git push origin serverfix
```

* If you want to push a local branch into a remote branch that is named differently, you can run instead `git push origin serverfix:awesomebranch` to push your local `serverfix` branch to the `awesomebranch` branch on the remote project.

* When you do a fetch that brings down new remote-tracking branches, you don't automatically have local, editable copies of them. You don't have a new `serverfix` branch - you only have an `origin/serverfix` pointer that you can't modify. To merge this work into your current working branch, you can run `git merge origin/serverfix`. If you want your own `serverfix` branch that you can work on, you can base it off your remote-tracking branch:

```
$ git checkout -b serverfix origin/serverfix
```

* This gives you a local branch that you can work on that starts where `origin/serverfix` is

## Tracking Branches

* Checking out a local branch from a remote tracking branch automatically creates what is called a "tracking branch" (and the branch it tracks is called an "upstream branch"). Tracking branches are local branches that have a direct relationship to a remote branch. If you are on a tracking branch and type `git pull`, Git automatically knows which server to fetch from and which branch to merge in.









# GIT TOOLS

# Revision Selection

* Git allows you to refer to a single commit, set of commits, or range of commits in a number of ways.

## Single Revisions

* Refer to any single commit by its full 40-character SHA-1 hash

## Short SHA-1

* First few characters of the SHA-1 hash is enough to refer to a commit, as long as that partial hash is at least 4 characters long and unambiguous (no other object in the object database can have a has that begins with the same prefix).

* Generally 8 to 10 characters are more than enough to be unique within a project. 

* If you want to inspect which commits have a hash value that begins with `1c002dd`
```
$ git show 1c002dd
```

* If you pass `--abbrev-commit` to the `git log` command, the output will use shorter values but keep them unique; it defaults to using seven characters but makes them longer if necessary to keep the SHA-1 unambiguous
```
$ git log --abbrev-commit --pretty=oneline
```

## Branch References

* To refer to a particular commit that is at the tip of a branch, we can simply use the branch name in any Git command that expects a reference to a commit. For eg, if you want to examine the last commit object on a branch, assuming `topic1` branch ponts to commit `ca82a6d`, the following commands are equivalent:
```
$ git show ca82a6d
$ git show topic1
```

## RefLog Shortnames

* Git keeps a "reflog" - a log of where your HEAD and branch references have been for the last few months. You can see your reflog by using `git reflog`
```
$ git reflog
```

* If you want to see the fifth prior value of the HEAD of your repository, you can use the `@{5}` reference
```
$ git show HEAD@{5}
```

# Stashing and Cleaning

* When you have been working on part of your project, things are in a messy state and you want to switch branches for a bit of work on something else. But you don't want to do a commit of half-dome work just so you can get back to this point later. `git stash` comes to the rescue.

* Stashing takes the dirty state of your working directory - that is, your modified tracked files and staged changes - and saves it on a stack of unfinished changes that you can reapply at any time (even on a different branch).

## Stashing your work

* If you work on a project and start working on a couple of files, and possibly stage one of the changes. Now when you want to switch branches, you don't want to commit what you've been working on yet, so you'll stash the changes. To push a new stash onto your stack, run `git stash push`
```
$ git stash push
```

* It will make your working directory clean. Run `git status` to verify that.

* To see which stashes you have stored, you can use `git stash list`
```
$ git stash list
```

* You can reapply the stash using the command `git stash apply`. If you want to apply one of the older stashed, you can specify it by naming it (if you don't specify a stash, Git assumes the most recent stash and tries to apply it):
```
$ git stash apply stash@{2}
```

* Having a clean working directory and applying it on the same branch aren't necessary to successfully apply a stash. You can save a stash on one branch, switch to another branch later, and try to reapply the changes. You can also have modified and committed files in your working directory when you apply a stash - Git gives you merge conflicts if anything no longer applies cleanly. The changes to your files will be reapplied, but the file you staged before wasn't restaged. To do that, you must run the `git stash apply` command with a `--index` option to tell the command to try to reappply the staged changes. 
```
$ git stash apply --index
```

* The `apply` option only tries to apply the stashed work - you continue to have it on your stack. To remove it, you can run `git stash drop` with the name of the stash to remove
```
$ git stash drop stash@{3}
```

* You can also run `git stash pop` to apply the stash and then immediately drop it from your stack.

## Creative Stashing

* By default `git stash` will stash only modified an staged tracked files. If you specify `--include-untracked`, Git will include untracked files in the stash being created. However, including untracked files in the stash will not include explicitly ignored files, to additionally include ignored files use `--all`.
```
$ git stash --included-untracked --all
```

* If you specify the `--patch` flag, Git will not stash everything that is modified but will instead prompt you interactively which of the changes you would like to stash and which you would like to keep in your working directory
```
$ git stash --patch
```

## Creating a Branch from a Stash

* If you stash some work, leave it there for a while, and continue on the branch from which you stashed the work, you may have a problem reapplying the work. If the apply tries to modify a file that you've since modified, you'll get a merge conflict and will have to try to resolve it. If you want an easier way to test the stashed changes again, you can run `git stash branch <new branchname>` which creates a new branch for you with your selected branch name, checks out the commit you were on when you stashed your work, reapplies your work there, and then drops the stash if it applies successfully
```
$ git stash branch testchanges
```

# Advanced Merging

## Merge Conflicts

* Try to make sure your working directory is clean before doing a merge that may have conflicts. If you have work in progress, either commit it to a temporary branch or stash it. This makes it so that you can undo anything you try here. If you have unsaved changes in your working directory when you try a merge, some of these tips may help you preserver that work.

* Example - Let's say we have a simple Ruby file that prints "Hello World".

	![Ruby File](image-14.png)

	* We create a new branch named `whitespace` and proceed to change all the Unix line endings to DOS line endings, essentially changing every line of the file, but just the whitespace. Then we change the "hello world" to "hello mundo".

	![Changes to Ruby File](image-15.png)

	* Now we switch back to our `master` branch and add some documentation for the function.

	![Changes to Ruby File in different branch](image-16.png)

	* Now when we try to more in our `whitespace` branch, we will get conflicts because of the whitespace changes.

	![Merge Conflict](image-17.png)

## Aborting a Merge

* `git merge --abort` - Back out of the merge if conflicts were unexpected. It tries to revert back to your state before you ran the merge, unless you had unstashed, uncommitted changes in your working directory when you ran it.
```
$ git merge --abort
```

* If you want to start over, you can also run `git reset --hard HEAD` and your repository will be back to the last committed state. Any uncommitted work will be lost

## Ignoring Whitespaces

* `-Xignore-all-space` - Ignore whitespaces completely when comparing lines
* `-Xignore-space-change` - Treats sequences of one or more whitespace characters as equivalent
```
$ git merge -Xignore-space-change whitepsace
```

* This is useful in situations when someone reformats everythings from spaces to tabs or vice-versa

## Manual File Re-Merging


## Undoing Merges

* Lets say you are working on a topic branch, and accidentally merged it into `master`, and now your commit history looks like this:

![Accidental merge commit](image-18.png)

### Fix the references

* If the unwanted merge commit only exists on your local repository, the easiest and best solution is to move the branches so that they point where you want them to. If you follow `git merge` with `git reset --hard HEAD~`, thsi will reset the branch pointers so they look like this:

![History after `git reset --hard HEAD~`](image-19.png)

* The `reset --hard` goes through three steps:
	* Move the branch HEAD points to. 
	* Make the index look like HEAD
	* Make the working directory look like the index

* This can be problematic with a shared repository. If other people have the commits you are rewriting, you should probably avoid `reset`. This approach won't work if any other commits have been created since the merge, moving the refs would effectively lose those changes.

### Reverse the commit

* Git gives you the option of making a new commit which undoes all the changes from an existing one. 
```
$ git revert -m 1 HEAD
```

* The `-m 1` flag indicates which parent in the "mainline" and should be kept. When you invoke a merge into `HEAD`, the new commit has two parents, the first one is `HEAD` and the second is the top of the branch being merged in. If we want to undo all the changes introduced by merging in parent #2, while keeping all the content from parent #1. The history with the rever commit looks like this:

![History after `git rever -m 1`](image-20.png)

* The new commit `^M` has exactly the same contents as C6, so starting from here its as if the merge never happened, except that the now un-merged  commits are still in `HEAD`'s history, so Git things that the branch `topic` is already merged. If you add work to `topic` and merge again, Git will only bring in the changes since the reverted merge.

![History with bad merge](image-21.png)

* The best way around this is to un-revert the original merge, since now you want to brign in the changes that were reverted out, then create a new merge commit.
```
$ git rever ^M
$ git merge topic
```

![History after re-merging a reverted merge](image-22.png)

* M and ^M cancel out, and ^^M effectively merged in the changes from C3 and C4, and C8 merges in the changes from C7, so now `topic` is fully merged.

## Other Types of Merges

### Ours or Theirs Preference

* By default, when Git sees a conflict between two branches being merged, it will add merge conflict markers into your code and mark the file as conflictedd and let your resolve it. If you would prefer for Git to simply choose a specific side and ignore the other side instead of letting you manually resolved the conflict, you can pass the `merge` command either a `-Xours` or `-Xtheirs`. If Git sees this, it will not add conflict markers. Anny differences that are mergable, it will merge. Any differences that conflict, it will simply choose the side you specify the while, including binary files.
```
$ git merge -Xours mundo
```

* Instead of getting conflict markers in the file with "hello mundo" on one side and "hola world" on the other, it will simply pick "hola world". All the other non-conflicting changes on that branch are merged successfully in.

* If you dont want to have Git even try to merge changes from the other side in, use the "ours" merge strategy. This will basically do a fake merge. It will record a new merge commit with both branches as parents, but it will not even look at that branch you are merging in. It will simply record as the result of the merge the exact code in your current branch. This can be verified by seeing that there is no difference between the branch we were on and the result of the merge. 
```
$ git merge -s ours mundo
$ git diff HEAD HEAD~
```
