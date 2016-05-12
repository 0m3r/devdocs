<div markdown="1">

Before you continue, to avoid errors during your installation or update, make sure you verify *all* of the following:

*	You set up a [Magento file system owner](#magento-owner-group) and shared that owner's group with the web server user group
*	Your [cron jobs](#magento-cron) are set up and running
*	[File system permissions](#perms) are set properly

<div class="bs-callout bs-callout-warning">
    <p>Do not continue without performing these checks. Failure to do so could result in errors.</p>
</div>

### Magento file system owner and group {#magento-owner-group}
For security reasons, Magento requires two users to run properly:

*	The web server user
*	A local user on the Magento server

In addition, these users must either be a member of each other's group or the local user must be a member of the web server user's group.

To check group ownership, use the following command:

	groups <username>

Typical web server group names follow:

*	Ubuntu: `www-data` or `nginx`
*	CentOS: `apache` or `nginx`

For example, enter:

	groups magento_user

Any of the following is a valid result on CentOS:

	magento_user : apache

or

	magento_user : magento_user apache

For details, see [Create the Magento file system owner]({{ site.gdeurl }}install-gde/prereq/file-sys-perms-over.html">).

### Cron jobs are running {#magento-cron}
Magento requires three cron jobs, all running as the [Magento file system owner]({{ site.gdeurl }}install-gde/prereq/file-sys-perms-over.html).

To verify your cron jobs are set up properly, enter the following command as a user with `root` privileges:

	crontab -u <magento file system owner> -l

For example, if your Magento file system owner is named `magento_user`, enter:

	crontab -u magento_user -l

Results similar to the following should display:

	*/1 * * * * /usr/bin/php -c /etc /var/www/html/magento2/bin/magento cron:run >> /var/www/html/magento2/var/log/magento-cron.log&
	*/1 * * * * /usr/bin/php -c /etc /var/www/html/magento2/update/cron.php >> /var/www/html/magento2/var/log/updater.log&
	*/1 * * * * /usr/bin/php -c /etc /var/www/html/magento2/bin/magento setup:cron:run >> /var/www/html/magento2/var/log/setup.log&

Another symptom of cron not running is the following error in the Magento Admin:

![cron isn't running]({{ site.baseurl }}common/images/compman-cron-not-running.png){:width="500px"}

To see the error, you might need to click **System Messages** at the top of the window as follows:

![System Messages]({{ site.baseurl }}common/images/compman_sys-messages.png)

For details, see [Set up cron]({{ site.gdeurl }}install-gde/install/post-install-config.html#post-install-cron).

### File system permissions {#perms}
For security reasons, Magento requires certain permissions on the file system. Permissions are different from [*ownership*](#magento-owner-group). Ownership determines *who* can perform actions on the file system; permissions determine *what* the user can do.

Directories in the Magento file system must have 770 (read/write/execute) permissions; files must have 660 (read/write) permissions. Only the Magento file system owner and the web server user can have access to these files.

To verify your file system permissions are set properly, either log in to the Magento server or use your hosting provider's file manager application.

For example, enter the following commands on a Linux system if the Magento application is installed in `/var/www/html/magento2`:

	ls -al /var/www/html/magento2

A sample result follows:

{% highlight xml %}
total 200592
drwxrwx---. 12 magento_user apache      4096 Apr  1 15:08 .
drwxr-xr-x.  3 magento_user apache      4096 Jan 11 15:13 ..
drwxrwx---.  4 magento_user apache      4096 Apr  1 15:08 app
drwxrwx---.  2 magento_user apache      4096 Apr  1 15:08 bin
-rw-r--r--.  1 magento_user apache    438803 Apr  1 15:08 CHANGELOG.md
-rw-rw----.  1 magento_user apache      3008 Apr  1 15:00 composer.json
-rw-rw----.  1 magento_user apache    338028 Apr  1 15:08 composer.lock
-rw-r--r--.  1 magento_user apache      3425 Apr  1 15:08 CONTRIBUTING.md
-rw-r--r--.  1 magento_user apache     10011 Apr  1 15:08 CONTRIBUTOR_LICENSE_AGREEMENT.html
-rw-r--r--.  1 magento_user apache       631 Apr  1 15:08 COPYING.txt
drwxrwx---.  4 magento_user apache      4096 Apr  1 15:08 dev
-rw-r--r--.  1 magento_user apache      2926 Apr  1 15:08 Gruntfile.js
-rw-r--r--.  1 magento_user apache      7592 Apr  1 15:08 .htaccess
-rw-r--r--.  1 magento_user apache      6419 Apr  1 15:08 .htaccess.sample
-rw-r--r--.  1 magento_user apache      1358 Apr  1 15:08 index.php
drwxrwx---.  4 magento_user apache      4096 Apr  1 15:08 lib
-rw-r--r--.  1 magento_user apache     10376 Apr  1 15:08 LICENSE_AFL.txt
-rw-r--r--.  1 magento_user apache     10364 Apr  1 15:08 LICENSE.txt
-rw-r--r--.  1 magento_user apache      4108 Apr  1 15:08 nginx.conf.sample
-rw-r--r--.  1 magento_user apache      1427 Apr  1 15:08 package.json
-rw-r--r--.  1 magento_user apache      1659 Apr  1 15:08 .php_cs
-rw-r--r--.  1 magento_user apache       804 Apr  1 15:08 php.ini.sample
drwxrwx---.  2 magento_user apache      4096 Apr  1 15:08 phpserver
drwxrwx---.  6 magento_user apache      4096 Apr  1 15:08 pub
drwxrwx---.  7 magento_user apache      4096 Apr  1 15:08 setup
-rw-r--r--.  1 magento_user apache      3731 Apr  1 15:08 .travis.yml
drwxrwx---.  7 magento_user apache      4096 Jan 11 15:15 update
drwxrwx---. 11 magento_user apache      4096 Apr  1 15:21 var
drwxrwx---. 27 magento_user apache      4096 Apr  1 15:08 vendor
{% endhighlight %}

In the preceding example, the Magento file system owner is `magento_user`. Directories in the Magento file system have `drwxrwx---` permissions (770) and files have `-rw-r--r--` permissions (660).

To get more detailed information, you can optionally enter the following command:

	ls -al /var/www/html/magento2/pub

Because Magento deploys static file assets to subdirectories of `pub`, it's a good idea to verify permissions and ownership there as well.

For more information, see [File system permissions and ownership]({{ site.gdeurl }}install-gde/prereq/file-system-perms.html).