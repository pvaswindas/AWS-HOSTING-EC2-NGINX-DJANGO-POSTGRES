# Commands & It's Utilization in Linux

# su
- The su command stands for 'substitute user' and allows you to switch to another user account, typically the root user, by entering the target account's password.
- However, it does not set up the target user's environment, meaning you keep your own environment variables and working directory."

## sudo:
 - The sudo command stands for "superuser do." It allows a permitted user to execute a command as the superuser or another user, as specified by the security policy configured in the sudoers file. The superuser, often abbreviated as "root," has unrestricted access to all files and commands on a Unix-like operating system.

## chmod:
 - "Chmod," short for "change mode," is a command utilized in the Linux terminal to alter file and directory permissions. It enables the user to specify who can perform which actions on a particular file. Permissions play a critical role, particularly in cloud computing and virtual machines, as they allow multiple users to have controlled access to a single virtual machine. However, not every user should have equal privileges to access all files on a computer.
   
 - The command "chmod 400 keyname.pem" sets the file "keyname.pem" to be read-only for the owner on Unix-like systems. It prohibits all access for others.
