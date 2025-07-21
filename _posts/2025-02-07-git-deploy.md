---
layout: post
title: Using Git for deployments
tags: git, deployment
---

# Using Git for deployments

The final step to a lot of software engineering projects is taking a Git
repository off your local machine and shoveling it onto a remote server
somewhere to run. There are plenty of nice deployment solutions out there that
allow you to simply `git push` to them, like Github Actions or Gitlab's CI or
Vercel or Netlify or about a billion others. One that doesn't often come up is
simply configuring a repository on a remote server that accepts `git push`es,
which is a shame because if you're willing to hold your nose about manually
configuring servers it's pretty powerful for very little configuration.

This is blog post was mostly written because I keep forgeting the specific
incantation to set it up.

On your remote machine (`user@host`), set up the repository and configure it to
accept pushes without complaining about local changes:

```bash
$ mkdir repo_name && cd repo_name
$ git init --initial-branch=main
$ git config receive.denyCurrentBranch updateInstead
```

and on your local machine:

```bash
$ git remote add deploy user@host:repo_name
```

## rsync

A pretty good alternative option is `rsync`, which will just copy everything in
your local directory over to the remote without having to set up a git
repository or deal with merges. I usually set this up with a makefile:

```makefile
REMOTE=user@host:path/to/repo

copy:
	rsync . ${REMOTE} \
		-rav --progress --del \
		--exclude .git \
		--filter=':- .gitignore'
```

which will copy everything except the `.git` directory, respect `.gitignore`,
and remove files it's not expecting to find.
