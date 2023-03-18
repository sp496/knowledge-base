# Git Notes

## Pushed .idea directory into remote respository from pycharm

To remove the .idea directory from the remote repository, you can use the git rm command with the --cached flag to
remove it from the repository but keep it in your local working directory:

```bash
git rm --cached .idea
```

Then, commit the change and push it to the remote repository:

```bash
git commit -m "Removed .idea directory"
git push
```

To prevent the .idea directory from being pushed again in the future, you can add it to your .gitignore file. 

