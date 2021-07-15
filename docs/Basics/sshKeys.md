---
layout: default
title: sshkeys
parent: Computation Basics
has_children: false
nav_order: 5
has_toc: true
---

### Introduction

The Secure Shell Protocol (or SSH) is a cryptographic network protocol that allows users to securely access a remote computer over an unsecured network.

Though SSH supports password-based authentication, it is generally recommended that you use *SSH keys* instead. SSH keys are a more secure method of logging into an SSH server, because they are not vulnerable to common brute-force password hacking attacks.

Generating an SSH key pair creates two long strings of characters: a public and a private key. You can place the public key on any server, and then connect to the server using an SSH client that has access to the private key.

When the public and private keys match up, the SSH server grants access without the need for a password. You can increase the security of your key pair even more by protecting the private key with an optional (but highly encouraged) passphrase.

Step 1 --- Creating the Key Pair
------------------------------

The first step is to create a key pair on the client machine. This will likely be your local computer. Type the following command into your local command line:

```bash
ssh-keygen -t ed25519
```

You should see

```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/Users/max/.ssh/id_ed25519):
```

You will see a confirmation that the key generation process has begun, and you will be prompted for some information, which we will discuss in the next step.

Step 2 --- Specifying Where to Save the Keys
------------------------------------------

The first prompt from the `ssh-keygen` command will ask you where to save the keys:

```
Enter file in which to save the key (/Users/max/.ssh/id_ed25519):
```

Press `ENTER` here to save the files to the default location in the `.ssh` directory of your home directory.

Alternately, you can choose another file name or location by typing it after the prompt and hitting `ENTER`.

Step 3 --- Creating a Passphrase
------------------------------

The second and final prompt from `ssh-keygen` will ask you to enter a passphrase. Hit `ENTER` twice.

```
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/maxwell/.ssh/id_ed25519.
```

Finally, it will give you some pretty art under this:
```
The key fingerprint is:
```

The public key is now located in `/Users/maxwell/.ssh/id_ed25519.pub`. The private key is now located in `/Users/maxwell/.ssh/id_ed25519`.

Step 4 --- Copying the Public Key to Your Server
----------------------------------------------

Once the key pair is generated, it's time to place the public key on the server that you want to connect to.

You can copy the public key into the server's `authorized_keys` file with the `ssh-copy-id`command. Make sure to replace the example username and address (which are mine!)

```
ssh-copy-id bertolem@cubic-login.uphs.upenn.edu
```
This will take a long time. It should say:

```
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/Users/maxwell/.ssh/id_ed25519.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
```


Once the command completes, you will be able to log into the server via SSH without being prompted for a password. However, if you set a passphrase when creating your SSH key, you will be asked to enter the passphrase at that time. This is your local `ssh` client asking you to decrypt the private key, it *is not* the remote server asking for a password.

Step 5 --- Disabling Password-based SSH Authentication (Optional)
---------------------------------------------------------------

Once you have copied your SSH keys onto the server, you may want to completely prohibit password logins by configuring the SSH server to disable password-based authentication.

Warning: before you disable password-based authentication, be certain you can successfully log onto the server with your SSH key, and that there are no other users on the server using passwords to log in.

In order to disable password-based SSH authentication, open up the SSH configuration file. It is typically found at the following location:

```
sudo nano /etc/ssh/sshd_config

```

Copy

This command will open up the file within the `nano` text editor. Find the line in the file that includes `PasswordAuthentication` (or create the line if it doesn't exist), make sure it is not commented out with a `#` at the beginning of the line, and change it to `no`:

/etc/ssh/sshd_config

```
PasswordAuthentication no
```

Copy

Save and close the file when you are finished. In `nano`, use `CTRL+O` to save, hit `ENTER` to confirm the filename, then `CTRL+X` to exit.

Reload the `sshd` service to put these changes into effect:

```
sudo systemctl reload sshd

```

Copy

Before exiting your current SSH session, make a test connection in another terminal to verify you can still connect.

Conclusion
----------

In this tutorial we created an SSH key pair, copied our public key to a server, and (optionally) disabled password-based authentication completely.

For more information about SSH and the SSH service, including how to set up multifactor authentication, please read our related tutorials:
