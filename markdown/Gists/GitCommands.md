Last updated: 2015/11/09

**remote repository <--- fetch/clone/push ---> local repository(including local branches and remote branches) <--- checkout/commit/merge ---> work zone(one of the branch in local repository)**

### git init ###
Initialized empty Git repository in your corrent working diretory.

### git status ###
Check the repository's status:

1. If there have untracked files
2. If there have unstaged changes
3. If there have uncommit changes
4. If the commit HEAD is ahead of or behind of the remote repository


### git log [--pretty=oneline]###
Show the commit history

	--pretty=oneline: Display the log more nice and simple

### git branch [-r/-a] ###
Show the local branches in local repository  

	-r: Only show the remote branches in local repository
	-a: Show the local branches and remote branches in local repository

### git branch NEW-BRANCH-NAME ###
Create a new local branch from the current branch in working zone.

### git checkout BRANCH-NAME ###
Checkout a branch to working zone. The working zone lists all of the files in the current branch on your file system. Changes on these files only are applied to the current branch in the working zone.

### git checkout -b NEW-BRANCH-NAME ###
Create a new local branch and checkout it out to working zone.

### git checkout -b NEW-BRANCH-NAME HOST-NAME/BRANCH-NAME ###

### git add FILE-NAME ###
Stage the changed file

### git commit -m "COMMIT MESSAGE" ###
Commit the staged files

### git commit -a -m "COMMIT MESSAGE" ###
Stage and commit all of the changed files

### git push [HOST-NAME] [LOCAL-BRANCH][:][REMOTE-BRANCH] ###
Push the local branch(s) to the remote branch(s).

	The HOST-NAME can be omitted if the repository only has one remote host.

	If the LOCAL-BRANCH is omitted, it will delete the REMOTE-BRANCH.

	If both LOCAL-BRANCH and the colon are omitted, it will push the current branch to the remote branch with the same name. If the remote branch doesn't exist, it will create it.

	git push: If the current branch in working zone have created the tracking with remote branch, it will push the current branch to the remote branch. Else it will execute according to the git configuration: 

		git config [--global] push.default matching: ppush local branches to the remote branches that already exist with the same name.
		git config [--global] push.default simple: pushes the current branch to the corresponding remote branch that 'git pull' uses to update the current branch. If the current branch is set to track the remote branch, it will fail.

### git pull HOST-NAME REMOTE-BRANCH:LOCAL-BRANCH ###
Pull the remote branch to the local branch.

### git branch --set-upstream-to=HOST-NAME/BRANCH-NAME BRANCH-NAME ###
Create the tracking between local branch and remote branch. So that you can use the `git pull` without specifying the remote branch and local branch. It will pull the tracked remote branch to the current local branch.

### git push --set-upstream HOST-NAME BRANCH-NAME ###
Create the tracking between local `BRANCH-NAME` and remote `BRANCH-NAME`. It will track both of the `pull` and `push` and override the `pull` tracking that created by `git branch --set-upstream-to=HOST-NAME/BRANCH-NAME BRANCH-NAME`. So that you can use the `git pull` and `git push` without specifying the remote branch and local branch. It will pull(push) the tracked remote(local) branch to the current local(remote) branch.

### git merge [HOST-NAME/]BRANCH-NAME ###

### git reset --hard COMMIT-ID ###

### git checkout -- FILE-NAME ###

### git reset HEAD FILE-NAME ###

### git rebase BRANCH-NAME ###

### git diff FILE-NAME ###

### git diff BRANCH1-NAME BRANCH2-NAME ###

### git remote add HOST-NAME URL ###

### git remote show HOST-NAME ###

### git clone URL ###

### git config user.name "USER-NAME" ###

### git config user.email "EMAIL-ADDR" ###

### git remote set-head origin -a ###

### git fetch origin [BRANCH-NAME] ###

### git rev-parse --abbrev-ref HEAD ###

### git cherry-pick COMMIT-ID ### 