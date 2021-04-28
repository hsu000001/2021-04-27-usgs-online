---
title: SSH to Remote
teaching: 15
exercises: 0
questions:
- "How can I tell github who I am?"
objectives:
- "Create a local SSH key."
- "Add SSH key to accepted keys on Github."
- "Confirm SSH can authenticate."
keypoints:
- "Keep your friends close and your private keys closer."
---

There are a couple different ways to access Github, each with their pros and cons.
One of these methods is **SSH**, which is a highly secure way method of data transfer.
In order to use **SSH** with Github, we need to generate **SSH keys**.
These come as a pair, one which stays on your computer, and one which is copied to Github.
They allow Github to confirm that your computer should be associated with your account.
Here we will walk through how we can set this service up.

**Note:**
Currently the recommended ways to access Github is in flux.
We chose the **SSH** method due to the fact that it will continue to work long into the future.

The information here is a summary of the Github's more in depth
[walk-through](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)

First, lets check if any SSH keys are currently present on your computer:

~~~
$ ls -al ~/.ssh
~~~
{: .language-bash}

~~~
drwx------  2 anthony anthony 4096 Apr 13  2020 .
drwxr-xr-x 81 anthony anthony 4096 Apr 27 13:15 ..
-rw-r--r--  1 anthony anthony 5462 Apr 27 10:11 known_hosts
~~~
{: .output}

For many people, the above command will error, and that is ok.
We will be generating the data for this directory soon.

**Note:** If your output looks like what is bellow,
you should just follow along with the next section and pickup at the Github step.
~~~
drwx------  2 anthony anthony 4096 Apr 13  2020 .
drwxr-xr-x 81 anthony anthony 4096 Apr 27 13:15 ..
-rw-------  1 anthony anthony  464 Apr 27 10:05 id_ed25519
-rw-r--r--  1 anthony anthony   97 Apr 27 10:05 id_ed25519.pub
-rw-r--r--  1 anthony anthony 5462 Apr 27 10:11 known_hosts
~~~
{: .output}

Assuming you do not have any files that look like `id_ed25519`, lets generate a new key:
~~~
$ ssh-keygen -t ed25519 -C "your_email@example.com"
~~~
{: .language-bash}

~~~
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/.ssh/id_ed25519):
~~~
{: .output}

We have asked `ssh-keygen` to generate a new key pair.
In order to save this pair in the default directory, press enter.

~~~
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase): 
~~~
{: .output}

I recommend entering a password, which will add an additional layer of security.
Enter this password twice to move on.

This will generate some pretty art, and we are done.
Let's check to see if everything was made correctly.

~~~
$ ls -al ~/.ssh
~~~
{: .language-bash}

~~~
drwx------  2 anthony anthony 4096 Apr 27 13:32 .
drwxr-xr-x 81 anthony anthony 4096 Apr 27 13:27 ..
-rw-------  1 anthony anthony  411 Apr 27 13:32 id_ed25519
-rw-r--r--  1 anthony anthony   97 Apr 27 13:32 id_ed25519.pub
-rw-r--r--  1 anthony anthony 5462 Apr 27 10:11 known_hosts
~~~
{: .output}

Let's go ahead and make sure the `ssh-agent` is running

~~~
$ eval `ssh-agent -s`
~~~
{: .language-bash}

~~~
Agent pid 26536
~~~
{: .output}

If this gives and `Agent pid`, you are good to go.

As we said before, the private key, `id_ed25519`, will stay on your computer
and the public key, `id_ed25519.pub`, will be given to github.
Let's add the private key to the `ssh-agent`.

~~~
$ ssh-add ~/.ssh/id_ed25519
~~~
{: .language-bash}

~~~
Identity added: /home/.ssh/id_ed25519 (your_email@example.com)
~~~
{: .output}

Now we need to copy the public key.
Do not copy any white space.

~~~
$ nano ~/.ssh/id_ed25519.pub
~~~
{: .language-bash}

The next part assumes that you have set up your Github account already.
Let's go to the settings in our Github accounts.

<img src="https://docs.github.com/assets/images/help/settings/userbar-account-settings.png" 
     alt="Go to settings" 
     height="500px"/>

On the left side of the page, look for the **SSH and GPG keys** tab.

<img src="https://docs.github.com/assets/images/help/settings/settings-sidebar-ssh-keys.png" 
     alt="Go to SSH keys"
     height="350px"/>

Click to add a new SSH key.

<img src="https://docs.github.com/assets/images/help/settings/ssh-add-ssh-key.png" 
     alt="Add an SSH key"
     height="250px"/>

We are presented with a spot to put the public key.
Paste the copied data from `id_ed25519.pub` into the `key` panel.
Since each computer will need its own SSH key,
give your key a name which references your current computer such as `Work Laptop`.
Once finished, click **Add SSH Key**.

<img src="https://docs.github.com/assets/images/help/settings/ssh-key-paste.png" 
     alt="Paste in our copied SSH key"
     height="450px"/>

You may be asked to re-enter your password.

<img src="https://docs.github.com/assets/images/help/settings/sudo_mode_popup.png" 
     alt="Confirm password"
     height="250px"/>

That should do it. 
You have now generated a way for Github to determine who you are as you interact with it.
Let's do a test to determine whether we can contact github

~~~
$ ssh -T git@github.com
~~~
{: .language-bash}

~~~
Warning: Permanently added the RSA host key for IP address '140.82.114.3' to the list of known hosts.
Hi UserName! You've successfully authenticated, but GitHub does not provide shell access.
~~~
{: .output}

