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

