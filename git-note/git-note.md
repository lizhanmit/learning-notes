# Git Note

## Basics

![git.png](img/git.png)

Git cannot track the change of binary files, such as images, videos, and Microsoft Word files.  

---

## Command Line

1. Go to project directory. `git init`. Init the repo.
2. `git add .` Add "add" and "update" but not "delete" type of changes to stage.
3. `git add -A` or `git add -all` Add all kinds of changes to stage.
4. `git commit -m "init commit"`
5. `git remote add origin <remote repository URL>` Set the new remote.
6. `git remote -v` Verify the new remote URL.
7. `git push -u origin master` Push up to the remote repository.

- `git log` Check commit history and get "commit_id".
- `git reflog` Check commit history and get "commit_id" in a concise manner.
- `git reset --hard HEAD^` Roll back to the last version that has been committed to the repo.
- `git reset --hard <commit_id>` Roll back to the specific version in the repo.
- `git checkout -- <file_name>` Drop changes to the file in workspace.
- `git reset HEAD <file_name>` Take the file from stage back to workspace.

---

## Useful Resources

- [Fork a repo](https://help.github.com/articles/fork-a-repo/)
- [Syncing a fork](https://help.github.com/articles/syncing-a-fork/)
