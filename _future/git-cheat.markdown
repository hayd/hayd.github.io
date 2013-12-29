|action|command
|:---|:----|
|
|*Fetching from upstream*|
|go to master| `git checkout master`
|get the latest from upstream| `git fetch upstream`
|update master with upstream| `git merge upstream/master --ff-only`
|push to github|`git push origin`
|
|*Creating branches*
|go to master| `git checkout master`
|create and move to new branch|`git checkout -b branch_name`
|check which files have changed| `git status`
|check what's changed| `git diff`
|commit all changes to branch| `git commit -a -m 'commit description'`
|push branch to github| `git push origin branch_name`
|
|*Updating branches*
|go to the branch| `git checkout branch_name`
|update branch\*|`git rebase master`
|abort rebase| `git rebase --abort`
|force push to github\*\*| `git push -f branch_name`
|
|*Branch tidying*
|see last commits| `git log`
|squash 3 commits\*| `git rebase -i HEAD~3`
|force push to github\*\*| `git push -f branch_name`
|
|*Making a backup branch*
|make backup branch| `git branch branch_name_backup`
|
|*Troubleshooting*
|remove last 3 commits\*| `git reset --hard HEAD~3`

---

\*Be careful when doing this, perhaps consider making a backup branch.

\*\* Be careful when doing this, ensure no-one is working off your branch, and if there is a current pull request that it is not going to be merged.
