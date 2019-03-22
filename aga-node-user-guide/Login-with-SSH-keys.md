### Login with SSH keys
One of the best options to secure the SSH login is to completely disable the password login and require a SSH key certificate. Only someone with physical possession of the private key can login. 

**_Set up SSH keys for the "admin" user:_**

**For Windows Users**: [Configure “No Password SSH Keys Authentication” with PuTTY on Linux Servers](https://www.tecmint.com/ssh-passwordless-login-with-putty)

* You should have generated three new files. Keep them safe!

![SSH keys files](images/20_ssh_keys_filelist.png)

**For Mac / Linux Users:**

* Back on the machine you’re working on (an easy way to make sure this is the case is to open a new tab in your terminal window) we first need to check for an existing private / public key pair:

   `$ ls -la ~/.ssh/*.pub`

* If files are listed, your public key should be one of the following files (by default):

   ```
   id_dsa.pub
   id_ecdsa.pub
   id_ed25519.pub
   id_rsa.pub
   ```
* If one of these files exist, skip ahead to the "Let's make sure..." bullet point below. If none of those files exist, or you get a ` No such file or directory` do the following to create a new pair:
  
   `$ ssh-keygen -t rsa -b 4096`
   * When you're prompted to "Enter a file in which to save the key," press Enter. This accepts the default file location.

   * Next, to enforce key security, use password [A] to protect your keys. Enter again to confirm.

* Let's make sure that the `~/.ssh` directory exists on the Raspberry pi (Be sure to swap the IP of your Raspberry Pi in for RASPBERRY_PI_IP below):

   `ssh admin@RASPBERRY_PI_IP 'mkdir -p ~/.ssh'`

* Copy over your public key to the Raspberry Pi and set the file mode of the .ssh directory (Again, swap out your Pi's IP for RASPBERRY_PI_IP below). If your public key file is something other than `id_rsa.pub`, substitute its filename below:

   `$ cat ~/.ssh/id_rsa.pub | ssh admin@RASPBERRY_PI_IP 'cat >> ~/.ssh/authorized_keys && chmod -R 700 ~/.ssh/'`

**Once the Raspberry Pi has a copy of your public key, we'll now disable password login:**

* Log in to the Raspberry Pi as "admin" with your SSH key (you shouldn't be prompted for admin's password anymore).

* Edit ssh config file  
`$ sudo nano /etc/ssh/sshd_config`

* Change settings "ChallengeResponseAuthentication" and "PasswordAuthentication" to "no" (uncomment the line by removing # if necessary)  
  ![SSH config](images/20_ssh_config.png)

* Save config file and exit 

* Copy the SSH public key for user "root", just in case  
  `$ sudo mkdir /root/.ssh`  
  `$ sudo cp /home/admin/.ssh/authorized_keys /root/.ssh/`  
  `$ sudo chown -R root:root /root/.ssh/`  
  `$ sudo chmod -R 700 /root/.ssh/`  
  `$ sudo systemctl restart ssh`  

* Exit and log in again. You can no longer log in with "pi" or "bitcoin", only "admin" and "root" have the necessary SSH keys.  
  `$ exit`

:warning: **Backup your SSH keys!** You will need to attach a screen and keyboard to your Pi if you lose it.

