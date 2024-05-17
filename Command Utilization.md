# Commands & It's Utilization in Linux

## » ls:
- "ls" is a Linux shell command that lists directory contents of files and directories.  It provides valuable information about files, directories, and their attributes.

```bash
ls [option] [file/directory]
```

## » cd:
- The ‘cd’ command allows users to change their current working directory within the file system. The basic syntax of the `cd` command is as follows:

```bash
cd [directory]
```
## » pwd:
- The ‘pwd,’ which stands for “print working directory.”
- It prints the path of the working directory, starting from the root. pwd is shell built-in command(pwd) or an actual binary(/bin/pwd).
  
```bash
pwd [OPTIONS]
```

## » mkdir: 
-Make directory

```bash
mkdir new_folder
```

## » rm: 
- Remove files or directories.

```bash
rm file.txt
```

## » mv: 
- Move files to directories.

```bash
mv file1.txt new_location/
```

## » touch:
- Create an empty file or update file timestamps.

```bash
touch new_file.txt
```

## » cat: 
- Concatenate and display file contents.

```bash
cat file.txt
```


## » grep: 
- Search for the specified pattern in file.

```bash
grep "pattern" file.txt
```

## » head: 
- Display the first few lines of a file.

```bash
head file.txt
```

## » tail: 
- Display the last few lines of a file.

```bash
tail file.txt
```

## » sudo:
 - The sudo command stands for "superuser do." It allows a permitted user to execute a command as the superuser or another user, as specified by the security policy configured in the sudoers file. The superuser, often abbreviated as "root," has unrestricted access to all files and commands on a Unix-like operating system.

```bash
sudo apt update
```

## » su
- The su command stands for 'substitute user' and allows you to switch to another user account, typically the root user, by entering the target account's password.
- However, it does not set up the target user's environment, meaning you keep your own environment variables and working directory."

```bash
su user_name
```

## » runuser: 
- Similar to su, it allows you to run a command with substitute user and group IDs. 

```bash
runuser -l john -c 'command
```

## » sudo -u:
- The sudo -u command in Linux is used to execute a command as a different user, specified by the -u option.

```bash
sudo -u user_name command
```

## » chown: 
- Change file owner and group.

```bash
chown user:group file.txt
```
###### This will change the owner of file.txt to user and the group to group.



## » chmod:
 - In the chmod command, numbers are used to represent file permissions. Each permission is assigned a numeric value, and these values are used to set or modify permissions for the file's owner, group, and others. Here's how it works:
   - 0: No permissions (---)
   - 1: Execute only (--x)
   - 2: Write only (-w-)
   - 3: Write and execute (-wx)
   - 4: Read only (r--)
   - 5: Read and execute (r-x)
   - 6: Read and write (rw-)
   - 7: Read, write, and execute (rwx)
   ###### These values are combined to represent different combinations of permissions.
- To use chmod with numeric values, you typically provide a `three-digit number` representing the permissions for the owner, group, and others, respectively.

- For example, to give read, write, and execute permissions to the owner, read and execute permissions to the group, and no permissions to others, you would use:
  ```bash
  chmod 750 filename
  ```

- Using these numbers, you can assign different combinations of permissions to the owner, group, and others. Here are some commonly used examples:
  - 740: Owner has read, write, and execute permissions; group has read permission only; others have no permissions.
  - 644: Owner has read and write permissions; group and others have read-only permissions.
  - 755: Owner has read, write, and execute permissions; group and others have read and execute permissions.
  - 600: Owner has read and write permissions; group and others have no permissions.
  - 777: Owner, group, and others have full read, write, and execute permissions (use with caution as it grants full access to everyone).
  - 400: Owner has read permission only; group and others have no permissions.
