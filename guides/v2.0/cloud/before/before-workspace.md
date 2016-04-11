---
layout: default
group: cloud
subgroup: 02_before
title: Set up a Magento workspace
menu_title: Set up a Magento workspace
menu_order: 6
menu_node: 
github_link: cloud/before/before-workspace.md
---

#### Contents
*	[Set up a Magento workspace](#cloud-first-workspace)
*	[Install the CLI](#cloud-ssh-cli-cli-install)
*	[Enable Secure Shell (SSH)](#cloud-ssh-cli-ssh)

## Set up a Magento workspace {#cloud-first-workspace}
The following sections discuss your options for setting up a Magento Enterprise Cloud Edition project.

To be able to manage your projects, environments, and services, you must set up the Magento Enterprise Cloud Edition command-line interface (CLI) and Secure Shell (SSH). These tools enable you to perform tasks like:

*	Work on a local branch
*	Branch and merge in your project
*	Push changes to the parent branch
*	Pull changes from the parent branch

## Install the CLI (Command Line Interface) {#cloud-ssh-cli-cli-install}
The CLI is equivalent to the Web Interface. It will help you manage your projects on Magento Enterprise Cloud Edition.

To install the Magento Enterprise Cloud Edition CLI:

1.	Open a terminal and enter the following command:

		curl -sS https://accounts.magento.cloud/cli/installer | php
2.	After the CLI downloads, an operating system-specific command displays.

	For example, on Ubuntu and CentOS, the command is:

		source .bashrc

	If the `source .bashrc` command fails, prepend the absolute file system path to your user home directory to `.bashrc`
3.	Enter the operating system-specific command to add the CLI to your system `$PATH`.
4.	Verify the `magento-cloud list` command is in your path by entering the following command:

		magento-cloud list

## Enable Secure Shell (SSH) {#cloud-ssh-cli-ssh}
The [SSH protocol ](https://en.wikipedia.org/wiki/Secure_Shell){:target="_blank"} is designed to maintain a secure connection between two systems&mdash;in this case, your local working environment and your Magento Enterprise Cloud Edition Git project.

You must create an SSH keypair on every machine with which you and your team expect to interact with Magento Enterprise Cloud Edition.

### Locate an existing SSH keypair
An existing SSH keypair is typically located in the `.ssh` subdirectory of the user's home directory. For example, if you log in as `magento_user`, your SSH keys are typically located in:

    /home/magento_user/.ssh

If you don't have SSH keys, continue with the next section.

If you already have SSH keys, you can skip the next section and continue with any of the following:

*	[Create a sample Magento project from a template]({{ site.gdeurl }}cloud/access-acct/first-time-setup_template.html)
*	[Import an existing Magento project]({{ site.gdeurl }}cloud/access-acct/first-time-setup_import.html)

### Create a new SSH keypair
Use the `ssh-keygen` command to create an SSH keypair. `ssh-keygen` is typically installed on Linux systems. On Windows, you can use a UNIX environment like Cygwin or you can use Putty.

For more information:

*	[How To Set Up SSH Keys (Digitalocean)](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2){:target="_blank"}
*	[Manually generating your SSH key in Windows](https://docs.joyent.com/public-cloud/getting-started/ssh-keys/generating-an-ssh-key-manually/manually-generating-your-ssh-key-in-windows){:target="_blank"}
*	[ssh-keygen man page](http://linux.die.net/man/1/ssh-keygen){:target="_blank"}

The command syntax follows:

	ssh-keygen -t rsa -C "your_email_address@example.com"

Follow the prompts on your screen to complete the task.

You can add SSH keys to your account in any of the following ways:

*	Using the Magento Enterprise Cloud Edition CLI
*	Using the Magento Enterprise Cloud Edition Web Interface when you create a new project
*	Your account settings

### Add a public SSH key to your account
As the account owner, log in to your Magento Enterprise Cloud Edition account and add a public SSH key. You must add the SSH key for the machine on which you will do the initial `git push` to add code to the project.

If you don't already have SSH keys on that machine, see [GitHub documentation](https://help.github.com/articles/generating-an-ssh-key){:target="_blank"} to create them.

To add a public SSH key:

1.	Copy your SSH public key to the clipboard.
2.	Using the link in your welcome e-mail, access your Magento Enterprise Cloud Edition account.
2.	Log in to your project using Bitbucket, GitHub, Google, or a user name and password.

	![Log in to a project]({{ site.baseurl }}common/images/cloud_project-login.png){:width="500px"}
3.	In the upper right corner of the page, click the **Account Settings** tab as the following figure shows.

	![Account settings]({{ site.baseurl }}common/images/cloud_acct-settings.png){:width="650px"}
5.	Expand **SSH Keys**.
6.	On the next page, click **Add Public SSH Key** as the following figure shows.

	![Add SSH public key to your account]({{ site.baseurl }}common/images/cloud_add-public-key.png){:width="650px"}
7.	Follow the prompts on your screen to complete the task.

#### Next steps
*	[Create a sample Magento project from a template]({{ site.gdeurl }}cloud/access-acct/first-time-setup_template.html)
*	[Import an existing Magento project]({{ site.gdeurl }}cloud/access-acct/first-time-setup_import.html)

