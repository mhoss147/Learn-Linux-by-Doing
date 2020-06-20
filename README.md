# Learn-Linux-by-Doing


- Type  :quit<Enter>  to exit Vim
    
    
# Managing Users in Linux

- Add the Users to the Server

    To add users to the system we can run 

    useradd <username>

    So we would run

    useradd tstark

    useradd cdanvers

    useradd dprince


- Create the `superhero` Group

 
    To create a new group we would run

    groupadd <groupname>

    So for this task

    groupadd superhero


- Set `wheel` Group as the the `tstark` Account's Primary Group

    For this task we would run usermod like this

    usermod -g wheel tstark


- Add `superhero` as a Supplementary Group on All Three Users

    There isn't an easy way to do this all at once, so we need to run the following command for each user

    usermod -aG superhero <username>

    So

    usermod -aG superhero tstark

    usermod -aG superhero dprince

    usermod -aG superhero cdanvers

- Lock the `dprince` Account

     To lock an account all we have to do is run:

    usermod -L dprince


# ----------------------------------------------------------------


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




# Using SSH, Redirection, and Permissions in Linux

    Additional Information and Resources

    We need to set up a new server for a developer to use. He needs to be able to connect with ssh from server1 to server2 as the dev user, without having to enter a password password.

    Once that's set up there are some tar files on server1 that need to be copied over and extracted. To enable the developer to verify that it was done correctly, we should have all the output from the extraction go to /home/dev/tar-output.log.

    We need to make sure that new files have the correct permissions (readable and writeable by only the user) by setting the umask correctly.

    Once complete we should verify permissions on the very important /home/dev/deploy.sh script and run it.

    Log in to one of the servers (which will then be referred to as server1 throughout the rest of the lab) using the credentials provided:

    ssh dev@<server1_PUBLIC_IP>

    The password for the dev user is the same as for cloud_user.


- Enable SSH to Connect Without a Password from the `dev` User on `server1` to the `dev` User on `server2`

     We need to use SSH keys to satisfy this requirement, so generate them with this:

     [dev@server1]$ ssh-keygen

    Then run:

     [dev@server1]$ ssh-copy-id <server2 IP>
    


- Copy All tar Files rom `/home/dev/` on `server1` to `/home/dev/` on `server2`, and Extract Them Making Sure the Output is Redirected to `/home/dev/tar-output.log`


    We need to use a method of copying files over a network. scp is the best tool, like this:

    [dev@server1]$ scp *.gz <server2 IP>:~/

    Then connect to server2 using ssh:

    [dev@server1]$ ssh <server2_IP>

    Then we can extract the files:

    [dev@server2]$ tar -xvf deploy_content.tar.gz >> tar-output.log

    [dev@server2]$ tar -xvf deploy_script.tar.gz >> tar-output.log

Make sure to use >>, so that the output is appended rather than overwritten (just one >).


- Set the Umask So That New Files Are Only Readable and Writeable by the Owner

    The task is asking to make new files with the following permission: 0600.

    So we can do subtraction to figure out what our umask should be.

    0666 <-- Default

    0600 <-- Desired

    ----

    0066 <-- What we need to set

    So we run:

    [dev@server2]$ umask 0066


- Verify the `/home/dev/deploy.sh` Script Is Executable and Run It


     First, we check permissions on deploy.sh:

     [dev@server2]$ ls -l deploy.sh

     There's no execute bit. Let's add one:

     [dev@server2]$ chmod +x deploy.sh

     And then run it:

     [dev@server2]$ ./deploy.sh











