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

# commands

all commands should be called in working folder or it's subfolders
they automatically climb up to folder contains syncsrc.rc and read settings there

## push files to server

```sh
../sync-tools/sync-all push
```

this command push (rsync) content of working folder (and it's subfolders) to the server configured in syncsrc.rc
sync executed in --delete and -a mode 
files transfered only if the local change stamp is newer than the server one 

## pull files from server

```sh
../sync-tools/sync-all pull
```

this command pull (rsync) content from the server to working folder 
sync executed in --delete and -a mode 
files transfered only if the servers change stamp is newer than the local one

NOTE.
push/pull processed can be permanently run
stamp check prevents multidirectional flow
i.e. changes tranfered from a last used mechine to server and to other machines
when somebody start using another machines it change stamp updated and this computer becomes a master

NOTE.
multimaster mode DOES NOT WORK, only one master at any time permitted

## watch changes and transfer them

```sh
../sync-tools/dirwatch &
```

start watcher (inotify) process with prepared ssh tunnel 
and transfer all changes to the server and (with sync-all pull) to other seats




