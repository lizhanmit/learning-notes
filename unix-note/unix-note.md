# Unix Note

## vi

create files or open files: `vi <filename>`

enter into edit mode: press the key **i**

come out of edit mode: press the key **esc**

save the file and exit: Press two keys **Shift + ZZ** or `:wq!`

save the file: `:w`

exit the file without saving: `:q!`

---

## File Management

All data in Unix is organized into files.

### Metacharacters

`*` - 0 or more characters 

`?` - single character

### Files Operation

List all files including hidden files: `ls -a`

List all files (including hidden files) in long listing format: `ls -al`

When using cat to see the content of a file, display line numbers: `cat -b filename`

Count the total number of lines, words, and size in a file: `wc filename`

Count words in multiple files: `wc filename1 filename2 filename3`

Copy a file: `cp source_file destination_file`

Rename a file: `mv old_file new_file`

Delete a file: `rm filename`

Delete multiple files: `rm filename1 filename2 filename3`

Delete a file with prompt: `rm -i filename`

## Directory Management

![linux-file-structure.png](img/linux-file-structure.png)

Go in your last directory: `cd -`

List files in a directory: `ls dirname`

Create multiple directories: `mkdir dirname1 dirname2 dirname3` 

Create parent directories: `mkdir -p /parentdir/childdir`

Delete an empty directory: `rmdir dirname` 

Delete a non-empty directory: `rm -R dirname`

---

## File Permission 

### Changing Permissions  

#### in absolute mode

Example 

`chmod 755 filename`: set permission of the file as owner with read, write and execute, group with read and execute, and others with read and execute. 

![chmod-in-absolute-mode.png](img/chmod-in-absolute-mode.png)

#### in symbolic mode

`chmod <o/u/g> <+/-/=> <r/w/r> filename` 

- `o` : others 
- `u` : owner 
- `g` : group  

+ `+` : add permission  

- `-` : remove permission  

- `=` : set permission  

Example 

`chmod o+wx filename`: add write and execute permissions to other users.  

### Changing Owners and Groups   

Change the owner of filename to the username: `chown username filename` 

Change the group of filename to the username: `chgrp groupname filename` 

---

## Environment

Set an environment variable: `ENVVAR = "test"`

Access an environment variable: `echo $ENVVAR `



