---
layout: post
title:  "how to use a secondary github account from the terminal"
date:   2021-10-22 15:24:29 -0400
categories: git terminal
---

# how to use a secondary github account from the terminal

## introduction

This has been a big headache for me for a long time. I want to use more than one github account. It is ridiculously difficult. For some reason none of of the various instructions I followed worked, until this one did. Links at bottom to various other resources on the same subject. 

This is set up per-repo. More clever automated solutions exist, see links at bottom. But they never worked for me. 

The instructions that finally cracked it for me: [Quick Tip: How to Work with GitHub and Multiple Accounts](https://code.tutsplus.com/tutorials/quick-tip-how-to-work-with-github-and-multiple-accounts--net-22574)

## assumptions and variables

This is not comprehensive at explaining how to use github, `git` or the terminal from scratch. It is just to fill in existing knowledge. Also I assume you do everything the same way I do. If you don't like it feel free to return to the original tutorials. 

Do it with an empty repo and make sure everything has gone correctly because if you screw it up it's really not so easy to backtrack. 

**Note** replace the emojis

🍅 =  your secondary username 

🥕 = repo name

## one time set up

create a new `ssh` key for use with new account in `~/ssh/id_ed25519-🍅` and add it to the keychain 

- Here is the official docs on how to do that: [Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

add to `~/ssh/.config`:

```sh
Host github-🍅
	HostName github.com
	User git
	IdentityFile ~/.ssh/id_ed25519-🍅
```

## for each repo you want to use your secondary identity

Create new repo locally:

```sh
❯ mkdir 🥕
❯ cd 🥕
❯ git init
Initialized empty Git repository in 🥕/.git/
```

Make a tiny nothing commit:

```sh\
❯ git commit -m "init git"
On branch main
Initial commit
nothing to commit (create/copy files and use "git add" to track)
```

Login to github and create 🥕 repo there

- Be sure to create the  `README.md`

Sync local and remote:


```sh
❯ git remote add origin git@github-🍅:🍅/🥕
❯ git pull origin main
From github-🍅:🍅/🥕
 * branch            main       -> FETCH_HEAD
❯ ls
README.md
```

open the `README.md` make some tiny change and save it.

```sh
❯ nano README.md
```

edit your local  `🥕/git/config` to add (use your correct commit email)

```sh
[user]
	name = 🍅
	email = 000000+🍅@users.noreply.github.com 
[pull]
	rebase = false
[branch "main"]
	remote = origin
	merge = refs/heads/main
```

send local files to remote

```sh
❯ git add README.md
❯ git commit -m "change readme local"

❯ git push origin main
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Writing objects: 100% (3/3), 260 bytes | 260.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github-🍅:🍅/🥕
   b2a484f..9ef3ae5  main -> main
```

## troubleshooting

### primary account should stay authenticated via `ssh`

if you check your ID via `ssh`, it will still say your main account:

```sh
❯ ssh -T git@github.com
Hi CouldBeThis! You've successfully authenticated, but GitHub does not provide shell access.
```

even if you are in a repo set up with secondary account, the primary one will appear. 

It has happened to me once or twice somehow that the secondary account became what showed up here, but I do not know how or why. 

When that happened I re added the key associated with this account to `ssh agent` (Mac): 

```sh
ssh-add ~/.ssh/id_ed25519-CouldBeThis
```

Which solved it. 

### who am I?

If you are having problems, here is how to determine which `git config` is being applied in case somehow there has been an error in that.  

```sh
❯ git config --show-origin user.name
❯ git config --show-origin user.email
```



## other useful links

* [Use multiple Git accounts from a single Linux machine](https://www.attosol.com/manage-multiple-git-accounts/)
* [git - Multiple GitHub accounts on the same computer? - Stack Overflow](https://stackoverflow.com/questions/3860112/multiple-github-accounts-on-the-same-computer)
* [Configuration options \| GitLab](https://docs.gitlab.com/omnibus/settings/configuration.html)
* [A Note on Misconfiguring my SSH Config When Setting Up Two Github Accounts \| Matthew J. Clemente](https://blog.mattclemente.com/2020/09/15/multiple-github-accounts-wildcard-ssh-config-identityfile/)
* [A Practical Guide to Managing Multiple GitHub Accounts \| by Fredrick Mgbeoma \| The Andela Way \| Medium](https://medium.com/the-andela-way/a-practical-guide-to-managing-multiple-github-accounts-8e7970c8fd46)


-  [c-alpha/gituser: Set the user id in a git working copy quickly when using user.useConfigOnly](https://github.com/c-alpha/gituser)


* [Modularizing your git config with conditional includes \| Whisper of the Heartman](https://blog.thomasheartman.com/posts/modularizing-your-git-config-with-conditional-includes)
* [Managing multiple Git profiles - DeepSource](https://deepsource.io/blog/managing-different-git-profiles/)
* [git config - Is it possible to have different Git configuration for different projects? - Stack Overflow](https://stackoverflow.com/questions/8801729/is-it-possible-to-have-different-git-configuration-for-different-projects)
* [Git Your Way: includeIf](https://nicknisi.com/posts/git-includeif/)
* [Basic Git Setup · (think)](https://batsov.com/articles/2020/11/22/basic-git-setup/)
* [Git config with multiple identities and multiple repositories · GitHub](https://gist.github.com/bgauduch/06a8c4ec2fec8fef6354afe94358c89e)
* Longer more complicated instructions using Keychain authentitcation: [How to set Git for multiple GitHub accounts - DEV](https://dev.to/mtilson/how-to-set-git-for-multiple-github-accounts-3kbn)

- [Setting up multiple GitHub accounts, the nicer way](https://medium.com/uncaught-exception/setting-up-multiple-github-accounts-the-nicer-way-5ab732078a7e)
- [microsoft/Git-Credential-Manager-Core](https://github.com/microsoft/Git-Credential-Manager-Core) - Secure, cross-platform Git credential storage with authentication to  GitHub, Azure Repos, and other popular Git hosting services. (No gitlab atm.)
- poss useful: [Lazy man's guide: multiple GitHub HTTPS accounts on Windows](https://dev.to/configcat/lazy-man-s-guide-multiple-github-https-accounts-on-windows-2mad)

[Using git with multiple profiles and GPG+SSH keys \| markentier.tech](https://markentier.tech/posts/2021/02/github-with-multiple-profiles-gpg-ssh-keys/)

#### official documentation

- [Git - Credential Storage](https://git-scm.com/book/en/v2/Git-Tools-Credential-Storage)
- [Git - Git Configuration](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration)
- [Git - git-config Documentation](https://git-scm.com/docs/git-config)
- [Git - git-config - includes Documentation](https://git-scm.com/docs/git-config#_includes)
- [Git - gitcredentials Documentation](https://git-scm.com/docs/gitcredentials)