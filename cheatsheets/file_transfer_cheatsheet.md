# File Transfer Cheat Sheet


## Rsync
transfer hierarchical data, such a directory and its member files, It is able to detect what has changed between the source and destination data, and saves time by only transmitting these changes (SEE: +files: RSYNC )

### Useful Options
- `-rsh=ssh`: specify the remote shell to use (**HPC is ssh**)
- `-progress`: show progress during transfer
- `v, --verbose`: increase **verbosity**
- `-stats`: show stats during transfer
- `n, --dry-run`: perform dry run (no changes), shows what this command would do
- `a, --archive`: **archive** mode copies files **recursively** and **preserves:** , file , user & group  and symbolic links permissions ownerships  timestamps
- `u, -update`: skip files that are the same/newer on destination
- `l, --links`: Copy the symlinks as symlinks (same as -a) — only use if **relative**
- `L, --copy-links`: Copy the **target** file that a symlink is pointing — use if paths **!same**
- `-ignore-existing`: skip files that already **exist** in destination, regardless of timestamp
- `-include/exclude=PATTERN`: **include** or **exclude** files matching PATTERN
- `-include-from/exclude-from=FILE.txt`: **include** or **exclude** patterns from FILE.txt
- `-include [string]`: - only only files matching parameters (ex: *.txt)
- `-exclude [string]`: - exclude particular files/directories, or regex
- `-remove-source-files`: removes source files after move (**NOT RECOMMENDED**)
- `-r`: copies data recursively (**does not preserve** ownership, timestamps)
- `-z`: compress file data


**⚠️NOTE:** When sending multiple files, I like to do a dry-run with `-n`, `--dry-run`. This also speeds up the transfer by first generating a list of files.

```sh
# first do a dry run
rsync -nauv /home/zach/SRC ${USER}@host.penn.edu:/projects/PROJECT/
# then run it without -n
rsync -auv /home/zach/SRC ${USER}@host.penn.edu:/projects/PROJECT/
```

### Examples

```sh
# SEND FILES
# ----------
# send file and rename if !exist (--> dest OR dest/file1 if dest/ !exist)
rsync -av src/file1 /dest
# send file and create directory if !exist (--> dest/file1) # ALWAYS STABLE
rsync -av src/file1 /dest/
# send all files in a directory to new directory (--> dest/file*)
rsync -av src/* dest/

# SEND DIRECTORY
# --------------
# send a src/ and create /dest if !exist
# if src/ is a dir, and using -r/-a, the trailing slashes aren't needed
# THESE WILL ALL RESULT IN --> dest/src/ whether dest exists or !exist
rsync -av src dest
rsync -av src dest/
rsync -av src/ dest
rsync -av src/ dest/

# RECEIVE FILE (ALWAYS INCLUDE dest filename)
# -------------------------------------------
# receive a file from server
rsync -av ${USER}@host.penn.edu:/home/${USER}/<file.txt> newfile.txt
# Receive a remotedir from server (--> /home/zach/dir/)
rsync -av ${USER}@host.penn.edu:/home/${USER}/dir /home/zach
# receive all files from remotedir (--> dest/files)
rsync -av ${USER}@host.penn.edu:/home/${USER}/dir/ /home/dest/
#---preserve path
# Use the -R or --relative option to preserve the full path.
# insert a dot /./ into the path where you want to break the path
rsync --relative /lpc/home/${USER}/./my_documents/file.txt /home/zach/
# would result in this path:
/home/zach/my_documents/file.txt
```