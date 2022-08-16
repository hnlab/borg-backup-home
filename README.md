# borg-backup-home
An example of borg to backup your home in linux.

### 0. Read this blog first: [BorgBackup —— 增量备份方案](https://wzyboy.im/post/1106.html)

### 1. Download [Standalone Binary](https://borgbackup.readthedocs.io/en/stable/installation.html#standalone-binary) and install (copy) borg to local and remote.
```
wget https://github.com/borgbackup/borg/releases/download/1.2.1/borg-linux64
chmod +x borg-linux64
mv borg-linux64 ~/bin/borg
```
Add `export PATH=$HOME/bin:$PATH` in `~/.bashrc`.

**Install borg at remote in the same way.**

### 2. create remote repo
```bash
hostname=x161.hn.org
borg_bin='~/bin/borg'
backup_path=/backup/postdoc/jcyang
borg_folder=borgbackup
repo_name=lab
repo_path=$backup_path/$borg_folder/$repo_name

# create folder
ssh -t $hostname mkdir -p $repo_path

# create repo without encryption
borg init --verbose $hostname:$repo_path --encryption=none 

# create first archive (commit) to backup all files in `~/bin`
borg create --verbose --stats $hostname:$repo_path::first_archive ~/bin
```

### 3. backup your `/home`
change
```
export BORG_REPO=CHANGE_HERE_TO_YOUR_REPO
```
to
```
export BORG_REPO=ssh://x161.hn.org:22/backup/postdoc/jcyang/borgbackup/lab
```

test setup and run backup
```
borg info --verbose ::first_archive

# backup all files in /home first time
bash borg.sh
```

### 4. setup crontab (open another terminal)
```
chmod +x borg.sh
cp borg.sh ~/bin
```

```
# crontab -e
# add this command into crontab, it run backup files daily.
# please change path/to/borg.sh
0 0 * * * /home/jcyang/bin/borg.sh >/tmp/borg.sh.log 2>&1
```
