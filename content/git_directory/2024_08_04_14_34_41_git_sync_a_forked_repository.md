+++
title = "Git Sync a Forked Repository"
author = ["Kiran"]
date = 2024-08-04T14:34:00-04:00
tags = ["git"]
draft = false
css = "../../zcustom.css"
+++

[Reference Video](https://www.youtube.com/watch?v=oJoyyTxtRRg)


## Git Sync a Forked Repository {#git-sync-a-forked-repository}

To sync a forked repository with the upstream repository (the original repository from which you forked), you can follow these steps:

1.  Add the parent repository/upstream repository if not already added:

<!--listend-->

```linux
git remote add {upstream/parent} {ssh original git repo}
```

-   {upstream/parent} is user defined the name given to the original repo.

2.   Once the original repository is added, [Git Pull]({{< relref "2024_08_04_12_24_44_git_pull.md" >}})  the original repository to the local repository:

<!--listend-->

```linux
git pull {upstram/parent} {branch}
```

-   {branch} is the name of the branch, it could be main or the branch name you want to sync the local repository.

This is will sync the original forked repository with the local repository.

3.  Push the changes to your forked remote repository.

<!--listend-->

```linux
git push {origin} {branch}
```

-   {origin} is the name of your forked remote repository. You can find the forked remote repository name using the command:

<!--listend-->

```linux
git remote -v
```

-   {branch}, again is the name of the branch you want push the changes. If you no branches you can push the changes to **main** branch.

Note: it is a good practice to regularly sync your fork with the upstream repository, you ensure that your fork stays up-to-date with the latest changes from the original project.
