# Git and Github

## Overview
**Git** is a version control system that can manage a development project's source code history and **GitHub** is a cloud-based platform built around the Git tool.

The main aspect of Git to understand is that it is **distributed**: each developer duplicates ("clones") the entire codebase and history from the Github server to their local machine.

Source code is stored in Git **repositories**.  An enterprise Github server such as IBM's has **organizations** which is a shared space where teams can collaborate over many projects at once.  Organizations contain repositories and repositories contain code.  A user must exist within the organization to have access to a repository and repository access is management by repository administrators.  (Note: that Github also uses the term **Owner** to mean an organization.)

IBM maintains an on-premises enterprise version of Github here: [IBM's enterprise Github](https://github.ibm.com){target=\_blank}.  Unlike traditional VCS where code is stored centrally on a server and individual files are checked out locally (and in some VCS, locking the file preventing others to edit), the entire repository of code in Github including all branches and history is replicated ("cloned") locally on each developer's workstation.

## Working with Git

!!! note
    Setup Requirement! When you set up Git first the first time on a machine, you need to set your name and email.

Git requires the username and email each time your commit code.  As a convenience this is set once instead of requiring it each time.  You can set these globally (common) under your user directory on your machine or per repository (less common).

**To set your global username/email configuration:**

1. Open the command line.

1. Set your username:

    `git config --global user.name "FIRST_NAME LAST_NAME"`

1. Set your email address:

    `git config --global user.email "MY_NAME@example.com"`

**To set repository-specific username/email configuration:**

1. From the command line, change into the repository directory.

1. Set your username:

    `git config user.name "FIRST_NAME LAST_NAME"`

1. Set your email address:

    `git config user.email "MY_NAME@example.com"`

1. Verify your configuration by displaying your configuration file:

    `cat .git/config`

Actions such as [branching and committing](lINK TO WORKFLOW) are performed in your local repository and do not require a connection to the server.  The server connection is required to clone the repository from the Github server to your local machine and to update and push the code changes from and to the server respectively.

**Basic flow of GIT:**

1. Clone the repository:
```
git clone git@github.ibm.com:<organization>/<repository>.git
```

1. Create a branch for a "feature" change:
```
git checkout -b "FEATURE_NAME"
```

1. Make changes and stage them (`.` stages all changes under a directory):
```
git add .
```

1. Commit those changes to your local repository (no server connection required):
```
git commit -m "DESCRIPTION_OF_CHANGE"
```

1. Push the commit to the server:
```
git push
```

After each push (all done on [https://github.ibm.com](https://github.ibm.com)):

1. Create a Pull Request ("PR") on Github.
1. Obtain a peer review.
1. After approval, merge.

**Work on next feature**

1. Return to main branch:
```
git checkout main
```

1. Pull the latest code from the server:
```
git pull
```

1. Repeat from step 1. at the top under "basic flow of development.

**Important:** when you create a new branch always make sure you have pulled from the Github server first to ensure you are creating that branch from the latest code.

See the [z/OS Pipeline Workflow](../pipelines/workflow.md) page in this documentation for more details.

## Conflicts
Git allows any user to work on the same file at the same time and this can cause conflicts when Git tries to merge the files.  Whomever pushes their file to the server first will not encounter a conflict.

To push code changes after commit, you need to pull the latest from the repository on the server.  This is also referred to as syncing changes.  If another developer user pushed a change and there is a newer copy of your file on the server than the one you pulled and worked on, Git will merge the files.  If there is a change to the file on the same line(s) then a conflict will arise that you need to manually resolve locally, re-commit and then sync/push.

A merge conflict looks like this:
```
<<<<<<< HEAD (Current Change)
This is content from the file on the server
=======
This is content on the local machine
>>>>>>> local_branch (Incoming Change)
```

## More Resources
Git is powerful and has a lot of features but is easy to get started with using the following commands:

For more beginner information on Git, see [this tutorial](https://www.simplilearn.com/tutorials/git-tutorial/what-is-git#:~:text=What%20is%20Git%3F&text=Git%20is%20a%20DevOps%20tool,together%20on%20non%2Dlinear%20development){target=\_blank}.  The full suite of Git documentation is located [here](https://git-scm.com/doc){target=\_blank}.

Learn more Git Concepts [here](https://www.gitkraken.com/learn/git#concepts){target=\_blank}.

There's also [this video](https://www.youtube.com/watch?v=LdSwWxVzUpo){target=\_blank} from MicroSoft that shows Git and Pull Requests process in VSCode (the most popular text editor currently).

### IDE Integration
Many integrated development environments (IDE's) contain a user interface to perform all of the terminal commands **which can make your work easier**.  For detailed instructions see this page for [Eclipse](https://www.vogella.com/tutorials/EclipseGit/article.html){target=\_blank} and this page for [VSCode](https://www.digitalocean.com/community/tutorials/how-to-use-git-integration-in-visual-studio-code){target=\_blank}.