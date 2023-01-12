---
title: "Restore from a Mirror"
date: 2022-09-20 15:00
categories: howto git
tags: git # always lowercase
---

# Restore Repository using a Git Mirror

If you followed the guide on [setting up a mirror](https://sarlaac.github.io/posts/gitlab-mirror/) as a real-time backup, you may now be wondering how can I restore a lost or corrupt repository using the mirror. For this how-to we will use Github (source) as the mirror and Gitlab (destination) as the repository to restore. Please Note: this method assumes whatever corrupted the source repository was not also mirrored, if that is the case, snapshots or backups are what you will want.

## Option 1

1. Copy the clone URL for the mirror - git@github.com:Sarlaac/sandbox-mirror.git
2. Clone the copy to a temporary local repository

```git clone git@github.com:Sarlaac/sandbox-mirror.git```

3. Update the remote URL to the repository you want to restore.

```
git remote set-url <name> <newurl> [<oldurl>]
git remote set-url origin git@gitlab.com:sarlaac/sandbox-demo.git git@github.com:Sarlaac/sandbox-mirror.git
```

4. Check that the new URL is in place.

```git remote -v```
  
5. Push the local copy to Gitlab, restoring the repository. You may need to force-push depending on the state of the destination.
