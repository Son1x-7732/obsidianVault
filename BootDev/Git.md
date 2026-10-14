# Git from BootDev

```
Command Syntax
Throughout this course, you'll see commands written like this:

command <required> [optional]

Arguments in angle brackets <> are mandatory and must be provided when running the command.
Arguments in square brackets [] are optional and can be included if needed.
For example, to create a new directory in your terminal, you would run:

mkdir <directory-name>

mkdir is the command
<directory-name> is a required argument
```

```
Porcelain and Plumbing
In Git, commands are divided into high-level ("porcelain") commands and low-level ("plumbing") commands. The porcelain commands are the ones that you will use most often as a developer to interact with your code. Some porcelain commands are:

git status
git add
git commit
git push
git pull
git log
Don't worry about what they do yet, we'll cover them in detail soon. Some examples of plumbing commands are:

git apply
git commit-tree
git hash-object
We'll focus on the high-level commands because that's what you use 99% of the time as a developer, but we'll dip down into the low-level commands occasionally to really understand how Git works.
```

```
Quick Config
We need to configure Git to contain your information. Whenever code changes, Git tracks who made the change. To ensure you get proper credit (or more likely, blame) for all the code you write, you need to set your name and email.

Git comes with a configuration both at the global and the repo (project) level. Most of the time, you'll just use the global config.

Assignment
Let's set your identity. Check if your user.name and user.email are already set using config get:

git config get user.name

git config get user.email

If they aren't, set them using config set. I recommend using your GitHub username and email.

git config set --global user.name "github_username_here"
git config set --global user.email "email@example.com"

Finally, let's set a default branch (we'll talk more about configs and branches later) so that we're all on the same page. Run:

git config set --global init.defaultBranch master

We're using master for now because it is Git's default, but later we'll change it to main, which is GitHub's default. Just bear with us for a second.

Make Sure It Worked
Your ~/.gitconfig file is the file that stores your global Git configuration. View it:

cat ~/.gitconfig

Run and submit the CLI tests.
```

```
Config
The very first step of any project is to create a repository. A Git "repository" (or "repo") represents a single project. You'll typically have one repository for each project you work on.

A repo is essentially just a directory that contains a project (other directories and files). The only difference is that it also contains a hidden .git directory. That hidden directory is where Git stores all of its internal tracking and versioning information for the project.

WebFlyx
In this course, we'll be managing content for "WebFlyx", an imaginary self-hosted video streaming application. WebFlyx serves content directly from a Git repo!

Assignment
Navigate to somewhere on your filesystem where you'd like to store your project. Once you're there, create a new directory called webflyx and cd into it.

mkdir webflyx
cd webflyx

Once inside, create a new Git repository:

git init

You should now have a hidden .git directory in your webflyx directory. This means you've successfully created a new Git repository! List (ls -a) the contents of the directory to confirm.

Run and submit the CLI tests from the root of the webflyx directory.
```

```
Status
A file can be in one of several states in a Git repository. Here are a few important ones:

untracked: Not being tracked by Git
staged: Marked for inclusion in the next commit
committed: Saved to the repository's history
The git status command shows you the current state of your repo. It will tell you which files are untracked, staged, and committed.

Assignment
Every good content management system needs a table of contents.

Create a file in the root of your webflyx directory called contents.md and add the following text to the file:
# contents

The .md extension means it's a markdown file, which is popular for writing docs.

Save the file, then run:
git status

Run and submit the CLI tests from within the webflyx directory.

Troubleshooting
This course expects Git's output to be in English. If it is not, you can temporarily set your terminal to English:

export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
export LANGUAGE=en

You might need to install the language pack for your system, e.g. sudo apt-get install language-pack-en for Ubuntu.
```

```
Staging
The contents.md file has been created, but as we saw, it's untracked. We need to stage it (add it to the "index") with the git add command before committing it later.

Without staging, no files are included in the commit – only the files you explicitly git add will be committed.

Here's the command:

git add <path-to-file OR pattern>

For example:

git add i-use-arch.btw

Assignment
Add contents.md to the staging area.
Run git status again.

```

```
Commit
After staging a file, we can commit it.

A commit is a snapshot of the repository at a given point in time. It's a way to save the state of the repository, and it's how Git keeps track of changes to the project. A commit comes with a message that describes the changes made in the commit.

Here's how to commit all of your staged files:

git commit -m "your message here"

Assignment
Commit the contents.md file with the message A: add contents.md.
You wouldn't normally start a commit message with A:, you'd just write the message, but we're going to use letters to help us easily identify commits in this course.

Run git status again, and you should see that the file is no longer staged.
Run and submit the CLI tests from inside the webflyx directory.
Tip
If you screw up a commit message, you can change it with the --amend flag. For example:

# Change the last commit message
git commit --amend -m "A: add contents.md"
```


