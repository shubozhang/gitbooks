## Configuration

``git config --global user.name "k1000"``

``git config --global user.email "john.doe@email.com"``

``git config -l``

//view git configs

``git config --global --unset user.email`` 

//delete a config


## Remote Repo Commands

 ``git init`` 
 
 //create an empty Git repository or reinitialize an existing one

`` git clone https://K26511@bitbucket.finra.org/scm/mrp/fi046.git``

//create an empty Git repository or reinitialize an existing one

## Making Changes 

``git status``

// show the working tree status: lists all new or modified unstaged files

``git checkout --<file>``

//discard changes in the working directory

``git add <file.name> `` 

//add file contents to the index

``git add -A `` 

//stages All (modified, deleted, untracted, and new files)

``git add -u``  

//stages modified and deleted, without new

``git reset HEAD <file>``

// unstage the changes

``git commit -m ['add a message']``

//record changes to the repository


## Check-in code
``git fetch``

// download objects and refs from remote repository

``git diff origin/master master ``

//Show changes between commits, commit and working tree, etc

``git pull ``

// fetch from and integrate with a local branch

``git push origin/master master``

// update remote master branch

``git log``

// show all commit logs
