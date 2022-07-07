# Using a code repository (_git, GitHub_)

## Why git?

![git logo](../pic/git-logo.png)

Using just the CG IDE in your browser is convenient: it takes care of saving your code, you can even access all your earlier submissions via the `Results / History` menu. However, you should not rely solely on this. Have a local collection of all your code you have written. Moreover, having just a bunch of files in some folders on your hard drive quickly becomes unmanagable, so use a _source code management_ tool (also known as a _version control system_). While there are (were?) other tools as well, `git` became a de-facto monopoly in this field.
Benefits:

* Many CG puzzles require similar concepts. But you can reuse code only if you can find it. This is much easier if you have everything together, with full text search, etc.
* The code you write is yours. Whatever happens to CodinGame or your CG account, you shall never loose your code.
* Sometimes we screw up things and need to revert changes. With `git` it is very easy to turn back the wheel of time to any point when you commited code into the repository. You never need to worry of overwriting or modifying something you regret later.

## Why GitHub?

![octocat](../pic/octocat.png)

While having a local version control is fine, if you loose your filesystem, you are still busted. GitHub is the largest free code hosting platform in the cloud. (But not the only one, so feel free to use [GitLab](https://about.gitlab.com/), [BitBucket](https://bitbucket.org/), etc instead.)

* Syncing up your local repo with GitHub provides instant cloud backup for your code.
* You can access your code easily from multiple devices. You can still maintain a 'single source of truth', instead of relying on file dates with a risk of accidentaly overwriting the most recent version with an older file.
* On a code hosting site there is much focus on ease of collaboration, code and knowledge sharing and teamwork. However, CodinGame code of conduct prohibits to share your solutions publicly, as this would ruin the challenge for others. So don't do that, always keep your CG-related repos `private`.

## Installing git

There are plenty of git tutorials on the web, so I will remain brief here.

* Depending on your OS, `git` might come already preinstalled. Try it out with `git --version`.
* Download it from the [git website](https://git-scm.com/) and install. Make sure that your git installations `cmd` folder is added to your `PATH`.
* While it comes with a Client GUI, we will use only the command line (especially as we will soon move to `VS Code` IDE, which has excellent `git` support)
* Do some basic one-time setup:

```bash
git config --global user.name "Your Name"
git config --global user.email yourmailaddress@yourdomainname.com
git config --global init.defaultBranch main
```

Registration on [GitHub](https://www.github.com/) is quite straightforward and you are good to go. Besides the `Free` plan they have a paid `Pro` service, but we won't need it. GitHub also provides a [command line tool](https://cli.github.com/), but its usage is optional and not really essential.

## Setting up you 'CG solutions' code repo

Usually one should make a separate git repo for every project, keeping only the project-related files together. However, you can have hundreds of CG solutions (1 file each) making separate repo would be silly. I have a single repo for all my php CG solutions (600+ files), plus another repo for all the other languages (26 languages with typically ~40 simple solutions each). With such number of files keeping things organized is a must. For example I use directories per CG game type and per difficulty with a strict naming convention also on filenames:

![folders](../pic/repo-folders.png)

* Create a directory and initialize your repo:

```bash
mkdir reponame
cd reponame
git init
```

* Sign in to your `github` account  and [create a new repo](https://github.com/new). Preferably choose the same name for the repo as the local directory name you have chosen above.
* __For CG puzzles: make sure sure you set your repo to be 'private'__.
* Now connect your local repo with the github repo yopu created:
    * At first time you need to provide your github credentials. Later you don't need to type in passwords or access tokens if usinf the same machine.

```bash
git remote add origin "https://github.com/your-github-profile-name/reponame.git"
git pull origin main
git status
```

* to make sure everything is OK, let's create php source file in the repo directory and commit it to the repo.

```bash
git git add -A
git commit -m "my first commit"
git push origin main
```

* Now if you visit your account on github, the new php file should appear under the repository you created.

## Useful links

* [Git](https://git-scm.com/)
* [Pro Git](https://git-scm.com/book/en/v2), a free book: at least the first few chapters are definitely worth reading.
* [GitHub](https://www.github.com/)

## Coming next

Using an IDE (_Visual Studio Code_)
