# Introduction to Git and GitHub

## What is Git?

Git is a distributed version control system that helps track changes in your code over time. It allows you to:

- Save different versions of your project
- Collaborate with others
- Revert to previous versions if needed

## What is GitHub?

GitHub is a web-based platform that uses Git for version control. It provides:

- A place to store your code online
- Tools for collaboration
- Features like issue tracking and project management

## Basic Git and GitHub Operations

### Initial config

The `git config` command is used initially to configure the user.name and user.email. This specifies what email id and username will be used from a local repository.

`git config --global user.name “any user name”`

### Creating a Repository

A repository (or "repo") is like a project folder that contains all your files and their history.

To create a repo on GitHub:
1. Log in to GitHub
2. Click the "+" icon in the top right
3. Select "New repository"
4. Give your repo a name and optional description
5. Choose public or private
6. Click "Create repository"

### Cloning a Repository

Cloning creates a local copy of a GitHub repository on your computer.

To clone a repo:
1. On GitHub, go to the repo's main page
2. Click the green "Code" button
3. Copy the URL
4. Open your terminal or command prompt
5. Type: `git clone [URL you copied]`

### Making Changes

After making changes to your files locally:

1. Open your terminal
2. Navigate to your project folder
3. Type `git status` to see which files have changed
4. Use `git add [filename]` to stage specific files, or `git add .` to stage all changes
5. Commit your changes with a message: `git commit -m "Your message here"`

### Pushing Changes

To upload your local changes to the main branch on GitHub: `git push origin main`

### Pulling Changes

To get the latest changes from GitHub to your local machine: `git pull origin main`

### Creating Branches

Branches allow you to work on different versions of your project simultaneously.

1. To create a new branch: `git branch [branch-name]`

2. Switch to the new branch: `git checkout -b [new-branch-name]`

3. Switch to an existing branch: `git checkout [branch-name]`

4. List all remote or local branches: `git branch -a`

5. Delete a local branch: `git branch -d [branch-name]`

### Check current status
To get the current status of the repository: `git status`. The command provides the current working branch. If the files are in the staging area, but not committed, it will be shown by the git status. Also, if there are no changes, it will show the message no changes to commit, working directory clean.


## Best Practices
1. Create a README.md file for every repository to explain your project, its purpose, and how to use it.
2. Always create separate branches whenever collaborating with others on the same project and merge them with main after each update.
3. Always pull the latest changes from the remote repository before pushing your own work to avoid conflicts.

## Helpful links
1. https://www.simplilearn.com/tutorials/git-tutorial
2. https://docs.github.com/en/get-started/start-your-journey/hello-world
