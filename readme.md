# RSYNC for single- or multi- computers workflow

This toolset can sync folders between one or more workstations (seats) and single server folder

In this workflow all chages made at the current workstation instantly transfered to the server

Also, when user moved to another workstation he can sync it's working folder with server one

```

+-----+
|        |
| WS-1 | \   sync-on-chage | push 
|        |  \
+-----+   \
              \
		+------+
		|         |
		| server |
		|         |
		+------+
              /
+-----+   /
|        |  /  pull, then sync-on-change | push
| WS-2 | /
|        |
+-----+

```

# folder structure

Client and server folder should follow next pattern:

$ROOT_DIR/working_folder
$ROOT_DIR/working_folder/syncsrc.rc
$ROOT_DIR/.seats

where $ROOT_DIR is an arbitary folder on client and server machines
	(can be differen)
for example 
	$HOME/my-project-dir
on the client machine and
	/var/www/html
on server side

$ROOT_DIR/.seats - is a folder where toolset store change stamps

$ROOT_DIR/working_folder/syncsrc.rc 
	is a config file (in sh syntax) describes rsync command and server location

usually, it's useful to have folder $ROOT_DIR/sync-tools
with content of this repo

# command

the command should be called in working folder or it's subfolders
it automatically climbs up to folder contains syncsrc.rc and read settings

## watch changes and transfer them

```sh
../sync-tools/dirwatch &
```

start watcher (inotify) process with prepared ssh tunnel 
and transfer all changes to the server
also periodically pull changes from remote server 
if some changes made locally when no monitor exists
next _dirwatch_ call transefers this changes

NOTE.
multimaster mode DOES NOT WORK, only one master at any given time permitted
NOTE.
but multiple dirwatcher can work on different machines at same time,
a machine with latest changes becames master and others bacame slave

## stop monitoring

```sh
../sync-tools/dirwatch stop
```


