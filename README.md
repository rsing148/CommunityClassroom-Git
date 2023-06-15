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
* Rules for the patterns you can pit

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

* 










# 2.3 - Viewing the Commit History
`git log` - To view the several commits made. By default with no arguments it will list the commits made in that repository in reverse chronological order (most recent commits will show up first) along with the SHA-1 checksum, the author's name and email, the date written and the commit message

```
$ git log
```

`-p` or `--patch` - Shows the difference (the **patch** output) introduced in each commit. It shows the same information but with a diff directly following each entry. This is very helpful for code review or to quickly browser what happened during a series of commits that a collaborator has added. 

`-2` - Limits the number of log entries displayed to 2

```
$ git log -p -2
```

`--stat` - See some abbreviated stats for each commit. It prints below each commit a list of of modified files, how many files were changed, and how many lines in those files are added and removed. It also puts a summary of the information at the end. 

```
$ git log --stat
```

`--pretty` - Changes the log output to formats other than the default. 
* The `oneline` value for this option prints prints each commit on a single line, which is useful if you're looking at a lot of commits
* `short`, `full`, `fuller` - Show the output in less or more information

```
$ git log --oneline
```

* `format` - Allows you to specify your own log output format. This is especially useful when you're generating output for machine parsing - because you specify the format explicitly, you know if won't change with updates to Git:

```
$ git log --pretty=format:"%h - %an, %ar : %s"
```

(Note - Author is the person who originally wrote the work, whereas the committer is the person who last applied the work. If you send in a patch to a project and one of the core members applies the patch, both of you get credit - you as the author, and the core member as the committer)

`--graph` - It adds a ASCII graph showing your branches and merge history

```
$ git log --pretty=format:"%h %s" --graph
```

## Limiting Log Output

`--since` and `--until` - Gets the list of commits made in the last two weeks

```
$ git log --since=2.weeks
```

`--author` - Allows you to filter on a specific author

`--grep` - Lets you search for keywords in the commit messages.

`-S` - Takes a string and shows only those commits that changed the number of occurences of that strings. For instance, if you wanted to fined the last commit that added or removed a reference to a specific function, you could call:

```
$ git log -S function_name
```

`--` - Filter as a path. If you specify a directory or file name, you can limit the log output to commits that introduces a change to those files. This is always the last option and is generally preceded by double dashes (--) to separate the paths from the options:

```
$ git log -- path/to/file
```




# Working with Remotes

1. Remote Repositories are versions of your project that are hosted on the Internet or network somewhere with read-only or read-write for you. 
2. Collaborating with others involve managing these remote repositories and pushing and pulling data to and from then when you need to share work.

**SHOWING YOUR REMOTES**
<code>git remote</code> - To see which remote servers you have configured. It returns the list of shortnames of each remote handle you have specified.
(If you have cloned your repository, you should at least see <code>origin</code> - that is the default name Git gives to the server you cloned from. <code>git clone</code> adds this implicitly)
```
$ git remote
```

<code>-v</code> - Shows the URL that Git has stored for the shortname to be used when reading and writing to that remote. 
```
$ git remote -v
```

**ADDING REMOTE REPOSITORIES**
`git remote add <shortname> <url>` - To add a new remote Git repository as a shortname you can reference easily
```
git remote add pb https://github.com/paulboone/ticgit
```

