# The Problem

## How Can I synchronize files between two file systems?
- Select only the files I want
- Select only files that are different
- Ignore files that need to stay
- Remove files that need to be removed
- Backup files that need to be backed up


I was having a problem where I wanted to synchronize my files from backup on my linux nas/server (WS) thing to my ssd, and I wanted to talk about my experience and solution. I started out with using rsync to sync the entire directory over my target computer, RP400. The initial transfer took a long time, but I figured it would get faster after the initial update, since it would only have to sync files that are different between the two drives.

After I started changing around my file sets, I found that there is no real 'caching' that happens with rsync, so each time you run the script to sync, you have to read every file that's potentially going to be synced. If you're comparing checksum, that takes a big chunk of time. Even if you use 'size-only' or other built-in options to limit the number of things that you're going to transfer, it still takes a while for rsync to actually do the thing. 


### Project Restrictions
- Do whole-file copies; with no partial transfers.
- Reduce the number of files that need to be updated
- Backup files that need to be saved from the target computer (RP400)
- Cache and update file metadata while the files are otherwise not-in use
- Copy files directly from backup/download storage on server (WS) to target folders on (RP400) without using extra space
- Files that are to-be synced can change without needing to move and re-arrange all files on Server (WS) 
- Use only built-in or common utilities that are available on both operating systems
- Should be able to change 

# Solutions
I separated this into two processes, lets "Select" which things actually need to change, then pass that through to rsync in order to replace the changed files.

## Selection

How do we determine what needs to synchronize between computers?

### Selection Priorities
1. Explicitly Excluded files
2. Explicitly Included files
3. Files to sync based on Name
4. Files to sync based on size
5. Files to sync based on checksum
6. Files to sync to backup

### Project Directory
I've built a project directory, where I assume everything that I'm going to sync up will be inside this directory. Since I don't want to have to move all my files and folders into this directory, I'll be using symlinks to my target folders in order to quickly change the file contents as needed.

### Inclusions and Exclusions
The need for this actually comes later in the project, but some files will have to be explicitly included and excluded from this sync. Specifically files that have to do with cached data or logs that happen to share file space on either computer. This which will just be a list of files that needs to be changed or not changed, called include.ls and exclude.ls.

### Files to sync based on Name
I'm starting out by using the linux "Find" command, specifically looking for files, and I'm running this on each system. The command runs locally on (WS) and spits out a dated and identified 'checkpath.csv' file. I do this over SSH on the remote target (RP400) as well to spit out a similar 'checkpath.csv' file then diff the two, putting all files that exist on the server but not the target in one file 'checkpath-diff', and all files that are on the target (RP400) but not the server into another file 'checkpath-bak'. All files with the same name should probably go into a third file 'checkpath-same'.

### Files to sync based on size
For this, I intend to either use find with an output filter, or use du. My intention is to process the files in 'checkpath-same'

### Files to sync to backup
To Do:
- Make a list (RP400-liv) of files and folders that need to be backed up from the target
- Make a list (WS-SUM-BKP) of files and folders from previous backups (newer files replace older files)
- Difference files on target against the files saved in previous backups based on checksum
- Save changes to be made in a file to be processed by rsync and 


### Files to sync based on checksum
For this, I intend to use md5sum

## Synchronization
For this, I intend to use rsync, ssh, diff, and some kind of archiving software like tar, or possibly just atools for versatility

### To Do
- Copy files from server Project File to target
- Copy files from target to server Project File (cached files)
- Copy files from target to server (backup file)
