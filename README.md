# git-annex-maildir

Boilerplate to set up a git-annex repository configured for use with maildir email directories


# What does this do?

[git-annex](https://git-annex.branchable.com/) is an interesting tool that improves git's abiity to work with large files.  It can be used to archive files that you want to preserve for future review without resulting in twice the disk space being used.  There is a lot more to it than that, so check out the web site.

I use it to archive files that do not change (e.g. movies, completed projects, ebook collection, etc.)

I also wanted to use it to archive my email in such a way that I can easily sync it between machines, keep an offsite backup, and have the ability to validate data integrity to protect against "bit rot" over time.

In order to achieve this, I:

1.	Installed dovecot on my machine (macOS)

2.	Configure dovecot to use `/path/to/email/%n` for maildir storage (explaining this is beyond the scope of this document -- I am not a dovecot expert)

3.	Used this script to create `/path/to/email` and configure it as desired

4.	Run my email client (in this case Mail.app for macOS) and connect to my local email server (again -- details of this are out of scope for this document)

5.	Create a new folder in Mail on this account (e.g. `Archive`) and copy a message or two in

6.	Verify that `path/to/email/username/.Archive/cur` now contains one file for each message copied

7.	Run `git annex add username` and `git annex add username/.Archive` to add the desired files

8.	Run `git annex sync` to commit the changes


# How does it work?

The settings are designed to:

1.	Ignore files that can be rebuilt by dovecot to save on space (e.g. index files and certain setting files)

2.	Ignore files that need to be written to, but don't need to be archived (e.g. log files)

3.	Use git annex to store files that should not be edited once written (e.g. email messages that have been stored in a folder)

4.	Use regular git for files that will need to be maintained as you modify the setup (e.g. `subscriptions` which includes a list of available folders)


# How do I use this?

I recommend the following, either via the command-line or as a shell script if you will be using it multiple times.

	#!/bin/sh

	git init new-email-folder

	cd new-email-folder

	git remote add "template" https://github.com/fletcher/git-annex-maildir.git

	git pull template master

	git annex init

	git annex config --set remote.template.annex-sync false


The new folder `new-email-folder` is configured to be the folder where user maildirs are stored.  For example, the user `user1` could have email stored in `new-email-folder/user1`.  `new-email-folder` should not directly be used for email, since `git` would then appear as an invalid email folder in your mail client.


# IMPORTANT NOTES #

This script is still being developed and tested.  I needed to publish it to github in order to verify that my workflow is correct on different machines.

Properly configuring git, git-annex, and dovecot takes some experience and/or practice.  There are plenty of documents and guides available on the internet.

Always test your backup system before assuming it works (this includes doing a trial restore as if your primary data source had been completely destroyed.)  

This is designed to be another tool in your backup toolbox -- it is not intended to be the only way that you backup important email archives.


