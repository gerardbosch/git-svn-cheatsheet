<!--
    Author: Gerard Bosch (gerard.bosch@gft.com)
    Date: 20/20/2018
-->

# Git SVN Cheatsheet
This document describes how to set up a Subversion repository to work locally using Git, and how to use Git as a Subversion
client. This will allow you to work locally in a more Git workflow and publish the changes to Subversion making it
transparent.

# Prepare the Subversion repository

Check out the trunk your SVN project:

`svn checkout <URL>` You should check out the trunk of your project (e.g. svn checkout
http://svn.example.com/project/trunk)

First of all consider cleaning SVN repository, for example setting up a nice list of ignores. To do that you can use a
file to list all files and folders that must be ignored by SVN. The following command will do that:

`svn propset svn:global-ignores -F <svnignore> .` Last dot (`.`) is the target where properties are applied (current directory)
but this must correspond to the root of the Maven project where POM and other stuff sits.

A typical file for ignores (`svnignore`) can contain the following which will prevent your repository from pollution
with the unnecessary files of common IDEs:

```
target
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.idea
*.iws
*.iml
*.ipr
nbproject/private
build
nbbuild
dist
nbdist
.nb-gradle
```

Once applied, you can verify properties has been successfuly applied with:

`svn proplist -v`

Now you can commit the updates to the SVN repository:

`svn commit -m "Update svn:global-ignores"`


# Using git svn

You can use git as a Subversion client and that will let you to work locally using most of the git features and commit changes to svn repository once ready. In order to do that you must learn a few commands.

The following guide and git documentation provides further details:

- https://git-scm.com/book/en/v1/Git-and-Other-Systems-Git-and-Subversion
- https://gist.github.com/rickyah/7bc2de953ce42ba07116


## Setup Git

Run the following to work with SVN (more info in the avobe links):

`git config --global svn.rmdir true`


## Clone SVN repository

Considering a standard SVN layout:

`git svn clone --user=<svn username> <svn repo url> -T trunk -b branches -t tags`

This will take a considerable amount of time (several hours) depending on the repository size. Just be patient.

Now you must generate your local .gitignore based on the ignore property of svn project. Run the following:

`git svn propget svn:global-ignores > .git/info/exclude`

This will add the contents to the project's git exclude file. This way, you don’t litter the project with `.gitignore` files.


## Getting the latest changes from SVN

The equivalent to git pull:

`git svn rebase`

## Pushing local changes to SVN

Always assure you are in sync with the SVN repository before to commit to SVN:

`git svn rebase`

Then perform the "push" to SVN:

`git svn dcommit`

As reported in the avobe links, the most important is to keep history lienar.
