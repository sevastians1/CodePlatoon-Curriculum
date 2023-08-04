# Installfest

## Topics Covered / Goals
- Get your local machine set up to start the course
- Exploring your IDE (VSCode)
- Be able to navigate your computer using the command line
- Create and manage git repositories using the command line

## Lesson

### Computer Setup (Mac, Linux, & Windows)
Before we get started, just know that this will be chaos. Your goal is to get a working environment. Please follow along closely!
- [Slack](https://slack.com/downloads) - for all communication purposes
- [Zoom](https://zoom.us/support/download)
- Sign up for [Operation Code](https://operationcode.org/join)

### Mac Setup
- Make sure you have an Apple ID
- Download XCode from the Mac App Store
- Ensure that you're running the most recent operating system. You can check your version by going to "System Preferences" and clicking on "Software Update"
- [Complete Installfest (MacOS)](../../page-resources/installfest_mac.md)

### Ubuntu Linux Setup
- [Complete Installfest (Ubuntu Linux)](../../page-resources/installfest_ubuntu.md)

### Windows Setup
- Ensure that you're running the most recent operating system (Windows 10 and 11 are both fine). You can update to the latest Windows version by selecting `Start > Settings > **Windows Update **> Check for updates.`
- [Complete Installfest (Windows)](../../page-resources/installfest_windows.md)

### CLI (Command Line Interface) Intro

Review the following BASH commands:
- pwd
- ls, ls -a
- cd, ., .., ~, / (different places to cd to)
- mkdir (make ~/projects for all codeplatoon assignments)
- touch
- cp, cp -r
- mv
- rm, rm -r, rm -rf
- rmdir
- sudo
- clear, cmd+k
- less (useful by itself, also used by git diff)
  - `<spacebar>` moves down a page
  - `b` moves up a page
  - `q` quits
- vim (just learn how to quit. used by git commit)
  - press `<esc>` to enter "normal mode"
  - from normal mode, press `:` to enter "ex mode"
  - from ex mode, type `wq` to save and quit, or `q!` to quit without saving
  

### Git Intro

- `ls -a` (nothing here)
- `git status` (not a repo)
- `git init` 
- `ls -a` (see .git folder)
- `git status`
- `touch work.txt`
- `git add .`
- `git commit -m 'i did work'` (use -m to add the message inline, or you'll find yourself in vim)
- `git log` (uses `less` to show the commit history)
- `git remote add origin https://github.com/username/reponame` (the name is arbitrary, but origin is VERY conventional)
- `git remote rm origin` (in case you messed up)
- `git remote -v` (maybe you messed up, but aren't sure)
- `git push origin master`
- `git pull origin master`
- `git clone https://github.com/username/reponame` (now you can start today's exercises)

## Visual Studio Code

There are many IDEs (integrated development environments) out there that developers can use. For our class, we're going to be using Visual Studio Code, a free IDE created by Microsoft. By default, VSCode is a powerful, flexible editor that supports many different coding languages. However, VSCode is also highly extensible, with a rich ecosystem of plugins. Here are a few plugins that might make it easier for you to build applications with VSCode. 
- Beautify
- VSCode Icons
- Bracket Pair Colorizer
- Path IntelliSense
- Live Server
- Python
- Live Share
- Live Share Audio
  
 


## Assignments
- [99 Bottles](https://github.com/sierraplatoon/algo-99-bottles) in JS
- [Deaf Grandma](https://github.com/sierraplatoon/algo-deaf-grandma) in JS
- [Terminal Commands In Depth](https://github.com/sierraplatoon/misc-command-line) ...nothing to submit here but necessary reading
- [Incoming Student Survey](https://docs.google.com/forms/d/e/1FAIpQLSePRzeiSCe1hqzud7btr--Xxm791zqCe_RqA_9mGxEIUN_IwQ/viewform)

