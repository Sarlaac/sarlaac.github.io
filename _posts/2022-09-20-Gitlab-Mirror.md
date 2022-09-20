---
title: Mirror a Gitlab repository to Github
date: 2022-09-20 9:00
categories: [howto]
tags: [howto] # always lowercase
---

# Mirror a Gitlab repository to Github

To start off create a new empty repository in Github. Do not add a readme or any other files.

![image](https://user-images.githubusercontent.com/92181960/191295117-9e63da4c-9cee-4717-a06a-95301c14da4d.png)

- Copy the SSH clone URL - git@github.com:Sarlaac/sandbox-mirror.git
- Update the URL to add ssh:// and change the colon after github.com

```ssh://git@github.com/Sarlaac/sandbox-mirror.git```


---
Now let’s logon to Gitlab and go to the repository you want to mirror.
- In the repository, go to Settings > Repository > Mirroring repositories
- Enter the URL we created earlier.
- Click on detect host keys
- Switch from password to SSH public key
- Click on the Mirror repository button.

![image](https://user-images.githubusercontent.com/92181960/191296139-8b815f60-c574-4f9a-a4c3-f12315a60750.png)


There should be an entry under Mirrored Repositories
- Copy the SSH public key.

![image](https://user-images.githubusercontent.com/92181960/191296386-ae46cb65-18c2-4bed-8692-f1516f14c84d.png)


---
Back on Github, go to the repositories settings.
- Add the key as a deploy key.
- Be sure to check the allow write access.

![image](https://user-images.githubusercontent.com/92181960/191296603-3bfc3620-6fe3-43d3-95bf-3739192bdd3d.png)

That’s it! Test the setup by committing a change on the Gitlab repository and go look for it to be mirrored to the Github repository.
