{[date]Last updated June 11, 2026}
# Permissions – read, write, execute
{[author]{pp::stepan-vana}Štěpán Váňa}
{[read_time]5 min read}
{[copy_raw_file]Copy Markdown}
{[view_as_md]View as Markdown}

To prevent anyone in Linux from doing anything they want, permissions exist. These determine who can view a file or folder, who can edit it, and who can execute it (if it is a script). Permissions can be simply divided into 3 attributes: `rwx`, meaning read, write, and execute.

At first glance, it might seem that if you have read, write, and execute permissions within a folder, you can manipulate every individual file inside it in the same way. However, the opposite is true – permissions are defined separately for each file.

## Permissions on a File vs. a Directory

Detailed explanation of the different behavior of permissions on directories and files:

| permission | file | directory |
|---|---|---|
| read ( r ) | reading the file | reading the directory and filenames inside it |
| write ( w ) | writing to the file | creating files and directories |
| execute ( x ) | executing the file | entering the directory |

## Permissions for Owner, Group, and Others

To make things a bit more complex, every file and directory has three permission levels. The owner, the group, and others can each have different permissions for the same object. “Others” refers to users who are neither the owner nor members of the file/directory’s group.

As a result, we have 9 permission attributes in total: `rwxrwxrwx` (`rwx` repeated three times – once for the owner, then for the group, and finally for others).

Still not clear? Let’s say we want a file where:

- the owner can read, modify, and execute it,
- the group can read and execute it,
- others cannot do anything.

If a permission is missing (not assigned, for example the group cannot write), it is replaced with a dash. The permissions would therefore look like this:

- `rwx` for the owner,
- `r-x` for the group (missing `w`, so no writing),
- `---` for others (no permissions at all).

The final notation is therefore `rwxr-x---`.

## Numeric `rwx` Permissions aka `rw-r-----` == `640`

To make notation simpler, each permission (`rwx` – read, write, execute) is assigned a number. By summing these numbers, we get the permission value separately for the owner, group, and others.

The following notations are equivalent:

| textual permission | numeric permission |
|---|---|
| read (r) | 4 |
| write (w) | 2 |
| execute (x) | 1 |

The numbers were intentionally chosen so their sums always produce a unique permission combination. Using the previous example (`rwxr-x---`), if we sum the permissions for the owner, group, and others, we get `750`.

How did we get that?

- The owner can read (`4`), write (`2`), and execute (`1`), total = `7`.
- The group can read (`4`) and execute (`1`), total = `5`.
- Others cannot do anything, total = `0`.

Therefore, the notation `rw-r-----`, which “humanly” means:

- the owner can read and write (`4` + `2` + `0` = `6`),
- the group can only read (`4` + `0` + `0` = `4`),
- others cannot do anything (`0` + `0` + `0` = `0`),

can be written as `640`.

## chmod

Now that we know how numeric permissions work, we can easily change file and directory permissions using this notation. For example, if we want to change the permissions of the file `hello.txt` to `640`, we can do it like this:

```bash
chmod 640 hello.txt
```

## chown

Changing the owner is even simpler with the `chown` command (`ch`ange `own`er) in the format `chown [user] [filename]`

```bash
sudo chown user hello.txt
```

> Changing the owner of a file requires sudo privileges.

## chgrp

If we want to change the group, we use the `chgrp` command (`ch`ange `gr`ou`p`) in the format `chgrp [group] [filename]`

```bash
sudo chgrp group hello.txt
```

> Changing the group of a file requires sudo privileges.

## Recursive Changes

To change permissions, owner, or group for all files in a directory and its subdirectories at once, use the `-R` switch:

```bash
chmod -R 640 folder
```

```bash
sudo chown -R user folder
```

```bash
sudo chgrp -R group folder
```
<br><br>