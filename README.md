# 2.2 - Recording Changes to the Repository

* Typically, you'll want to start making changes and committing snapshots of those changes into your repository each time the project reaches a state you want to record.
* Each file in your working directory can be in one of two states:
	* **Tracked** - Files that were in the last snapshot, as well as any newly staged files; they can be unmodified, modified, or staged. Trackes files are files that Git knows about.
	* **Untracked** - Everything other than tracked files - Any files in your working directory that were not in your last snapshot and not in your staging area. When you first clone a repository, all of your files will be tracked and unmodified because Git just checkout them out and you haven't edited anything. As you edit files


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

