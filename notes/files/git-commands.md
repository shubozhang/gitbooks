# Git Commands

#### 1. Configuration
#### 2. Remote Repo Commands
#### 3. Make Changes
#### 4. Branching and Merging
#### 5. Sharing and Updating Projects
#### 6. Git Workflow
#### 7. Git Alias


#### Configuration
```bash
git config --global user.name "[name]"
git config --global user.email "[email address]"

#set http proxy in git config
git config --global http.proxy http://proxyuser:proxypwd@proxy.server.com:8080 
git config -l //view git configs
```

#### Remote Repo Commands
```bash
git init //initialize a git repo in the current directory

# sets the remote to "notorigin"
git clone https://github.com/user/repo.git -o notorigin 

# set a new remote
git remote add origin https://github.com/user/repo.git 

# change the remote URL for origin
git remote set-url origin https://github.com/user/repo2.git 

# update the local repo with remote tracking branches. 
# Use this if you don't see you branch using git branch -r
git remote update 
          
# verify new remote            
git remote -v 

# delete remote repo folder 
git rm -r --cached .idea, and then commit  
```

#### git status
```bash
git status // lists all new or modified unstaged files
git status -b // show the branch and tracking info
```

#### git diff
```
Various ways to check your working tree
git diff            //changes in the working tree not yet staged for the next commit
git diff --cached   //Changes between the index and your last commit
                    //what you would be committing if you run "git commit" without "-a" option
git diff HEAD       //Changes in the working tree since your last commit 
                    //what you would be committing if you run "git commit -a"
git diff --staged  //show the difference between staging and the last file version 
```
```
3.2.2 Comparing branches
git diff topic master    //Changes between the tips of the topic and the master branches
git diff topic..master   //Same as above
git diff topic...master  //Changes that occurred on the master branch since when the topic branch was started off it
git diff-tree -r --root 40a450274b128348ec30d69abc51981ea7be20df -- see a full diff-tree
it diff [first-branch]...[second-branch] // Shows content differences between two branches
```

```
git show [commit] // Outputs metadata and content changes of the specified commit
```

* diff another branch on one file
```bash
git diff ..develop src/rest/server.js
```

#### git add
```
 git add <file.name>  //start tracking file.name
 git add -A  //stages All (modified, deleted, untracted, and new files)
 git add -u  //stages modified and deleted, without new
 git add . //stages new and modified, without deleted
```

#### git reset
```
git reset HEAD [file.name] //unstage a single file
git reset --hard HEAD //undo commits permanently, reset your local branch to the HEAD commit 
git reset --soft //undo a commit, does not touch the index file or the working tree at all,
                 //but resets the head to <commit>
git reset [commit] // Undoes all commits afer [commit], preserving changes locally
git reset --hard [commit] // Discards all history and changes back to the specified commit
```

#### git rm
```
git rm --cached //remove file from the index but not working dir
git rm $(git ls-files --deleted) // With red color(--deleted files), "git add -A" 
                                 //Deleted files become green color. Then "git commit -m 'delete files'"
```  

#### git commit
```
git commit -m ['add a message'] //files are listed in stating area
```

#### git branch
```
git branch // list local branches
git branch [branch_name] //create a new branch
git branch -d [branch_name] //delete a branch (locally !!!)
                            //The branch must be fully merged in its upstream branch, or in HEAD if no upstream 
                            //was set with -track or --set-upstream
git branch -D [branch_name] // delete a branch irrespective of ites merged status   
git push origin --delete branch_name // delete a remote branch in GitHub
git push origin : branch_name // delete a remote branch in GitHub
git branch -r //view available local remote branches
git branch -vv //view the tracking branch
git branch -a //List both remote-tracking branches and local branches
git branch -u //setup upstream
git branch --contains [<commit>] //Only list branches which contain the specified commit (HEAD if not specified). 
                                 //Implies --list.
git branch --merged [<commit>] //Only list branches whose tips are reachable from the specified commit 
                               //(HEAD if not specified). Implies --list.
git branch --no-merged [<commit>] //Only list branches whose tips are not reachable from the specified commit 
                                  //(HEAD if not specified). Implies --list.
```
#### git checkout
```
git checkout [file_name] // Discard changes in working directory
git checkout [branch_name] // switch to a new branch
git checkout master  // switch to master branch
git checkout -b [branch_name]// Create a new branch named [new_branch] and start it at [start_point]
git checkout -B [branch_name]// Creates the branch <new_branch> and start it at <start_point>; 
                //if it already exists, then reset it to <start_point>.
git checkout -b [branch_name] master // create a new branch and start it at master                
git checkout --track origin/serverfix // change the tracking branch    
git checkout HEAD --folder1/pom.xml //reset a single file to the HEAD commit
```

#### git merge
```
git merge [branch_name]  // merge [branch_name] into master
git merge [branch_name] --commit // Perform the merge and commit the result.
git merge [branch-name] --no-commit // perform the merge but pretend the merge failed and do not autocommit, 
                                    // to give the user a chance to inspect and further tweak the merge 
                                    // result before committing.
git merge --abort // only works when there are conflicts for git merge
```

#### git log
```
git log // show all changes we have committed so far
git log -p -2 //-p show difference introduced in each commit; -2 limit to 2 enties.
git log --follow [file] // Lists version history for a file, including renames
```

#### Save Fragments: Shelve and restore incomplete changes
```
git stash // Temporarily stores all modified tracked files
git stash list //Lists all stashed changesets
git stash pop // Restores the most recently stashed files
git stash drop //Discards the most recently stashed changeset
```

#### Sharing and Updating Projects
```
5.1 git fetch
git fetch [bookmark] // Downloads all history from the repository bookmark
```

```
5.2 git push
git push [alias] [branch] // Uploads all local branch commits to GitHub
```

```
5.3 git pull
git pull // Downloads bookmark history and incorporates changes
or git push origin :<branchName>
git push  -- commit master
git push origin --delete <branchName>  -- delete a remote branch
```

```
git clean -f -X -d -- remove all unstaged files (great for cleaning up after a build)
git show-ref -- shows the refs for the repo (still figuring this out)
git filter-branch --prune-empty --subdirectory-filter lib master -- split out a sub dir into a repository
```



#### Git Workflow
```
6.1 git checkout -b john-dev-2 origin/release-1.0.0 -- check out from a remote branch
```

```
6.2 git push -u origin john-dev-2 -- push feature branch to remote repo
```

```
6.3 git pull origin john-dev-2 -- pull feature branch
```

```
6.4 git push origin :john-dev-2 -- delete the remote feature branch (see the git-scm page)
```



#### Git Alias
```
Edit your ~/.gitconfig to add useful git aliases. see (Effective pull requests and other good practices for teams using github)
[alias]
    hist = log --color --pretty=format:\"%C(yellow)%h%C(reset) %s%C(bold red)%d%C(reset) 
    %C(green)%ad%C(reset) %C(blue)[%an]%C(reset)\" --relative-date --decorate
```

#### Git troubleshooting
```
to debug problems with an HTTP proxy set the environmental variable GIT_CURL_VERBOSE=1
The ours merge technique to replace master
git checkout seotweaks
        git merge -s ours master  
        git checkout master  
        git merge seotweaks
```

#### Git empty repository

You have an empty repository
To get started you will need to run these commands in your terminal.

Configure Git for the first time
```
git config --global user.name "John Doe"
git config --global user.email "johndoe@yahoo.com"
git config --global -l
```
Working with your repository
I just want to clone this repository
If you want to simply clone this empty repository then run this command in your terminal.
```
git clone http:github.bryanshubo.git
```
My code is ready to be pushed
If you already have code ready to be pushed to this repository then run this in your terminal.
```
cd existing-project
git init
git add --all
git commit -m "Initial Commit"
git remote add origin http:github.bryanshubo.git
git push origin master
```

My code is already tracked by Git
If your code is already tracked by Git then set this repository as your "origin" to push to.
```
cd existing-project
git remote set-url origin http:github.bryanshubo.git
git push origin master
```
git diff --name-only --diff-filter=U


delete local branch:
git branch -d the_local_branch

```
git checkout -b local-branch-name origin/remote-branch-name // checkout remote-branch-name and name it as local-branch-name

//discard all local changes
git checkout .

//If you want your branch B to look exactly like branch A. You could just do a reset --hard
git checkout branch-B
git reset --hard branch-A
```

### Git Command Tricks
```
A; B    Run A and then B, regardless of success of A
A && B  Run B if A succeeded
A || B  Run B if A failed
A &     Run A in background.
```
