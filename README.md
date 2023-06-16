# **TABLE OF CONTENTS**

#

# 2.2 - Recording Changes to the Repository

* _NOTE: Having a `checkout` on your local machine -> working copy of all files in a Git repository_
* Typically, you'll want to start making changes and committing snapshots of those changes into your repository each time the project reaches a state you want to record.
* Each file in your working directory can be in one of two states:
	* **Tracked** - Files that were in the last snapshot, as well as any newly staged files; they can be unmodified, modified, or staged. Trackes files are files that Git knows about.
	* **Untracked** - Everything other than tracked files - Any files in your working directory that were not in your last snapshot and not in your staging area. Git won't start including it in your commit snapshots until you explicitly tell it to do so.
* As you edit files Git seems them as modified, because you've changed them since your last commit. As you work, you selectively stages these  modified files and then commit all those staged changes, and the cycle repeats.

![image](https://github.com/rsing148/CommunityClassroom-Git/assets/127006601/41a407bb-45fb-4bc2-b53e-d2bde7f91114)

## Checking the Status of your Files

`git status` - To get which files are in which state. If you run this command directly after a clone, you should see a clean working directory - none of your tracked files are modified. The command tells you which branch you are on, and informs you that it has not diverd from the same branch on the server. 

```
$ git status
```

After you add a file to your project, a simple `README` file. If this file didn't exist before and you run `git status`, you see your untracked file under the "Untracked Files" heading in your status output. 

## Tracking New Files

`git add` - In order to begin tracking a new file. It takes a path or a directory; if its a directory, the command adds all the files in that directory recursively. IT means "add precisely this content to the next commit". For instance, to begin tracking the `README` file run

```
$ git add README
```

If you run status command again, you can see that your README file is now tracked and staged to be committed. You can tell it's staged because it appears under "Changes to be committed" heading. If you commit at this point, the version of the file at tht time you ran `git add` is what will be in the subsequent snapshot. 

## Staging Modified Files

Lets change a file that was already tracked. If you change a previously tracked file called CONTRIBUTING.md and then run your git status command again, you will see the file appears unders a section "Changes not staged for commit" - which means that a file that is tracked has been modified in the working directory but not yet staged. To stage it, you run the `git add` command.  After staging the file if we make another change to the same file, it will list as both staged and unstanged. It turns out that Git stages a file exactly as it wis when you run the `git add` command. If you commit now, the version of `CONTRIBUTING.md` as it was when you last ran the `git add` command is how it will go into the commit, not the version of the file as it looks in your working directory when you run `git commit`. If you modify a file after you run `git add`, you have to run `git add` again to stage the latest version fo the file.

## Short Status

* `git status -s` command shows changes in more compact way. The output has 2 columns - The left-hand column indicates the status of the staging area and the right-hand column indicates the status of the working tree.

* Symbols denote:
	* `??` - Untracked files
	* `A` - files that have been added to staging area
 	* `M` - Modifid files
    * `MM` - Files that have been staged and unstaged changes

## Ignoring File

* For files which you don't want Git to automatically add or even show as being untracked (such as log files or files produced by the build system - You can create afile listing patterns to match them names `.gitignore`.

* Rules for the patterns you can put in the `.gitignore` file are :

	* Blank line(s) are starting with `#` are ignored.
	* Standard glob patterns work, and will be applied recursively throughout the entire working tree.
	* You can start patterns with a forward slash (`/`) to avoid recursively
	* You can end patterns with a forward slash (`/`) to specify a directory
	* You can negate a pattern by starting it with an exclamation point (`!`).

* Glob patterns are simplified regular expressions that shells use. 

	* An asterisk (`*`) matches zero or more characters
	* `[abc]` matches any character inside the brackets (in this case a, b, or c)
	* `?` - matches a single character
	* Brackets enclosing characters separated by a hyphen (`[0-9]`) matches any character between them (in this case 0 through 9).
	* You can use 2 * to match nested directories (`a/**/z` would match `a/z`, `a/b/z`, `a/b/c/z` and so on).

	Its is also possible to have additional `.gitignore` files in subdirectories of tthe repository. The rules in these nested `.gitignore` files apply only to the files under the directory where they are located. 

## Viewing Your Staged and Unstaged Changes

* When you wnat to know exactly what you changed, not just which files were changed - you can use the `git diff` command. 

* It is used to get information about:

	* What have you changed but not yet staged?
	* What have you staged that you are about to commit

* `git status` answers those questions very generally by listing the file names, `git diff` shows you the exact lines added and removed - the patch.

* To see what you've changed but not yet staged, type `git diff` with no argument

```
$ git diff
```

The command compares what is in your working directory with what is in your staging area. The result tell you the chagnes you've made that you haven't yet staged.

* If you want to see what you've staged that will go into your next commit use the command `git diff --staged`. This command compares your staged changed to your last commit.

```
$ git diff staged
```

* `git diff` shows you only the changes that are still unstaged, not the changed made since your last commit. If you've staged all of your changes, it will give no output.

* You can also use `git diff` to see the changes in the file that are staged and the changes that are unstaged. 

* You can run `git difftool` instead of `git diff` to view any diffs in software like vimdiff.

## Committing Your Changes

* Anything that is unstaged (any files you have created or modified that you haven't run `git add` on since you edited them) won't go into this commit. They will stay as modified files on your disk.

* Type `git commit` would open an editor of choice and will show a default template of a commit message, which is commented out and will be ignored (using #). You can add your message after this, and keep this intact so that it will help you remember what you're committing. You can put `-v` option to the `git commit` command for add the diff of your change in the editor.
* You can use `-m` flag with `commit` command so that you can type your commit message inline with `commit` command.

```
$ git commit -m "Story 182: Fix benchmarks for speed
```

* Everytime you perform a commit, you are recording a snapshot of your project 

## Skipping the Staging Area

* Adding a `-a` option to the `git commit` command makes Git automatically stage every file that is already tracked before doing the commit, letting you skip the `git add` part
  
## Removing Files

* To remove a file from Git, you have to remove it from your tracked files and then commit. This can be done using `git rm` command, and also removed the file from your working directory so you don't see it as an untracked file the next time around. 

* If you simply remove the file from your working directory, it shows up under the "Changes not staged for commit" (unstaged) area of `git status`.

* But if you run `git rm` it stages the file's removal. The next time you commit, the file will be gone and no longer tracked. If you modifed the file or had already added it to the staging area, you much force the removal with the `-f` option. 

* You may want to keep the file on your hard drive but not have Git track it anymore. This is useful if you forget to add something to your `.gitignore` file and accidentally staged it. To do this, use the `--cached` option:

```
$ git rm --cached README
```

* You can pass files, directories, and file-glob patterns to the `git rm` command. 

```
$ git rm log/\*.log
```

This command removes all files that the `.log` extension in the `log/` directory. (Backslash (`\`) in front of the `*`).

## Moving Files

* If you want to rename a file in Git you can run 

```
$ git mv file_from file_to
```

This is equivalent to running something like:

```
$ mv file_from file_to
$ git rm file_from
$ git add file_to
```

# 2.3 - Viewing the Commit History

* `git log` - To view the several commits made. By default with no arguments it will list the commits made in that repository in reverse chronological order (most recent commits will show up first) along with the SHA-1 checksum, the author's name and email, the date written and the commit message

```
$ git log
```

* `-p` or `--patch` - Shows the difference (the **patch** output) introduced in each commit. It shows the same information but with a diff directly following each entry. This is very helpful for code review or to quickly browser what happened during a series of commits that a collaborator has added. 

`-2` - Limits the number of log entries displayed to 2

```
$ git log -p -2
```

* `--stat` - See some abbreviated stats for each commit. It prints below each commit a list of of modified files, how many files were changed, and how many lines in those files are added and removed. It also puts a summary of the information at the end. 

```
$ git log --stat
```

* `--pretty` - Changes the log output to formats other than the default. 

	* The `oneline` value for this option prints prints each commit on a single line, which is useful if you're looking at a lot of commits
	* `short`, `full`, `fuller` - Show the output in less or more information
	* `format` - Allows you to specify your own log output format. This is especially useful when you're generating output for machine parsing - because you specify the format explicitly, you know if won't change with updates to Git:

```
$ git log --pretty=oneline
```

```
$ git log --pretty=format:"%h - %an, %ar : %s"
```

(Note - Author is the person who originally wrote the work, whereas the committer is the person who last applied the work. If you send in a patch to a project and one of the core members applies the patch, both of you get credit - you as the author, and the core member as the committer)

* `--graph` - It adds a ASCII graph showing your branches and merge history

```
$ git log --pretty=format:"%h %s" --graph
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

# 2.4 Undoing Things

* If you make a commit too early and possibly forget to add some files, or you mess up your commit message, and you want to redo that commit, make the additional changes you forgot, stage them, and commit again using the `--amend` option - This command takes your staging area and uses it for the commit. If you've made no changes since your last commit (for example, you run the this command immediately after your previous commit), then your snapshot will look exactly the same and all you'll change is your commit message. 

```
$ git commit --amend
```

Only amend commits that are still local and have not been pushed somewhere. Amending previously pushed commits and force pushing the branch will cause problems for your collaborators.

## Unstaging a Staged File

* Let's say you've changed two files and want to commit them as two separate changes, but you accidentally type `git add *` and stage both of them. To unstage a file (file will become just a modified file) you can run the command:

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
$ git restored --staged <filename>
```

# 2.5 - Working with Remotes

* Remote Repositories are versions of your project that are hosted on the Internet or network somewhere with read-only or read-write for you. 

## Showing your Remotes

* `git remote` - To see which remote servers you have configured. It lists the shortnames of each remote handle you've specified. If you've cloned your repository, you should at least see `origin` - that is the default name Git gives to the server you cloned from.

* `-v` option - Shows you all the URLs that Git has stored for the shortname to be used when reading and writing to that remote, or all the URLs in case of multiple remotes

```
$ git remote -v
```

## Adding Remote Repositories

* `git remote add <shortname> <url>` - To add a new remote Git repository as a shortname you can reference easily

## Fetching and Pulling from your Remotes

* `git fetch <remote>` - Get all the data that the remote project has but you don't have yet. After you do this, you should have references to all the branches from that remote. 

* If you clone a repository, the command automatically adds that remote repository under the name 'origin'. So `git fetch origin` fetches any new work that has been pushed to that server since you cloned (or last fetched from) it. It only downloads the data to your local repository - it doesn't automatically merge it with any of the work or modify what you're currently working on.

* If your current branch is set up to track a remote branch, you can use `git pull` command to automatically fetch and then merge that remote branch into your current branch. By default, the `git clone` comand automatically sets up your local `master` branch to trach the remote `master` branch on the server you cloned from. Running `git pull` generally fetches data from the server you originally cloned from and automatically tries to merge it into the code you're currently working on.

## Pushing to your Remotes

* When you have your project that you want to share, you have to push it upstream. The command is `git push <remote> <branch>`

* This command only works if you have cloned from a server to which you have write access and if nobody has pushed in the meanwhile. If you and someone else clone at the same time and they push upstream and then you push upstream, your push will be rejected. You will have to fetch their work first and incorporate it in yours before you will be allowed to push. 

## Inspecting a Remote

* `git remote show <remote>` - Show information about a particular remote. It lists the URL for the remote repository as well as the tracking branch information. If you are on the master branch and you run `git pull`, it will automatically merge the remote's `master` branch into the local one after it has been fetched. It also lists all the remote references it has pulled down. It also shows which branch is automatically pushed to when you run `git push` while on certain branches. 

# Renaming and Removing Remotes

* `git remote rename <original_name> <new_name>` - Rename a remote's shortname from original_name to new_name.

* `git remote rm <shortname>` - Remove a remote shortname 





# CHAPTER 3 - GIT BRANCHING

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

