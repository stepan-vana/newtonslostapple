{[date]Last updated June 11, 2026}
# Working with Files
{[author]{pp::stepan-vana}Štěpán Váňa}
{[read_time]3 min read}
{[copy_raw_file]Copy Markdown}
{[view_as_md]View as Markdown}

An expansion of terminal basics – overview of useful commands. 

## `stat` – file information

Displays detailed information about a file or directory. Similar to `ls -la`, but the output can be formatted.

```bash
stat file                 # detailed info about file/directory
stat -c '%a' file         # permissions in numeric form (e.g. 755)
```

The `-c` option defines output formatting. All options can be viewed via `man stat`.

---

## `find` – searching for files

Used to search for files and directories based on various criteria.

```bash
find /home/user/dirA -name "fileA"   # search by filename
find ~ -name "fileA"                 # same using ~ instead of /home/user
find ~/folder -type d -name "x"      # search for directories (-type d)
find ~ -perm 755                     # search by permissions
find ~ -name "*.md"                  # files with .md extension
find ~ -name "*backup"               # files ending with 'backup'
```

The * (asterisk) represents any number of characters – so-called globbing.

---

## `grep` – searching text

Short for global regular expression print. Used for searching strings inside text.

It can be used in two ways:

**1) Reading directly from a file:**

```bash
grep "hello" fileA
```

**2) Reading from stdin via pipe:**

```bash
cat fileA | grep "hello"
```

Overview of standard streams:

|Stream|Number|Description|
|---|---|---|
|stdin|0|input stream (e.g. password input for sudo)|
|stdout|1|output stream (printed to console, can be redirected using | or >)|
|stderr|2|error messages|

## `strings` – readable text from binary files

Similar to `cat`, but prints only printable characters (letters, numbers, symbols). Useful for extracting strings from executable files.

```bash
strings programA                        # all printable characters
strings programA | grep "Hello world!"  # filter output using grep
```
<br><br>