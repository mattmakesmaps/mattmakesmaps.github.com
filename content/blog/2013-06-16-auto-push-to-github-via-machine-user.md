---
title: "Auto-Push to GitHub via Machine User"
date: 2013-06-16
---

This post will review a workflow for automatically pushing data from a client machine to github.
This can be useful if you want to automatically make publicly available data that are regularly processed on
a local machine. <!-- more --> For my particular use case, I'm downloading data from the [USGS Instantaneus Values
Web Service](http://waterservices.usgs.gov/rest/IV-Test-Tool.html) , and would
like to process those data locally for use in a d3 visualization. My d3 viz will eventually be
hosted on github as well, so having a dynamically updated dataset stored on github made sense.
I'm sure that it's possible to emulate this ETL workflow in pure JavaScript, but I wanted to see
how to set this up, in the event that I found myself in a situation in which heavy pre-processing
of data was required.

The general steps are as follows:

1. On the local machine, create a machine user and group.
2. Create a corresponding github account for the machine user.
3. Generate an SSH key machine user & associate the public key of the machine user to its github account.
5. Assign the github account to the repository of interest.
6. Clone the repository of interest, making it group writable.
7. On the local machine, create a cron job under the machine user to execute the processing script.

## Create Machine User and Group

On my local machine, I start by creating a user and group for the purpose of pushing data to github.
I'm doing this to isolate the amount of exposure that this user has to my wider system.

```bash
# Create the user, rdi-git
$ sudo adduser rdi-git
# You'll be prompted to enter a password as well.
# Create the group, git
$ sudo addgroup git
# Add our new user (rdi-git) to the new group (git)
$ sudo usermod -a -G git rdi-git
```

## Create a Github Account for Machine User

After having created the machine users on my local box, the next step is to create an analogous
user on [github](https://github.com).

## Generate SSH Key & Assign to Github User

From the local machine, make sure that you're logged in as the newly created machine user.

```bash
$ sudo su rdi-git
```

At this point, I defer to the great instructions on the Github help docs for [generating an ssh key](https://help.github.com/articles/generating-ssh-keys).

## Assign Github Account As Collaborator to Repo of Interest

Next, I add our github user, rdi-git, as a collaborator on my repository of interest. Here is a link on
the [Github help docs](https://help.github.com/articles/how-do-i-add-a-collaborator).

## Clone the Repository of Interest, add Assign Permissions

I'm creating a `/projects/` folder, in which I'll clone my repo. I'd like to ensure that this folder, and
subsequently all repos contained within it, are associated with git group. This [stackoverflow](http://unix.stackexchange.com/questions/12842/make-all-new-files-in-a-directory-accessible-to-a-group) outlines the required commands.

```bash
# Make the directory
$ cd /
$ mkdir projects
$ cd /projects
# Change perms to group writable. See Above SO post.
$ umask 002            # allow group write; everyone must do this
$ chgrp git .          # set directory group to gitG
$ chmod g+s .          # files created in directory will be in group git
# Clone our repo of interest, using ssh
$ git@github.com:mattmakesmaps/robo-d3.git 
```

## Create the Cron Job

The repo I've just cloned contains the [processing script](https://github.com/mattmakesmaps/robo-d3/blob/master/scripts/waterservices_parser.py) I'll be running.
Our processed data will also be stored and pushed back to this repository.

I'd like to have my processing script run once every hour, on the hour. We setup the cron job by first entering
crontab.

```bash
# Login as machine user, cron job will be run under this id.
$ sudo su rdi-git
$ crontab -e
```

Here is an excerpt of the machine user's cron jobs, with the new job added at the bottom.

```bash
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
0 * * * * python /projects/robo-d3/scripts/waterservices_parser.py > ~/robo-d3.log 2>&1
```

We essential say, on the minute zero, of every hour, for every day, month, and weekday, run the command 'python /path/to/script'. The tail indicates that we're outputting both STDOUT and the ERROR log to `/home/rdi-git/robo-d3.log`

And there we go. If we check out the [commit logs](https://github.com/mattmakesmaps/robo-d3/commits/master) we can
see that our github user, rdi-git, has been pushing every hour on the hour. Woo-hoo!
