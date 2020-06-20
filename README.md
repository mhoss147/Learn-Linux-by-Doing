# Learn-Linux-by-Doing


- Type  :quit<Enter>  to exit Vim


# Create New sudo Users


You are working at an organization that has just hired two new technicians. 

One of them will be the backup system administrator, while the other will need the ability to perform some tasks on the system with elevated privileges. 

You will create these two new accounts, and through the modification of the /etc/sudoers file and a separate sudoers file, 

these two new users will be able to invoke the sudo command.


- Create two new users



    Create two new users on the system, and assign the avance user to the wheel supplemental group:

    sudo useradd -m gfreeman
    sudo useradd -G wheel -m avance

    Set the password for both accounts to LASudo321:

    sudo passwd gfreeman
    sudo passwd avance


- Verify the `/etc/sudoers` file and test access.


    Using the visudo command, verify that the /etc/sudoers file will allow the wheel group access to run all commands with sudo. There should not be a comment (#) on this line of the file:

     %wheel  ALL=(ALL)       ALL

    From the cloud_user login, use the su (substitute user) command to switch to the avance account, and use the dash (-) to utilize a login shell:

     sudo su - avance

    As the avance user, attempt to read the /etc/shadow file at the console:

     cat /etc/shadow

    As a regular user, avance does not have sufficient privileges to do so. Rerun the command with the sudo command:

     sudo cat /etc/shadow

    After you have verified that avance can read the /etc/shadow file, log out of that account:

     exit


- Set up the web administrator



Now we need to configure gfreeman's account to have the ability to restart or reload the web server when needed. Since he will be the webmaster, he needs sudo permissions to restart the service.

    First, create a new sudoers file in the /etc/sudoers.d directory that will contain a standalone entry for webmasters. Use the -f option with the visudo command to create this new file:

     sudo visudo -f /etc/sudoers.d/web_admin

    Enter in the following at the top of the file. This will create an alias command group that we can apply to any user or group that we add to this file. This group of commands will contain the necessary commands for restarting or reloading the web server:

    Cmnd_Alias  WEB = /bin/systemctl restart httpd.service, /bin/systemctl reload httpd.service

    Add another line in the file for gfreeman to be able to use the sudo command in conjunction with any commands listed in the WEB alias:

     gfreeman ALL=WEB

    Save and close the file.

    Next, log into the gfreeman account:

     sudo su - gfreeman

    Attempt to restart the web service:

     sudo systemctl restart httpd.service

    Now gfreeman can restart the web server. As the gfreeman user, try to read the new web_admin sudoers file with the sudo command:

     sudo cat /etc/sudoers.d/web_admin

    Since the cat command is not listed in the command alias group for WEB, gfreeman cannot use sudo to read this file.


# ----------------------------------------------------







