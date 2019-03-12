---
layout: page
title: "How to: run a basic git development workflow"
date: 2019-03-07
---

First you want to maintain a local cloned copy of the repository: run `git clone https://repo_url` to an appropriate directory. To begin working, carry out the following steps:

1. Run `git pull origin master` to update the local repository with the latest changes.
2. Create a temporary branch to work on. `git checkout -b my_feature_branch`. 
3. Do the work.
4. Stage all changes: `git add -A`.
5. Commit the changes: `git commit -m "my commit message"`.
6. Push the feature branch with `git push origin my_feature_branch`. 
7. Using the browser, compare the changes, create a pull request, and merge the changes if you're satisfied. 
8. Switch back to the local master branch with `git checkout master`.
9. Update the local master `git pull origin master` to synchronise your local repository with the latest changes. 
10. Delete the local feature branch: `git branch -d my_feature_branch`. 

Other useful commands: `git branch -v` to see all branches. `git merge my_feature_branch` to directly merge feature branch onto master branch locally. This is followed by `git push origin master` to directly push to the origin without a pull request. `git status` to see what's going on.

Setting up a forked local repository to track changes in the original repository: 

1. `git remote -v` lists remote repositories. Normally these will only contain the `origin` url. If you have forked a repo and want to track the original, run:
2. `git remote add upstream https://original_repo_url`. Then `git remote -v` will show both `origin` and `upstream` urls.
3. `git fetch upstream` followed by `git merge upstream/master` gets changes and merges them into the local master branch.
4. After doing work on the code, add, commit, `git checkout master`, `git merge my_feature_branch`.
5. `git fetch upstream` followed by `git merge upstream/master` to resolve any conflicts before `git push origin master` to push changes to your forked repo.
6. From the forked repo in the browser, create a pull request for the changes to be merged into the upstream repo.
