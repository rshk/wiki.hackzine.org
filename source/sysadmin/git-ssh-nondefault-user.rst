Using Git via SSH and a non-standard user
#########################################

**Use case:** You currenty use GitHub or another git service via ssh, and have
a defualt user configured in your ``~/.gitconfig`` and uses your ``~/.ssh/id_rsa`` key
for connecting. So far so good.

Now, you want to use a separate user for certain repositories, for some reason.
Here it is an explanation on how to do this.


First, add something like this to ``~/.ssh/config``::

    Host github-otheruser
        IdentityFile ~/.ssh/id_rsa-otheruser
        User git
        HostName github.com
        Port 22

The, just clone the repository and configure user in one command::

    git clone github-otheruser:otheruser/myrepo \
         --config=user.name='Other User' --config=user.email=otheruser@example.com
