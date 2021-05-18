# Installation Ubuntu 20.04 - Installing MiniConda3

First, decide if you want to install for all users or current user.
> mbdash: I install for all users on my VMs

## Installing for current user
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod +x Miniconda3-latest-Linux-x86_64.sh
# ./Miniconda3-latest-Linux-x86_64.sh

sudo ./Miniconda3-latest-Linux-x86_64.sh
# choose default install location
```

## Installing for All users
Run the Install commands above for single user, but when asked for an install location, 

set the install path to: `>>> /opt/miniconda3`.
See below:
```
# Miniconda3 will now be installed into this location:
# /root/miniconda3
#
#   - Press ENTER to confirm the location
#   - Press CTRL-C to abort the installation
#   - Or specify a different location below
[/root/miniconda3] >>> /opt/miniconda3
```

## Give rights to a user group
In some circumstances, you might want to ensure your user group has access to miniconda3.
```
cd /opt/
sudo chmod 770 -R miniconda3/
sudo chown -R :USERGROUP miniconda3/

exec $SHELL
```

## Add to your user paths
```
# USER been your username
cd /home/USER/
su USER

nano .bashrc
export PATH="/opt/miniconda3/bin:$PATH"

exec $SHELL
```

## Update Conda and your current shell
```
conda update conda
conda init bash
exec $SHELL
```
