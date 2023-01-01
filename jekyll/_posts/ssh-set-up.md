# `ssh` set up

see also: [ssh-newkey.sh](https://github.com/CouldBeThis/_scripts/blob/main/snippets/ssh-newkey.sh) (current =  [dbe3133](https://github.com/CouldBeThis/_scripts/commit/dbe3133b978a7233f7a9c745747dc7994c677959))

## on local system, generate key

```zsh
ssh-keygen -t ed25519 -C "localkey" -f "~/.ssh/ed25519"

## start ssh-agent
eval "$(ssh-agent -s)"

## add key to agent
ssh-add ~/.ssh/ed25519

ssh-copy-id remoteuser@remoteip
##--> password is remote user password
```

```zsh
# List fingerprints of currently loaded keys:
ssh-add -l
```

```zsh
# Add a key to the ssh-agent and the keychain:
ssh-add -K path/to/private_key
```

```zsh
#list keys
ls -al ~/.ssh/*.pub
```

## references

- [scp man page - openssh-clients - General Commands | ManKier](https://www.mankier.com/1/scp)



## TODO 

### add later

- running `openssh` locally

