***
# What is?

Git is a modern version control system. It provides functionality to store code repositories on different machines at the same time and synchronize changes in different ways when it is needed.

***
## What is GitHub?:

GitHub is a service that can store your git repositories on the internet and provide functionality to pull and push from them.

---

# Create private GitHub repo and .gitignore

## Configuration
- Windows 10
- Oracle VM VirtualBox 7.0.4
- NAT network
	- host ip   - 10.0.2.5
- Ubuntu 22.04 Server as guest OS

***

## Create private GitHub repo

#### Register or log in to GitHub account 
- https://github.com
![Log in](https://github.com/qqwerty222/obsidian/blob/main/Git/screenshots/Log_in.png)

#### Open repositories page and click New
![New repo](https://github.com/qqwerty222/obsidian/blob/main/Git/screenshots/New_repo.png)

#### Create repository
1) Set repository name
2) Set description if needed
3) Pick "Private"
4) Click "Create repository"
![Create repo](https://github.com/qqwerty222/obsidian/blob/main/Git/screenshots/Create_repo.png)

## Generate ssh key

- generate
```
bohdan@test-host:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/bohdan/.ssh/id_rsa): /home/bohdan/.ssh/git
Enter passphrase (empty for no passphrase):
```

- show public key
```
bohdan@test-host:~$ cat ~/.ssh/git.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC3ZG2no7O/GYvFapvApCXuCN1vkaFHoQ7BJdJJ2Eu
...
```

- add key to local ssh-agent
```
bohdan@test-host:~$ ssh-add /home/bohdan/.ssh/git
Enter passphrase for /home/bohdan/.ssh/git:
Identity added: /home/bohdan/.ssh/git (bohdan@test-host)
```

## Add ssh key to GitHub account

#### Open SSH and GPG keys settings
1) Click on user icon
2) Click "Settings"
3) Click "SSH and GPG keys"
4) Click "New SSH key"
![Add ssh](https://github.com/qqwerty222/obsidian/blob/main/Git/screenshots/Add_ssh.png)

#### Add ssh public key generated in previous block
1) Set name (title)
2) Input public key in "Key" field
3) Click "Add SSH key"
4) Input password once again
![Add ssh2](https://github.com/qqwerty222/obsidian/blob/main/Git/screenshots/Add_ssh2.png)

You will see this on the "SSH and GPG keys" page
![ssh key](https://github.com/qqwerty222/obsidian/blob/main/Git/screenshots/ssh_key.png)


## Clone GitHub private repo and push .gitignore

#### Clone repository
```
bohdan@test-host:~$ git clone git@github.com:qqwerty222/private_rep.git
Cloning into 'private_rep'...

bohdan@test-host:~$ cd private_rep
bohdan@test-host:~/private_rep$
```

#### Change branch, add your identity and save link to repo as origin
```
bohdan@test-host:~/private_rep$ git branch -M main
bohdan@test-host:~/private_rep$ git config --global user.name "test-host"
bohdan@test-host:~/private_rep$ git config --global user.email "test.host@gmail.com"
bohdan@test-host:~/private_rep$ git remote add origin git@github.com:qqwerty222/private_rep.git
```

#### Create README.md, file to ignore, and .gitignore
- to ignore file just put path to it in one row of  .gitignore
```
bohdan@test-host:~/private_rep$ echo "Some text in README" > README.md
bohdan@test-host:~/private_rep$ touch file_to_ignore
bohdan@test-host:~/private_rep$ cat > .gitignore
file_to_ignore
bohdan@test-host:~/private_rep$ ls -a
.  ..  file_to_ignore  .git  .gitignore  README.md
```

#### Check status of local repo
- as you can see there is no file_to_ignore
```
bohdan@test-host:~/private_rep$ git status
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore
        README.md

nothing added to commit but untracked files present (use "git add" to track)
```

#### Add and commit changes
- commit is some kind of package, you can fill it with changes and push to repo
```
bohdan@test-host:~/private_rep$ git add .gitignore README.md
bohdan@test-host:~/private_rep$ git commit -m "add README and .gitignore files"
[main (root-commit) f7a8de7] add README and .gitignore files
 2 files changed, 1 insertion(+)
 create mode 100644 .gitignore
 create mode 100644 README.md
bohdan@test-host:~/private_rep$ git status
On branch main
nothing to commit, working tree clean
```

#### Push commit to repo
```
bohdan@test-host:~/private_rep$ git push origin main
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (4/4), 286 bytes | 286.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:qqwerty222/private_rep.git
 * [new branch]      main -> main
```

## Check new files in repo

- Log in to your account
- Click "Repositories"
![New repo2](https://github.com/qqwerty222/obsidian/blob/main/Git/screenshots/New_repo2.png)

- Click on repo name
![New files](https://github.com/qqwerty222/obsidian/blob/main/Git/screenshots/new_files.png)
