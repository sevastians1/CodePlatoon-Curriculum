# Mac Installfest

## What we'll cover
We are going to install everything that you will need for this course. Please do this in order!

1. XCode
1. Visual Studio Code
1. Homebrew
1. Python
1. Node
1. git

## Install XCode Commands

Run the following command in the terminal.

```sh
$ xcode-select --install
```

This installs the Command Line Tools package via the Terminal application. This is to ensure you have it installed correctly and in the correct path.

## Visual Studio Code
- Download [Visual Studio Code](https://code.visualstudio.com/download). If you're on an M1 Mac, click on `Apple Silicon`.
- Complete the `Launching from Command Line` instructions [here](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line). This is a command that allows you to open a file or directory in your VSCode text editor from the command line by running `code SOME_FILENAME`


## Homebrew

Homebrew is a package manager for MacOS. Let's check if we've installed homebrew correctly.

```sh
$ which brew
/usr/local/bin/brew
```

If you don't see this, run:

```sh
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Homebrew is going to do a lot of work for us in that it manages our software versions, so always ensure that it's up-to-date and that it's healthy.

```sh
$ brew update
```

and

```sh
$ brew doctor
```

`brew doctor` may tell you a lot of stuff… you'll want to read through each item, and attempt to resolve the issue for Homebrew. Warnings are good to read but are not mandatory to fix. We're ideally shooting for a message like this:

```sh
$ brew doctor
Your system is ready to brew.
```


## Python
Python comes built in with Mac, but it's an old version. You could download it directly from the Python [website](https://www.python.org/downloads/), but we are developers now! Let's download it using Homebrew so that one single package manager handles everything:

```sh
$ brew install python
```

Please note that it will take a while to install the most recent version of Python on your computer. Once it's finished, type in `type python` at your Command Line. Despite installing the most recent version of Python on your machine, your Terminal still likes using the old version. The new version can be accessed by typing `python3`. Since we will only be using python3, you might want to set up an alias in your terminal, so that when you type `python`, your terminal uses python3 instead of python2. Remember that the `.bash_profile` is only read when you start your terminal, so your changes won't take effect until you create a new terminal tab, or run the `source` command. 

```sh
alias python=python3
# Paste the code above
$ source ~/.bash_profile
```

Try typing `python` at your Terminal again and see that the version changed from Python 2 to 3!

## Node Version Manager
Node does not come built in to Mac, so we need to install it. First, we'll install `npm`, the 'Node package manager'.  

```sh
$ brew install npm
```

Since you might need different versions of Node for different projects, we won't be installing node directly with `npm`. Instead, we'll first install `n` (very descriptive name), which we can use to install a specific version of Node. We'll install `n` globally by using the `-g` flag, because `n` is used at the command-line for different node projects, not for individual projects. You might need administrator privileges to install node modules globally, so we put `sudo` ( (S)uper (U)ser (DO) ) at the start of the command to run it as an administrator. When you run commands with `sudo`, you'll be asked to type your user account password at the commandline. You won't see your password (or even `****`) as you type it, so make sure you type it carefully. 

```sh
$ sudo npm install -g n 
```


Download the latest stable (production-ready, non-experimental) version of node.

```sh
$ sudo n stable
```

## Git Configuration
Next, you'll want to get your gitconfig setup to recognize your github credentials:
1. Set up your command line Git [username](https://help.github.com/articles/setting-your-username-in-git/)
2. Set up your command line Git user [email](https://docs.github.com/en/github/setting-up-and-managing-your-github-user-account/managing-email-preferences/setting-your-commit-email-address#setting-your-commit-email-address-in-git)
3. Cache your Github [password](https://help.github.com/articles/caching-your-github-password-in-git/) so that you don't have to type it in every single time you push to Github

Finally, ensure that VSCode is your global text editor for all things Git:

```sh
$ git config --global core.editor "code -n -w"
```

Confirm `gitconfig` is set up, both for your username/email and gitignore by running `git config --global -l`
You should see that your username, email, gitignore, and editor are all listed.

