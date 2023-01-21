***
# What is best practice?

Best practice is a method or technique that is accepted by most of the users as the most efficient way to do things. It is a some kind of standard. 

***
## What is Git?

Git is a modern version control system. It provides functionality to store code repositories on different machines at the same time and synchronize changes in different ways when it is needed.

***
## What is GitHub?:

GitHub is a service that can store your GitHub repositories on the internet and provide functionality to pull and push from them.

---

# Best practices

- Branching strategy

***
## Branching strategy

- #### Work in "feature" branch 
	 If you work in separate branch you can't break anyone else code.
	 You can do whatever you want and merge with other branches after you will be sure that your code is working fine

- #### Branch out from "develop"
	 Using develop branch gives you opportunity to test code before merge with master.

- #### Never push into develop or master branch. Make a pull request 
- to notify your team that feature is done, and gives time to review it before merge.

- #### Update your local develop branch and do interactive rebase before pushing your feature and making a Pull Request.
	 Update your develop to always work with actual state of source code.
	 Do rebase after finishing feature, to make your git history clear and avoid conflicts

- #### Delete local and feature branches after merging.
	 You don't need to store a list of useless branches.

- #### Before making a Pull Request, make sure that your feature branch builds successfully and passes all tests
	 Push only tested, stable code that won't break parent branches.

 ***

## .gitignore template
[project-guidelines/.gitignore at master · elsewhencode/project-guidelines (github.com)](https://github.com/elsewhencode/project-guidelines/blob/master/.gitignore)
 
***
## Git workflow

- #### Checkout a new branch
	- checkout creates new local branch and clone files from remote one
```
git checkout -b <branchname>
```

- #### Commit little changes
	- it helps you in troubleshooting, because it is easier to find a bug in one small change than bigger one
```
git add <file1> <file2> ...
git commit
```

- #### Sync with remote to get changes you've missed
```
git checkout develop
git pull
```

- #### Update your feature branch with latest changes from develop by interactive rebase.
	- You can use --autosquash to squash all your commits to a single commit
```
git checkout <branchname>
git rebase -i --autosquash develop
```

- #### If you have conflicts, resolve them 
	- [Resolving a merge conflict using the command line - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-using-the-command-line)
```
git add <file1> <file2> ...
git rebase --continue
```

- #### Push your branch. Rebase will change history, so use -f or --force-with-lease if somebody works on the same branch
	- rebase clear history of previous commits
```
git push -f
git push --force-with-lease
```

- #### Remove feature branch if it is finished
```
git branch -d <branchname>
```

***
## Commit messages

- #### Separate the subject from the body with a newline between the two
	 If you will try git short log, instead of git log, you will see a long list of commit messages, consisting of the ID of the commit, and the summary only.

Only subject
```
Fix typo in introduction to user guide
```
Or subject with explanation
```
Derezz the master control program

MCP turned out to be evil and had become intent on world domination.
This commit throws Tron's disc into MCP (causing its deresolution)
and turns it back into a chess game.
```

- #### Limit subject line to 50 characters 
	 50 characters is not a hard limit, just a rule of thumb. Keeping subject lines at this length ensures that they are readable, and forces the author to think for a moment about the most concise way to explain what’s going on.

- #### Capitalize the subject line
	 Begin all subjects lines with a capital letter

	 Accelerate to 88 miles per hour
		 instead of 
	 accelerate to 88 miles per hour

- #### Don't use special characters when it is not needed
	 Sometimes punctuation is not needed. (you try to keep 50 chars or less)

	 Open the pod bay doors
		instead of
	 Open the pod bay doors.

- #### Use the imperative mood in the subject line
	 spoken or written as if giving a command or instruction

	 Accelerate to 88 miles per hour
	 Open the pod bay doors
	 Fix typo in introduction to user guide

- #### Wrap the body at 72 characters
	 Git never wraps text automatically 

- ### Use the body to explain what and why vs. how 
	 It can tell more than "git diff" command