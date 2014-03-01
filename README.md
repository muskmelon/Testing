# Environment setup

ex: https://gist.github.com/mmalawski/3848514

* read in order
  * `.bashrc`
  * `.bash_profile`
  * `.profile`
  * `/etc/profile`
  * `/etc/bash.bashrc `
  * and others

When bash is invoked as an interactive login shell, or as a non-interactive shell with the --login option
it first reads and executes commands from the file 

* `/etc/profile`

After reading that file, in order it looks for 

* `~/.bash_profile`
* `~/.bash_login`
* `~/.profile`

and reads and executes commands from the first one that exists and is readable. 
The --noprofile option may be used when the shell is started to inhibit this behaviour.

When an interactive shell that is not a login shell is started, 
bash reads and executes commands from 

* `/etc/bash.bashrc`
* `~/.bashrc`
 
This may be inhibited by using the --norc option. 
The --rcfile file option will force bash to read and execute commands from file instead of /etc/bash.bashrc and ~/.bashrc.

* check all the $PATH using `echo $PATH`
* usually a regular $PATH should look like this: (NOTE it should be 1 line, I add the break so is easier to read)
  ```
  /usr/local/sbin:
  /usr/local/bin:
  /usr/sbin:
  /usr/bin:
  /sbin:
  /bin
  ```
* if it's the $PATH under rvm may look like:
  ```
  /usr/local/rvm/gems/ruby-2.0.0-p247/bin:
  /usr/local/rvm/gems/ruby-2.0.0-p247@global/bin:
  /usr/local/rvm/rubies/ruby-2.0.0-p247/bin:
  /usr/local/rvm/bin:
  /usr/local/sbin:
  /usr/local/bin:
  /usr/sbin:
  /usr/bin:
  /sbin:
  /bin
  ```
* modify the $PATH: `export PATH=/usr/local/rvm/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`


----
James: I let ~/.bashrc to also look for ~/.bash_prompt
and it will do some customization to display more info in my terminal
* need to refresh it if changes made
$ `~/.bashrc`

# OSX

* show desktop
  * fn + F11
* add color when ls in terminal (this only work for MacOS, FreeBSD based system)
  * `mate ~/.bash_profile`
  * and add following line:
    ```
    export CLICOLOR=1
    export LSCOLORS=ExFxCxDxBxegedabagacad
    ```
  * REF:http://apple.stackexchange.com/questions/33677/how-can-i-configure-mac-terminal-to-have-color-ls-output

# RVM
  
Commands
--------

  * list ruby version
	  * $ `rvm list`
	
	* list gemset
	  * $ `rvm gemset list`
  
  * install ruby
    * sudo -i
    * rvm install <ruby-version>
    * check if the ruby version is still alive
      REF: https://bugs.ruby-lang.org/projects/ruby/wiki/ReleaseEngineering
  
GCC problem
-----------

Xcode 4.2+ (My was Xcode 4.5) does not have proper GCC for ruby, 
they call it llvm-gcc but obviously ruby 1.9.3 or under can not be compiled with llvm-gcc. 
To get proper gcc, based on $ rvm requirements it is suggested to get it through Homebrew 
(otherwise i need to uninstall xcode and its command line tools... 
...cannot find any info on how to properly uninstall both things, so gave up)

REF: https://github.com/wayneeseguin/rvm/issues/1054

I was following this post basically for the homebrew installation
REF: http://www.moncefbelyamani.com/how-to-install-xcode-homebrew-git-rvm-ruby-on-mac/

(after I install i run $ brew doctor but brew warn me about old xcode installed and macport installed - but i don't - then I realize it was the migration file process when I start the new moutain lion screw things up, just remove the ~/opt and ~/Developer and warnings was gone)

Once installed, do the following as suggested by RVM:

$ brew update
$ brew tap homebrew/dupes # if got http error, its fine, do it again and it will work
$ brew install autoconf automake apple-gcc42
$ rvm pkg install openssl

Then, after that I tried to install ruby through rvm

$ rvm install 1.9.3

Works fine, just rvm force you to read info on its requirements again, just need to quit it and it will continue its installation, and it works finally. 


Install RUBY 2.0
----------------

  https://coderwall.com/p/tptocq

$PATH messed up
---------------

try `vmsudo rvm get stable` to fix it


# RUBY BUNDLER

REF: http://bundler.io/v1.5/man/bundle-install.1.html

* install gems in local vendor files
  * `bundle install --path vendor/bundle`
  * than when run ruby MUST add the following on top (setup bundler environment)
  ```ruby
  require 'rubygems' 
  require 'bundler/setup'
  ```

# SSL
* REF: https://devcenter.heroku.com/articles/ssl-certificate-self

# THIN
* `gem install thin`
  REF: http://code.macournoyer.com/thin/

# NGINX

* REF: http://shapeshed.com/journal/building-a-rails-development-server/
* REF: http://articles.slicehost.com/2008/5/13/ubuntu-hardy-installing-nginx-from-source
* REF: http://articles.slicehost.com/2008/5/27/ubuntu-hardy-nginx-rails-and-thin

* start nginx $ `sudo /etc/init.d/nginx start`
* stop nginx $ `sudo /etc/init.d/nginx stop`
* nginx config
  * installed from apt-get (most of time):
    `cd /etc/nginx/sites-available/`
  * installed from source:
    `cd /usr/local/nginx/sites-available/`
* default root:
  `/usr/share/nginx/www`
* enable site:
  * installed from apt-get (most of time):
    * `sudo ln -s /etc/nginx/sites-available/<my-site-config> /etc/nginx/sites-enabled/<my-site-config>`
    * make sure default is disabled (remove the link) to avoid port 80 conflict


# APACHE

OSX 10.8 AND 10.9 has Apache 2.2.22 installed, but need to manually enable it

Commands
--------

	# start it
	$ sudo apachectl start
	
	# stop it
	$ sudo apachectl stop
	
	# restart it
	$ sudo apachectl restart
	
	# check version
	$ httpd -v

User Level Root & Rewrite
-------------------------
	
* create Sites folder back to ~/
* create yuanhsueh.conf in /etc/apache2/users/

	# to enable symbol link works, make sure Options has FollowSymLinks or Options is All
	<Directory "/Users/yuanhsueh/Sites/">
	Options Indexes MultiViews FollowSymLinks
	AllowOverride All
	Order allow,deny
	Allow from all
	</Directory>
	
* change permission of
	$ sudo chmod 644 /etc/apache2/users/yuanhsueh.conf

* # To debug mod_rewrite you can enable rewrite logging
  RewriteLogLevel 3
  RewriteLog /Users/yuanhsueh/Desktop/rewrite.log
  
REF: http://coolestguyplanettech.com/downtown/install-and-configure-apache-mysql-php-and-phpmyadmin-osx-108-mountain-lion

Auto Launch on Startup
----------------------

* The LaunchDaemon for Apache is still exist in OSX 10.8, just need to load it
	
	# The -w makes it permanent, i.e. it'll be reloaded when you reboot.
	$ sudo launchctl load -w /System/Library/LaunchDaemons/org.apache.httpd.plist

* check which process is listening on port 80 
	
	$ sudo lsof -i:80

REF: http://superuser.com/questions/455505/how-do-i-start-apache-in-osx-mountain-lion


File Permission
---------------

REF: http://codex.wordpress.org/Changing_File_Permissions

* Especially when using Wordpress
  
  check apache user of a local machine; in Leopard, Apache is already installed, and the httpd.conf file is
  stored under /etc/apache2; check the file and search for 'User' in the config file.
  in my case, my User is www and Group pis www as well,

  It is happening quite often that when you try to do an update on plugin but Wordpress show up a FTP conection form instead, this often means that you don't have the 'write' permission for the directory which Wordpress try to access in. This results that every time when doing an upload or update or installation, you may need to grant permission to certain directories recursively OR change ownership recursively WHILE remembering to change everything back to prevent security leak. And obviously, doing this process repeating will get cumbersome. 

  why this happens?
  REF: http://www.chrisabernethy.com/why-wordpress-asks-connection-info/
  REF: http://wordpress.org/support/topic/ftp-information-for-localhost?replies=4

* Basic knowledge

  user  group   everyone else
  rwx   rwx     rwx           = 421 421 421 = 777
  
  REF: http://codex.wordpress.org/Changing_File_Permissions
  REF: http://wiki.mediatemple.net/w/File_Permissions

* What I Did
  
  **this works on leopard (10.5.8)
  **however, just change the ownership won't work on OS 10.8, follow the other option instead
  
  # on leopard, every time I want to do an update, I change the ownership of the whole project to apache's user
      $ sudo chown www wp_project/ -R

  # and change it back again, works fine so far
      $ sudo chown yuanhsueh wp_project/ -R
  
  OR 
  
  **this works on mountain lion (10.8.2) && snow leopard (10.6.8)
  **always check apache user/group name from $ mate /etc/apache2/httpd.conf
  **could be www or _www
  
  # if apache user is _www, must add ':' in front
      $ sudo chown -R :_www wp_project

  # Give write permission to the group
      $ sudo chmod -R g+w wp_project 
  
  # Add to "wp-config.php"
  define('FS_METHOD', 'direct');

  REF: https://gist.github.com/3127778
  
  OR
  
  MAMP solution
  
  http://soderlind.no/archives/2011/08/26/running-wordpress-locally-on-mac-os-x-lion/
  http://www.mamp.info/en/index.html


Link to OSX Site Folder 
-----------------------
  
  # We don't want to save source project in Site, so we should use sym link point apache to the project
  $ ln -s <source> <destination>


Redirect
--------
REF: https://kb.mediatemple.net/questions/242/How+do+I+redirect+my+site+using+a+.htaccess+file%3F

* using mod_rewrite in MediaTemple
REF: http://wordpress.org/support/topic/media-temple-and-mod_rewrite
REF: https://kb.mediatemple.net/questions/85/Using+.htaccess+rewrite+rules#gs/-httpexamplecomfolder1-to-httpexamplecomfolder2--
REF: http://stackoverflow.com/questions/14492735/how-can-i-redirect-old-pages-with-question-marks-in-the-url

# PHP

* OSX 10.8 has PHP 5.3.13 installed, but need to manually enable it
* Load Module in Apache
  * $ `sudo nano /etc/apache2/httpd.conf`
    * Use "control" + "w" to search and search for 'php' this will land you on the right line then uncomment the line (remove the #):
	    ```
	    LoadModule php5_module libexec/apache2/libphp5.so
	    ```
	  * 'control o' and 'control x' to save and leave
  * and once its done, need to restart apache
	  $ sudo apachectl restart

* php info
  * $ `cd /var/www/` for linux or `cd ~/Sites` for Mac
  * $ `vi phpinfo.php` to create a php file
  * add `<?php phpinfo(); ?>` and save
  * due to security reason, do not leave the file there after

* find 'php.init' 
  $ `sudo find / -name 'php.ini'`

* change php memory 
  * change directly from php.ini (require restart apache)
    * $ `vi /etc/php5/apache2/php.ini`
    * find the line:
      ```
      memory_limit = 32M      ; Maximum amount of memory a script may consume (32MB)
      ```  
  * overwrite the setting in .htaccess (NOT require restart apache)
  * $ `sudo vi /var/www/.htaccess` and add the following line
    ```
    php_value memory_limit 32M
    ```

* cacheing using APC
  * REF: http://artur.ejsmont.org/blog/content/how-enable-apc-extension-under-command-line-php-interface-debian-and-ubuntu
  * Make sure the module is enabled in php.ini for command line interface. 
    On debian like systems check if installer did not add anything to `/etc/php5/cli/conf.d` or `/etc/php5/cli/php.ini`

      
# MYSQL

* install mysql
  * $ `sudo apt-get install mysql-server`
    REF: https://help.ubuntu.com/12.04/serverguide/mysql.html
  * $ `sudo apt-get install libmysql-ruby libmysqlclient-dev`
    without doing this step you will NOT able to instal mysql2 gem
    REF: http://stackoverflow.com/questions/3608287/error-installing-mysql2-failed-to-build-gem-native-extension

* set root password (first time)
  $ `mysqladmin -u root password NEWPASSWORD`
  
* change root password
  $ `mysqladmin -u root -p'oldpassword' password newpass`
  REF: http://www.cyberciti.biz/faq/mysql-change-root-password/
  * usually, I do NOT have root password for the local machine - easier for development

* type `mysql` to get into shell
  * if installed from `apt-get`, you don't need to do the following
  * add the mysql directory to your shell path
    * $ `cd ; nano .bash_profile`
    * add the following line
  	  ```
  	  export PATH="/usr/local/mysql/bin:$PATH"
      ```
    * once done, you can just type $ `mysql` to get into shell

* backup schema
  * $ `mysqldump -h localhost -u root -p'<password>' silvercore_spree_prod > ~/silvercore_spree_prod.sql`
  * make sure there's NO space between -p and password and a quotation outside the password

* Backup certain tables in the schema
  * $ `mysqldump --add-drop-table -h localhost -u root -p'<password>' silvercore_spree_prod (tablename tablename tablename) > path/to/silvercore_spree_prod_tables.sql`
  * make sure there's NO space between -p and password and a quotation outside the password
  
* Restore schema
  * $ `mysql -h localhost -u root -p'<password>' silvercore_spree_prod < silvercore_spree_prod.sql`
  * make sure there's NO space between -p and password and a quotation outside the password

* check socket file location
  * $ `mysqladmin variables | grep socket`

* start / restart
  * `sudo /etc/init.d/mysql start`
  * `sudo /etc/init.d/mysql restart`
  REF: http://askubuntu.com/questions/82374/how-do-i-start-stop-mysql-server
  
Install
-------

  OSX 10.8 is missing MySQL and need to be download and install manually

  * go to http://dev.mysql.com/downloads/mysql/
  * choose version: Mac OS X ver. 10.6 (x86, 64-bit), DMG Archive
  * no need to sign up, click new user and find No thanks, just take me to the downloads
  * right click open the following files and install them seperately 
		
  	mysql5.5.xxx.pkg # the MySQL software
  	MySQLstartupitem.pkg # allows MySQL to start when the Mac is booted
  	MySQLPrefPane # preference GUI

  * you can start the MySQL server from preference GUI or command line

  	$ sudo /usr/local/mysql/support-files/mysql.server start 
	
  * check version
	
  	$ /usr/local/mysql/bin/mysql -v
	


# XCODE

it will provide you a proper development environment. Basically, it comes with list of libraries that help developer make app for OSX or iOS. However, i'm only interested in Command Line Tools (which has gcc, git...)

REF: http://www.moncefbelyamani.com/how-to-install-xcode-homebrew-git-rvm-ruby-on-mac/

# GIT

OSX 10.8 does not come with git, tho you will have it after install XCODE

Config
------

need to add some colour flag and alias to make git work better
$ mate ~/.gitconfig

	[color]
		diff = auto
		status = auto
		branch = auto
	[user]
		name = James Hsueh
		email = jamesh@nodally.com
	[alias]
		st = status
		ci = commit
		restore = checkout
		pu = push origin master:refs/heads/master
    [merge]
        tool = opendiff
    [mergetool "opendiff"]
        cmd = \"/usr/bin/opendiff\" --merge --result=\"$MERGED\" \"$LOCAL\" \"$BASE\" \"$REMOTE\"
        trustExitCode = true
        keepBackup = false
	
Commands
--------

  * to commit only the staged modifications
	  * $ `git commit -a -m`
	
	* show differences between all versions in TextMate
	  * $ `git diff | mate`

	* reset to a previous version
	  * $ `git reset --hard [commit id]`
	
	* create a branch and move to the branch (also after clone from a remote, you want to get the branch that exists on the remote)
	  * $ `git checkout -b [name of the branch]`
	
	* Adding Local Changes to Non-Current Branch
	  * $ `git checkout -m other-branch`

	* undo command for git init ?
	  * $ `rm -rf .git`
	
	* check avaliable remote repo
	  * $ `git remote` OR $ `git remote -v`
	
	* adding remote (usually we use 'origin' to point it to github)
	  * $ `git remote add origin <github-ssh-git-address*>`
	  * $ `git remote add origin ssh://something@something.com:bla/blabla.git`
	  * make sure it is ssh, not http, otherwise it will always ask for password
	
	* push to one remote repo (<origin> is repo name)
	  * $ `git push -u origin`
	
	* pull from repo to a branch
	  * $ `git pull origin master`
	  * 'master' is name of the branch
	
	* remove file from git WITHOUT actually delete it (and yes put the filename to .gitignore to ignore it forever, and may need to manually transfer the file to another repo after deleted from remote repo)
	  * REF: http://help.github.com/ignore-files/
	  * $ `git rm --cached filename`
	
	* remove all the untracked files (this will literally remove the file, do it cautiously)
	  * $ `git clean -f -d`
	
	* tags
	  * James: you MUST push the tag seperately, doing push on a branch does NOT push the tags
	  * REF: http://learn.github.com/p/tagging.html
	  * $ `git tag -a v1.4 -m 'version 1.4'`
    * $ `git push --tags`
    * $ `git pull --tags`
	
	* commit all changes on tracked files, but doesn't add new files
    * $ `git commit -am "message...."`
  
  * clone a repo
    * $ `git clone git@github.com:Nodally/xenograte-osk.git`
    * !! clone only copy the master, to also get the branch(es), MUST do a checkout afterward
    * first checkout what branches are available
    * $ `git branch -r`
    * then get it by checking out that branch
    * $ `git checkout <branch-name>`
	
	* hide the changes before merge
	  * $ `git stash`
	
	* apply the hidden changes to current repo
	  * $ `git stash pop`
	
	* adding all changes to staged for commit (including all the deleted files)
	  * $ `git add -u .`
	  * ** you MUST add the dot in the end, else only do `git add -u` will stage file on ALL THE WORKING TREE
	  * REF: http://stackoverflow.com/questions/1402776/how-do-i-commit-all-deleted-files-in-git
	
	-------

	* set up git AND add pub key: 
	  * REF: http://help.github.com/mac-set-up-git/
	
	* find download_count in github?
	  * http://stackoverflow.com/questions/17971193/track-number-of-download-of-a-release-binaries-on-github/19486509#19486509
	

# GITFLOW

* install
  * $ `brew install git-flow`
  * REF: https://github.com/nvie/gitflow/wiki/Installation

# SSH KEY

* Generate Key
  * REF: https://help.github.com/articles/generating-ssh-keys
  * $ `cd ~/.ssh`
    * if the folder NOT exist, do the following
      * $ `mkdir ~/.ssh`
      * $ `chmod 700 ~/.ssh` (important!)
      * REF: http://stackoverflow.com/questions/15190391/ssh-directory-not-being-created
  * $ `ssh-keygen -t rsa -C "your_email@youremail.com"`
    * just keep press enter, NO need for file or passphrase

* get pub key
  * $ `tail ~/.ssh/id_rsa.pub`
  
* Host alias
  * $ `mate /etc/hosts`
	  * list of pairs of ip address and name
	    ex: 174.143.245.56 nodal0
	
* ssh config
  * $ `touch ~/.ssh/config` # create config file
	* $ `mate ~/.ssh/config`
  * REF: http://kb.mediatemple.net/questions/1625/Using+an+SSH+Config+File
  * REF: http://linux.die.net/man/5/ssh_config

* change default port
  * default port is 22
  * https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04

ssh without password
--------------------

  # IF your public key is not in the server-side's authorized_keys, every time you ssh into the server, 
  # it will still ask for the password. To avoid that, after first time get into a server through ssh 
  # (when it tell you it will add RSA key fingerprint into your know_host and you usually type 'yes'),
  # then do the following:
  
  * (loacl)$ `vi ~/.ssh/id_rsa.pub` to copy your public key 
  * (remote)$ `vi ~/.ssh/authorized_keys` to save the public key to the server's ssh authorized_keys
    * if authorized_keys does NOT exist
    * (remote)$ `touch ~/.ssh/authorized_keys`
    * (remote)$ `chmod 600 ~/.ssh/authorized_keys` (important!)
    * REF: http://sshkeychain.sourceforge.net/mirrors/SSH-with-Keys-HOWTO/SSH-with-Keys-HOWTO-4.html
  * (remote)$ `sudo reload ssh` to refresh the settings
  * adding host record in both hosts file and ssh config file
    * (loacl)$ `mate /etc/hosts`
    * (loacl)$ `mate ~/.ssh/config`
  	
  * To debug ssh access (like if you get denied, you want to know why)
    * $ `ssh -v nodal1`

ssh on github
-------------

  REF: https://help.github.com/articles/managing-deploy-keys
  * do NOT use dash on host name or key file!
  * don't forget to update your ~/.ssh/config file to add your newly generate deploy key
  * hmmm.. i think there's more things need to be done in order to have this works.... does not work if I just add new repo...
    * ok problem solved, MAKE SURE that when clone the project, use the 'hostname' you've defined in the config file
    * ex: git@github.com:Nodally/xenograte-core.git SHOULD BE git@<hostname>:Nodally/xenograte-core.git instead!!


    
# GEM

* Make –no-ri –no-rdoc default for gem install

	$ mate ~/.gemrc
	
	---
	:backtrace: false
	:benchmark: false	
	:bulk_threshold: 1000
	:sources:
	- http://gems.rubyforge.org/
	- http://gems.github.com
	:update_sources: true	
	:verbose: true
	gem: --no-ri --no-rdoc
	
** make sure everything here is correctly written, otherwise it may cause gem command not behave properly
ex: if --no-ri was written as -no-ri, when you do $ gem list, it will not show any gem at all, yet 
it doesn't show any error message either, so it would not be obvious what went wrong


# RMAGIC

we need this to process image!
$ brew update
$ brew install --fresh imagemagick

*** 
more setup check:
https://gist.github.com/1860902


# NODE.JS

* install node (& npm)
  * since i have homebrew install, it is as easy as:
	* `brew install node`
	* REF: http://shapeshed.com/setting-up-nodejs-and-npm-on-mac-osx/

* install node WITHOUT xcode
	* IF you want to install NODE.js without install XCODD, install through binsary
	* http://nodejs.org/download/
	
* uninstall npm
  * if npm installation failed due to old npm already install, please uninstall it
  * try `sudo npm uninstall npm -g`
  * if the above command failed, you should go to the npm source code dir and do
    * `sudo make uninstall`
  * REF: https://npmjs.org/doc/misc/removing-npm.html
    
* package.json
  * `npm init` to create a new package.json
  * REF: http://package.json.nodejitsu.com/

# Grunt

* make sure you already installed NODE.js and NPM (above)
  
  * first need to install 
    * `sudo npm install -g grunt-cli`
  
  * once grunt is installed, we can go to the project folder (where it has package.js) and do
    * `npm install`
  
  * the above script will install all the required library, including grunt, for that project
  * once it's done, then we can run grunt (ex: xeno_beta/UI)
    * `grunt dev`

* reinstall grunt
  * `sudo npm uninstall grunt -g`
  * `npm cache clean`
  * `sudo install grunt -g`

* template
  * all the template should installed/created under `~/.grunt-init`
---
"Fatal error: Arguments to path.resolve must be strings" when doing grunt
update grunt version in package.json and reinstall grunt (above) seems solve the problem
REF: https://github.com/gruntjs/grunt/issues/726

---
"Unable to parse package.json file"
That means your json format is wrong, must be some spacer or bracket or comma missing
easies way to solve it is change the state of the package.json back to what it was, 
because it could be time-consuming to debug it


# TEXTMATE

* Folder Reference / Folder Pattern: (ignore log and uploads folder to make search faster)

!.*/(\.[^/]*|uploads|log|system|CVS|_darcs|_MTN|\{arch\}|blib|.*~\.nib|.*\.(framework|app|pbproj|pbxproj|xcode(proj)?|bundle))$

* Shell Variable: (so we can use command + r to run ruby code)
  * $ `rvm info` to get current ruby info
	* add the following variables:
	  ```
	  TM_RUBY => binaries / ruby
	  GEM_PATH => environment / GEM_PATH
    ```	
	* AND, for rspec, I need to set on extra variable to have this working:
	  ```
	  RUBYOPT => rubygems
    ```
  * REF: http://www.yhds.ca/having-rspec-works-on-textmate-371/	

* Install Textamte Bundle from git
  * REF: https://github.com/timcharper/git-tmbundle
  *REF: http://blog.macromates.com/2008/git-bundle/
  
* $ `mate` command is gone after updating XCode or OS
  * `ln -s /Applications/TextMate.app/Contents/SharedSupport/Support/bin/mate /usr/local/bin/mate`
  * REF: http://stackoverflow.com/questions/9610884/cant-create-a-symbolic-link-with-textmate-in-terminal-mate-command-not-found


# Default Apps

REF: http://www.rubicode.com/Software/RCDefaultApp/

# SVG to Keynotes

$ `svg2keynote <svg_file>`
in keynote: Format / Shape / Make Editable
REF: http://www.christianholz.net/keynote_utilities.html

# UNIX (linux) Commnads
  
  * check path order for binary lookup (it will pickup the first one it finds in the path)
    * $ `echo $PATH`
  
  * process
    * list all process
      * $ `ps aux`
    * list all rail process
      * $ `ps aux | grep rails`
  
  * memory
    * top process / memory usage
      * $ `top`
    * check system memory (NOT WORK SOMETIMES...)
      * $ `cat /proc/memuinfo`
    * check memory usage
      * $ `free -m`
        
  * make file executable
    * $ `chmod a+x <file>`
    
  * move file to bin so we can use it directly
    * $ `sudo mv <file_name> /usr/bin/<file_name>`
    
  * netstat
    * check out all the available servers / ports 
      * $ `netstat -tulnp | more`
      * $ `netstat -atpn|less`
      * $ `netstat -atpun`
    # check port bindings
      * $ `netstat -l`
    * check out only the established connection
      * $ `netstat -atpun | grep ESTABLISHED`
        
  * check open file by process
    * $ `lsof | grep [r]uby;`
    
  * search from history
    * $ `history|grep 'git'`
  
  * history key (up and down) does not work, so is the tab
    * `vi ~/.inputrc` and put the following
    * ```
      ## arrow up
      "\e[A":history-search-backward
      ## arrow down
      "\e[B":history-search-forward
      ```
    * REF: http://askubuntu.com/questions/59846/bash-history-search-partial-up-arrow
    * usually it should NOT happen ever because `/etc/inputrc` is there
    * I'm also opening a ticket: https://cloud.digitalocean.com/support#ticket-161517
    * https://www.digitalocean.com/community/articles/how-to-edit-the-sudoers-file-on-ubuntu-and-centos
    
    * After ask doug, it could be the shell was assigned to the wrong one
      * do `echo $SHELL` to check it, it should be `/bin/bash` NOT `/bin/sh`
      * http://askubuntu.com/questions/325807/arrow-keys-tab-complete-not-working
    
  * copy file with preserved permission
    * $ `cp -p <file>`
  
  * search 
    * file with certain name
      * $ `sudo find $HOME -name '*<search-term>*'`
    * file contains a string
      * $ `sudo grep --color -R -h "<search-term>" / 2>/dev/null`
      * REF: http://www.cyberciti.biz/faq/howto-search-find-file-for-text-string/

  * scp
    * copy file from Local to VM:
      * VM $ `scp -P10000 path/to/file admin@0.0.0.0:path/to/file`
      * VM $ `scp -P10000 -r path/to/directory admin@0.0.0.0:path/to/directory`
    
  * change to root
    * $ `sudo su` and it will prompt for password
  
  * add user
    * $ `adduser <USER-NAME>` to add an entry in `/etc/passwd`
    * $ `sudo adduser <USER-NAME> admin` to add an sudo user
    * $ `passwd <USER-NAME>` to change password of the user
    * $ `groups <USER-NAME>` to check which group(s) the user belongs to
    * $ `sudo usermod -a -G sudo <USER-NAME>` to add sudo group after user is created OR
      adding a file in `/etc/sudoers.d` directory and adding
      ```
      <USER-NAME> ALL=(ALL) ALL
      ```
    * REF: http://askubuntu.com/questions/168280/how-do-i-grant-sudo-privileges-to-an-existing-user
    
Debug
-----
    
  * search log one term
    * $ `cat ~/public_html/silvercore/log/production.log | grep whatever`
    
  * search log multiple terms (OR)
    * $ `cat ~/public_html/silvercore/log/production.log | grep 'warning\|error\|critical'`
    
  * search log multiple terms (AND) - just keep piping it
    * `$ cat ~/public_html/silvercore/log/production.log | grep cart | grep 2012-12-22 | grep POST`
    
  * check apache log, filters out all IP addresses, the sort then removes duplicates
    * $ `awk '{print $1}' access.log|sort -u`
  
  * find out all accesses to a URL, I do
    * $ `grep URL access.log`
  

Firewall
--------
  * iptables
    * REF: http://articles.slicehost.com/2011/2/21/introducing-iptables-part-1
    * list all rules
      * $ `sudo iptables -L`
      * $ `sudo iptables -L --line-number`
    * delete all rules
      * $ `sudo iptables -F`
    * add a rules to a port
      * $ `sudo iptables -A INPUT -p tcp --dport <port> -j ACCEPT`
    * delete a rule (from line-number)
      * $ `sudo iptables -D INPUT <line-number>`
    * search iptable file (could have imported rules from anywhere)
      * $ `find $HOME -name '*iptable*'`
    * backup iptables
      * $ `sudo sh -c '/sbin/iptables-save > /etc/iptables.up.rules'`
      * REF: https://wiki.debian.org/iptables
    * restore iptables
      * $ `sudo sh -c '/sbin/iptables-restore < /etc/iptables.up.rules'`
      * REF: https://wiki.debian.org/iptables
      * MAKE SURE that the port append statement is located in the right place, because iptables rules will take the first one match
        that means the previous statement has higher priority
    * started on a reboot 
      * REF: https://wiki.debian.org/iptables

    
  * ufw 
    * uncomplicated firewall - it is default firewall configuration tool
      * REF: https://help.ubuntu.com/community/UFW
    * check status
      * $ `sudo ufw status verbose`
    * turn ufw on with the default set of rules:
      * $ `sudo ufw enable`
    * turn ufw off
      * sudo ufw disable


Cron
----

* start (eanble) a cron job:
  * `crontab -e` to add command
  * `sudo crontab -e` to add command in root (recommended)
  * REF: http://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job
* to check scheduling that is working:
  * `sudo tail -f /var/log/syslog` if it was using `sudo crontab -e`
  * REF: http://askubuntu.com/questions/56683/where-is-the-cron-crontab-log
* use it with RVM, MUST do the following:
  * creates environment files for your gemset: 
    * `rvm env --path -- ruby-2.0.0-p247@xenograte-core`
  * write a shell script for the ruby script that you want to run, and make specify the environment using:
    * REF: http://rvm.io/integration/cron
  
  
  





# COFFEESCRIPT
http://coffeescript.org/

* since I already install node.js from homebrew, i can just install it through
    
    $ sudo npm install -g coffee-script

* Now I need to make 'coffee' available in console
  To check what module we've installed under npm
  
    $ npm -g list
  
  and make a sym link
  
    $ ln -s /usr/local/share/npm/lib/node_modules/coffee-script/bin/coffee /usr/local/bin/coffee

  OR: 
    
    $ mate ~/.bash_profile
  
    export PATH="/usr/local/share/npm/bin:$PATH"

Commands
--------

    auto compile and make it run in background (this is NOT daemon, it is tie to the current session)

    $ coffee -cwo compiled src
    $ ctrl z
    $ bg
    $ fg # this will forground the process

    or 

    $ coffee -cwo compiled src &
    
# WORDPRESS
  
  * when import database, make sure wp_options table is NOT overwritten, because the root url will screw everything;
  and for some reason, the theme options is NOT carryover
  
  * when doing transition OR setting up development, check the followings:
    * REF: http://codex.wordpress.org/Moving_WordPress
    * After import the database, do the following:
      * 1. Run the script to change base url
        * ```
          SELECT * FROM wp_options WHERE option_name = "home" OR option_name = "siteurl";
          UPDATE wp_options SET option_value = "http://my/project/path" WHERE option_name = "home" OR option_name = "siteurl"
          ```
          There should be two row affected.
        * save a local db backup
        * REF: http://codex.wordpress.org/Running_a_Development_Copy_of_WordPress
      * 2. Replace the domain name in `wp_posts` table
        * the script will allow you to choose the table
        * the script will ask what you want to replace with, make sure it is replace to the proper "http://my/project/path"
        * REF: https://github.com/muskmelon/searchreplacedb2
    * After pull from github
      * .gitignore
        * i'm using default github's gitignore for WordPress + ignore ALL the plugins
      * Make sure .htaccess exists and it's writable, `chmod 755 .htaccess` to make the change
        * Once a writable `.htaccess` is there, you should go to settings/permalink to "save change" again
          It will write/update the proper .htaccess to the disk
        * REF: http://wordpress.org/support/topic/problem-moving-wp-now-only-the-home-page-works
        * of course, it won't work if mod_rewrite module is not enable. Check APACHE "User Level Root & Rewrite" section for that.
      * Change admin email
        * under `Settings`, change `E-mail Address` to 'jamesh@nodally.com'
        * save a local db backup
      * If any Theme/Widget are sending email(s), disable them all
        * ex: for `ClassiPress > Emails`, disable all the email options
        * save a local db backup
      * Make sure `wp-content/upgrade` and `wp-content/uploads` exist 
        * should not need to care if everything is owned by apache
      
  * Update
    * `mate /etc/apache2/httpd.conf` to check your apache user/group name 
      * could be www OR _www OR www-data
    * $ `sudo chown -R _www:_www [PATH/TO/FILE]` to change the ownership to apache
    * now we should be able to use the one click update!
    * for more info checkout APACHE > File Permission section
    
  * update strategy (wip)
    * should update the live site and export the database from live site and import into the local one
      (Yuan: not sure yet, but seems the better way...)
  
  * change wordpress memory
    * $ `sudo vi /var/www/new/wp-config.php`
    * find the line:
      ```
      define('WP_MEMORY_LIMIT', '96M');
      ```
    * make sure also check the memory setting on PHP


# CSS
  
  * unminify css
    * REF: http://mrcoles.com/blog/css-unminify/

# NMAP
   
  * allow us to see all the available port of a computer (using its IP)

Install
--------

  * install (mac)
    * $ `brew install nmap`
 
Examples
--------
 
    # all the local network's available apache (will take a while to scan)
    $ nmap 192.168.50.1-254 -p 80

    # verbose and scan all
    $ nmap -v -A 192.168.50.71
    
    # list of default port:
    3306 - mysql
    80 - apache
    3000 - rails


# MANGODB

* install (mac)
  $ brew install mongodb
  
* update (mac)
  $ brew update
  $ brew upgrade mongodb

* To have launchd start mongodb at login:
  $ ln -sfv /usr/local/opt/mongodb/*.plist ~/Library/LaunchAgents
  $ launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
  
* log
  /usr/local/var/log/mongodb/mongo.log
  
  
# REDIS

Install
-------

  * Install Redis (Method 1)
    http://redis.io/download
    * $ `make`
    * $ `sudo make install`
    * make sure following the 'Installing Redis' section in README file of the tar package
      you will need to run the `make install` and `cd utils; ./install_server`, 
      and it will ask you where to save default config and other questions, see README for detail
    * we use port 9119 for redis
    
  * Install Redis (Method 2) (mac)
    * $ `brew install redis`
    * will make link in `cd /usr/local/bin` 
    
  * Install Redis (Method 3) (linux)
    * $ `sudo apt-get install redis-server`
    * usually will be saved to `cd /usr/bin`
    * uninstall `apt-get --purge remove redis-server`
    
 
Commands
--------
  
  * start with default config
    * $ `redis-server` 
    * $ `redis-server &` to daemanize
    * $ `sudo /etc/init.d/redis_9119 start` if use Method 1
    
  * start with custom config
    * $ `redis-server <PATH-TO-CONFIG>`
    * $ `redis-server /Users/muskmelon/Downloads/redis-2.4.12/redis.conf`
    * $ `sudo redis-server /Users/yuanhsueh/Projects/xenograte/core/config/node_redis.conf`
    * new redis (>2.6.0) has deprecated VIRTUAL MEMORY, so all configuration that prefix with vm- sh commentted out; also, hash-max-zipmap- is taken out too, just use hash-max-ziplist-
    * config can be anywhere, depending on how you organize them like:
      * `sudo vi /etc/redis/9119.conf` if use Method 1
      * `sudo vi /etc/redis/redis.conf`
      * `sudo vi /usr/local/etc/redis.conf`
    * MAKE SURE that the following config exist:
      * bind 127.0.0.1 is uncomment
      * timeout is 300
    
  * stop 
    * list process `ps -ef | grep [r]edis` and kill the process
    * $ `/etc/init.d/redis_9119 stop` if use Method 1
  
  * log
    * $ `tail -f /var/log/redis_9119.log` if use Method 1
    * the location is configurable

  * check redis version
    * $ `redis-cli info`
    
  * debug redis
    * `$ redis-cli`
      > keys *
      > get "b72c1aac4a47c494a76e37045822c5fe"
      > lrange sm1:log:error 0 -1

GEM
---

---
VP-MacBook:server Vance$ gem install redius
ERROR:  Could not find a valid gem 'redius' (>= 0), here is why:
          Unable to download data from https://rubygems.org/ - SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed (https://s3.amazonaws.com/production.s3.rubygems.org/latest_specs.4.8.gz)
the solution is to do this
VP-MacBook:server Vance$ rvm osx-ssl-certs update all

Console
------- 

* redis$ `keys *`
* redis$ `llen "<key>"`
* redis$ `lrange "<key>" 0 -1`
* redis$ `flushall`
 

# ZEROMQ

Install
-------
  
  * Install ZEROMQ (mac)
    * $ `brew install zeromq`


# CHROME

 * ` ⌥ ⌘J` (Command - Option - J) to open Developer Tools and bring focus to the Console.
 * ` ⇧ ⌘C` (Command - Shift - C) to toggle Inspect Element mode.

# DRUPAL

https://drupal.org/documentation/install/settings-file

# Websocket

* onclose
  * http://tools.ietf.org/html/rfc6455#section-7.4
  * https://developer.mozilla.org/en-US/docs/Web/API/CloseEvent?redirectlocale=en-US&redirectslug=WebSockets%2FWebSockets_reference%2FCloseEvent
  * http://stackoverflow.com/questions/16627898/how-to-get-access-to-onclose-event-codes-in-javascript-onclose-function

# Domain

* transfer Nameserver
  * http://www.rackspace.com/knowledge_center/article/rackspace-cloud-essentials-6-what-are-your-name-servers

  * http://support.hostgator.com/articles/hosting-guide/lets-get-started/domain-names-buy-sell-manage/how-can-i-transfer-my-domain-to-you#checklist
  * http://www.rackspace.com/apps/support/portal/1186
  * if it's in go daddy, go to dns.godaddy.com

* transfer DNS Hosting (domain continue to be registered with old domain registrar)
  * http://www.rackspace.com/apps/support/portal/1164

* DNS Zone File
  * if it's in cpanel, go to cpanel > Advanced DNS Zone Editor
  * if it's in rackspace, go to Cloud Control Panel > DNS
  * first create a mirror DNS Zone File
  * copy over all the records from original one to a new one

* Email migration / MX record setup
  * http://www.rackspace.com/apps/support/portal/3900
  * http://www.rackspace.com/apps/support/portal/1148

# Craiglist
LIMIT 120 characters
(eq3|chintz|nood|cb2|"herman miller"|bensen|livingspace|"pottery barn"|"bo concept"|"pottery barn"|"west elm"|natuzzi|inform) 
http://vancouver.en.craigslist.ca/search/sss?zoomToPosting=&catAbb=sso&query=%28eq3%7Cchintz%7Cnood%7Ccb2%7C%22herman+miller%22%7Cbensen%7Clivingspace%7C%22pottery+barn%22%7C%22bo+concept%22%7C%22pottery+barn%22%7C%22west+elm%22%7Cnatuzzi%29&minAsk=&maxAsk=&sort=date&excats=




inform -(AUTOEMPIRE|vehicle|dolls|honda|Lexus|pontiac|audi|ford|bmw|kids|jeep|dodge|suzuki|samsung|motorsports|"Rear lamps"|tires|Mouse|car|toy|toner|BABY|digital|lipstick|children|magic|Pappa|Potty) 
crate barrel
bed lift queen
(stool|stools|chair|chairs|desk|desks|table|tables|dresser|dressers|"side board"|mirror|bookcase|bookshelf|shelf|shelves|shelving|rack|cabinet|drawers|"tv stand"|"night stand"|lamp|lamps|"bed frame"|"Storage Bed"|"solid wood"|"moving sale"|bench|"media console") -(AUTOEMPIRE|vehicle|dolls|honda|Lexus|kia|nissan|pontiac|audi|ford|bmw|kids|jeep|dodge|suzuki|samsung|motorsports|"Rear lamps"|tires|Mouse|car|toy|toner|BABY|digital|lipstick|children|magic|Pappa|Potty|fiat|CHRSYLER|CHEVY|toyota|hyundai|Cadillac|mazda|acura|"MERCEDES-BENZ"|Chrysler|Chevrolet|VOLKSWAGEN|Madonna) 



http://vancouver.en.craigslist.ca/search/sso?catAbb=sso&query=%28stool%7Cstools%7Cchair%7Cchairs%7Cdesk%7Cdesks%7Ctable%7Ctables%7Cdresser%7Cdressers%7C%22side+board%22%7Cmirror%7Cbookcase%7Cbookshelf%7Cshelf%7Cshelves%7Cshelving%7Crack%7Ccabinet%7Cdrawers%7C%22tv+stand%22%7C%22night+stand%22%7Clamp%7Clamps%7C%22bed+frame%22%7C%22Storage+Bed%22%7C%22solid+wood%22%7C%22moving+sale%22%7Cbench%7C%22media+console%22%29+-%28AUTOEMPIRE%7Cvehicle%7Cdolls%7Chonda%7CLexus%7Ckia%7Cnissan%7Cpontiac%7Caudi%7Cford%7Cbmw%7Ckids%7Cjeep%7Cdodge%7Csuzuki%7Csamsung%7Cmotorsports%7C%22Rear+lamps%22%7Ctires%7CMouse%7Ccar%7Ctoy%7Ctoner%7CBABY%7Cdigital%7Clipstick%7Cchildren%7Cmagic%7CPappa%7CPotty%7Cfiat%7CCHRSYLER%7CCHEVY%7Ctoyota%7Chyundai%7CCadillac%7Cmazda%7Cacura%7C%22MERCEDES-BENZ%22%7CChrysler%7CChevrolet%7CVOLKSWAGEN%29+&zoomToPosting=&minAsk=&maxAsk=&sort=date
