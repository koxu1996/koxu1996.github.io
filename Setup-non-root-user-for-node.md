# Setup non-root user

Generating an ssh key pair: https://www.ssh.com/ssh/keygen/

`<username>` should be replaced below with your user.  This is assuming you will be remote logging into server using a key.

Create the user with no password as the key is your password
```
sudo adduser <username> --disabled-password
```
Create authorized_keys with your key to login
```
sudo su - <username>
mkdir .ssh
chmod 700 .ssh
touch .ssh/authorized_keys
```
Use the editor of your choice and paste your .ssh public key in `.ssh/authorized_keys`

Exit out of <username> into your previous sudo-er account or root
```
exit
```
Add your user to sudo-ers:
```
sudo visudo
```
Type `<username>  ALL=(ALL:ALL) NOPASSWD:ALL`
Below
```
root    ALL=(ALL:ALL) ALL
```

Now you should be able to `ssh -i <your ssh private key> <username>@<server ip>` and not use root user.