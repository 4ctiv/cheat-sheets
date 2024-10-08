<body style="background-color:rgb(40, 44, 52);color:lightblue;">

# Linux Cheat Sheet
> [!Tip]- Github Web: Outline view
> If you view this on guithub an outline can be displayed via clicking on the list icon while viewing this file (top right, next to edit button)

## Linux basics

### Aplication menu shortcuts:  
> |     Configuration Place / Hotkey   |                Description                 |
> |:-----------------------------------|:-------------------------------------------|
> | ``` /usr/share/applications     ```| Systemwide                                 |
> | ``` ~/.local/share/applications ```| User-specific                              |
> | ``` /etc/bash.bashrc            ```| global bash config file                    |
> | ``` [STRG] + [ALT] + [F_]       ```| changes between virtual consoles (`_` 0-9) |


### Terminal basics
> |             Code            |                 Comments                |
> |:----------------------------|:----------------------------------------|
> |``` su USER               ```| switch user in terminal                 |
> |``` chmod 766 FILE        ```| 7 = rwx for specified file, alt +rwx    |
> |``` cat FILE              ```| print file content in terminal          |
> |``` touch NAME            ```| creates File with NAME                  |
> |``` whoami                ```| print current user username             |
> |``` who                   ```| List all currently logged in users      |
> |``` grep $1 $2            ```| "filter" $1 string using $2 string      |
> |``` $(CODE)               ```| Use output of CODE as text              |
> |``` lsblk                 ```| List all drives and partitions          |
> |``` cfdisk [DEVICE]       ```| Modify partitions on given disk         |
> |``` df -h                 ```| Shows disk space used etc.              |
> |``` sed 's/ALT/NEU/g'     ```| replace ALT with NEW in $1 text         |
> |``` diff DAT1 DAT2        ```| prints diffrences between DAT1 and DAT2 |
> |``` script FILE _ exit    ```| logs content of _ as text in FILE       |
> |``` command >> file       ```| append command output to the end of file|
> |``` scp user@ip file copy ```| copy files via ssh between devices      |
> |``` for file in REGEX.pdf; do mv $file ${file#REGEX2}; done ```| Replaces all REGEX matching files with REGEX2 |
> |``` w		             ```| Display all logged in users in a system |
> |``` hostnamectl ```| Show System information  |


### Shell Script Tricks
> |                         Code                   	       |                Description                |
> |:-------------------------------------------------------|:------------------------------------------|
> |``` \#!/usr/bin/env                                  ```| Set default shell as interpreter "syntax" |
> |``` \#                                      	        ```| Comments in Code                          |
> |``` sudo -v                                          ```| request root privilegies                  |
> |``` if [["$(sudo -nv)" == ""]];then _; fi;           ```| Check for root privilegies and run _      |
> |``` "$?"                                             ```| return exit status of last command        |
> |``` "$@"                                             ```| List with all run arguments (as strings)  |
> |``` "$*"                                             ```| One String with all run arguments         |
> |``` "$#"                                             ```| return number of all run arguments        |
> |``` printf %"$COLUMNS"s  \| tr " " "-"       	      ```| Create 1 line of "-" symbols              |
> |``` seq -s_ $COLUMNS \|tr -d '[:digit:]'      	      ```|               "                           |
> |``` for i in {1..90}; do echo -n "-"; done  	        ```| Repeat 90 times "-"                       |
> |``` $(hostname -I \| awk '{ print $1 }')     	      ```| Get own local IP                          |
> |``` kill $(pidof "PROGRAMM" \| awk '{print $1}')	    ```| Kill specific programm by name            |
> |``` head -x TEXT                                     ```| ersten x Zeilen von TEXT (datei)          |
> |``` tail -x TEXT                                     ```| letzen x Zeilen von TEXT (datei)          |
> |``` nc ADDRESS PORT                                  ```| Verbindung zu ADRESS:PORT                 |
> |``` wc TEXT                                          ```| zaehlt woerter anzahl in TEXT             |
> |``` date --rfc-3339=date                             ```| get current date: yyyy-mm-dd              |
> |``` [ -C PATH ]; echo $?                             ```| Exists PATH & PATH c={d,r,w,x}?           |
> |``` notify-send "MESSAGE" -u normal -t 5000 -a "APP" ```| Send push-up MESSAGE for 5s sender "APP"  |
> |``` alias name command args                          ```| [command args] avalable by typing "name"  |
> |``` figlet -f smslant TEXT \| lolcat                 ```| Writes TEXT as "big font" in terminal     |
> 
>  #### Sh Scripts - Work with Interactive prompts
>  Create "<NAME>.exp" ("\<TEXT\>" is a variable) text file (expect script) with the following content: 
>  ```bash
>  set timeout 10
>  set pswd [lindex $argv 0]
>  spawn COMMAND WITH INTERACTION
>  expect "password:" #This is the last text before the expected input occures 
>  send "$pswd\r";
>  interact
>  ```
>  use the script: ``` <NAME>.exp <INPUT>``` ("\<TEXT\>" are variables)


### SSH-keys
> |                 Code                     |                 Comments                 |
> |:-----------------------------------------|:-----------------------------------------|
> |``` ssh-keygen -t ed25519              ```| Generate ssh key, saved in ~/.ssh/       |
> |``` ssh-copy-id -i KEY_LOCATON/KEY_NAME.pub -p SSH_PORT  USER@SERVER_ADDRESS    ```| Setup ssh key for the given ssh login     |
> |``` ssh USER@SERVER_ADDRESS -i KEY_LOCATON/KEY_NAME ```| Login using the specified SSH-KEY        |
> By default the ssh keys can be found in "~/.ssh/KEYS". 
> Use SERVER_ADDRESS as KEY_NAME for automated ssh key usage

#### SSH-user allowed user list
>|              Code              |       Description         |
>|--------------------------------|---------------------------|
>|``` /etc/ssh/sshd_config ```    | sshd_config file location |
>|``` echo "AllowUsers USER1 USER2 ..." >> /etc/ssh/sshd_config```| Allow only USER1 USER2 ... to login via ssh  |
>|``` /etc/init.d/sshd restart ```| Restart sshd              |

### SSH-Agent
- place the following in `nano ~/.config/systemd/user/ssh-agent.service`
```bash
[Unit]
Description=SSH key agent

[Service]
Type=simple
Environment=SSH_AUTH_SOCK=%t/ssh-agent.socket
ExecStart=/usr/bin/ssh-agent -D -a $SSH_AUTH_SOCK

[Install]
WantedBy=default.target
```
- Add to ssh auth config: `mkdir ~/.config/environment.d/ && echo 'SSH_AUTH_SOCK="${XDG_RUNTIME_DIR}/ssh-agent.socket"' >> ~/.config/environment.d/ssh_auth_socket.conf`
  - Enable Service: `systemctl --user enable --now ssh-agent`
    - Verify: `systemctl --user status ssh-agent`
  - Add SSH keys: `echo 'AddKeysToAgent  yes' >> ~/.ssh/config`
    - Set correct file perm's: `chown $USER:$USER -R ~/.ssh/ && chmod 600 -r ~/.ssh/`
    - list existing identities: `ssh-add -l`

> NOTE: 
> When `keepassxc` has problems finding the ssh agent set keepassxc's SSH_AUTH_SOC to `/run/user/$(id -u)/ssh-agent.socket` (& SSH_SK_PROVIDER to `ssh-agent`)
> - Test using `ssh-add -l` in terminal after adding a key via keepassxc


### Grub password protection
> #### Generated grub password:
> |                 Code                     |                        Comments                     |
> |:-----------------------------------------|:----------------------------------------------------|
> |``` grub-mkpasswd-pbkdf2               ```| Generate grub password hash (starting with grub....)|
> |``` sudo nano /etc/grub.d/00_header    ```| Edit grub configuration files (with nano)           |
> 
> ##### Use generated grub password in grub:
> ```bash
> echo " 
>     cat << EOF
>     set superusers="USERNAME"
>     password_pbkdf2 USERNAME PW-HASH
>     EOF " >> /etc/grub.d/00_header # appends text to 00_header config file
> ```
> 
> ##### Change specific entries to require no Authentification in grub configs 
>  <code> sudo nano "/etc/grub.d/GRUB_CONFIG" </code>
>  - add <code>--unrestricted</code> before <code>\${CLASS}</code> parameter
>  - add <code>--users USER1 USER2 ...</code> before <code>\${CLASS}</code> parameter to set specific user auth
> 
> ##### Example:
> ```bash
> sudo nano "/etc/grub.d/10_linux" # contains default system boot entries
> echo menuentry '$(echo "$title" | grub_quote)' --unrestricted ${CLASS} $menuentry_id_option #...
> echo menuentry '$(echo "$os" | grub_quote)' --unrestricted ${CLASS} $menuentry_id_option #...
> ```
>  [enabe/update] grub (sets password protection): ``` sudo update-grub ```


### Oh my posh terminal prompt
#### Download & install Programm 
> sudo wget https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/posh-linux-amd64 -O /usr/local/bin/oh-my-posh
>
> Note: Install and use a nerd font to remove broken characters
>
> sudo chmod +x /usr/local/bin/oh-my-posh
>
#### Setup bash
> mkdir ~/.poshthemes && wget https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/blue-owl.omp.json -O ~/.poshthemes/.blue-owl.omp.json
> nano ~/.bashrc
> - at the end add: ```eval "$(oh-my-posh init bash --config ~/.poshthemes/.blue-owl.omp.json)"```

----

## Linux networking

### Network configuration files
> Routing table: `/etc/hosts` (set own routs `endpoit_addr to_be_routed_addr`)
>
> Network table: `/etc/networks`
>
> DHCPCD config: `/etc/dhcpcd.conf`


### IPv6 Privacy extension
> file: `/etc/dhcpcd.conf` 
>
> `slaac hwaddr`  turns off privacy extension (server)
> `slaac private` turns on  privacy extension (pc)

### Linux ufw firewall
>|                      Befehl                                               |                  Beschreibung                     |
>|:--------------------------------------------------------------------------|:--------------------------------------------------|
>|``` sudo ufw enable                                                     ```| Enables ufw (also for following boots)            |
>|``` sudo ufw disable                                                    ```| Disable ufw (also for following boots)            |
>|``` sudo ufw logging [full/high/low/medium/off/on]                      ```| Enable ufw logging, default path `/var/log/ufw*`  |
>|``` sudo ufw status numbered                                            ```| Get ufw status and list of set port-rules         |
>|``` sudo ufw delete RULE ``` v ``` sudo ufw delete NR                   ```| Delete specified RULE / Nr (from status numbered) |
>|``` sudo ufw app list                                                   ```| Available Application Profiles (alternative to IP)|
>|                                                                           |                                                   |
>|``` sudo ufw default allow outgoing                                     ```| Default Outgoing  behavior if no rule exists      |
>|``` sudo ufw default deny incoming                                      ```| Default Incomming behavior if no rule exists      |
>|``` sudo ufw allow port 443 192.168.178.0/24                            ```| Allow requests from specified IP on PORT          |
>|``` sudo ufw deny from 192.168.178.255                                  ```| Deny ALL incomming requests from specified IP     |
>|``` sudo ufw deny to   192.168.178.255                                  ```| Deny ALL outgoing requests from specified IP      |
>|``` sudo ufw deny port 80                                               ```| Deny ALL access on PORT                           |
>|``` sudo ufw limit port 22                                              ```| Raitlimit specified port (deny on 6+ req in 30s)  |
>|``` sudo ufw allow proto tcp from 0.0.0.0/0 to 192.168.178.0/24 port 22 ```| Sample configuration for a network rule           |
>|``` sudo ufw allow 25565/tcp comment "Minecraft PC-Server"              ```| Sample configuration with comment (good practice) |
>
> Recommended Setup (with ssh enabled): 
> ```bash
> sudo su # run the below as superuser
> ufw limit proto tcp from 192.168.0.0/16 to any port 22 comment "ssh (homenetwork)"
> ufw allow from 127.0.0.0/8 to any comment "loopback subnet"
> ufw default deny incoming
> ufw default allow outgoing
> ufw enable
> ufw logging medium
> # Add further ports needed to expose (e.g. 443\tcp )
> # ufw limit proto [tcp|udp] from [EXTERNAL_SUBNET|EXTERNAL_IP|any] to [any|INTERNAL_SUBNET|INTERNAL_IP] port [PORT|FROM_PORT:TO_PORT] comment "SERVICE"
> ```

### Cron Job Sheduler Syntax (cronie package)
> ```bash
> # run to edit: sudo crontab -e
> # run to view: sudo crontab -l
> #
> # syntax event based schedule:
> # event:= reboot, yearly, monthly, weekly, daily, hourly
> # @event user-name command_to_be_executed
> # @reboot systemctl restart nginx
> ######################################################################
> # syntax time based schedule:
> #
> # Use * in fields to skip them
> # .-------------- minute (0-59)
> # |  .------------ hour (0-23)
> # |  |  .---------- day of month (1-31)
> # |  |  |  .-------- month (1-12)
> # |  |  |  |  .------ weekday [0-6] OR sun [0],mon [1],tue [2],wed [3],thu [4],fri [5]sat [6]
> # |  |  |  |  |
> # *  *  *  *  * (user) command/path_to_command args
> #55  3  *  *  * root /sbin/reboot -r +5
> #*/30 * * * * ping -c1 1.1.1.1 &>/dev/null && (pidof nextcloud > /dev/null && echo "NC already Running" || nextcloud&) || echo "Offline"
> ```
> - Note: If a script uses ```notify-send``` add ```export DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus export DISPLAY=:0;``` at the beginning
> - Note2: When running crontab scripts environment variables and path may be broken for basic scripts, use `cd ABS_PATH && ...` before your script as well as `#!/usr/bin/env` to yur script as it's first line (if not already `#!/...` is set)

----

## Linux audio

### Virtual audio cable + loopback Setup (pulse audio)
> ```bash
> #This sets up a virtual audio cable named Virtual_Sink, use pacmd or pactl
> pactl load-module module-null-sink sink_name="NAME_SINK" sink_properties="device.description='NAME Sink'"
> 
> # Get Output ID's for the next step
> pactl list sources
> 
> # Create link from Output1 to Output2: "pactl load-module module-loopback source=Output1_ID sink=Output2_ID"
> pactl load-module module-loopback source="Virtual_Sink.monitor" latency_msec=1
> 
> # Permanent setup:
> sudo -s
> echo "load-module module-null-sink sink_name=Virtual_Sink sink_properties=device.description='Virtual Sink'" >> /etc/pulse/default.pa
> echo "load-module module-loopback source=Virtual_Sink.monitor" >> /etc/pulse/default.pa
> ``` 
> Note: It might be that multiple loopback modules do not load correctly 

----

## Filesystem related

### Common FS terminal commands
> |                  Command                  |                 Description                    |
> |:------------------------------------------|:-----------------------------------------------|
> |``` lsof DRIVE_MOUNT_PATH               ```| List all accessing precesses of the mount path |
> |``` lsblk                               ```| List all drives with mounted paths etc.        |
> |``` umount -f MOUNTED_PATH              ```| Unmount specific mount on given path           |
> |``` mount /dev/PARTITION FULL_MOUNT_PATH```| Mount existing partition to given path         |

### System Paths
> |               Abriviation               |             (Path) Description                   |
> |:----------------------------------------|:-------------------------------------------------|
> |``` "./"                              ```| Relates to working directory                     |
> |``` "~/"                              ```| Relates to current user home directory           |
> |``` $VAR                              ```| Relates to the string in variable VAR            |
> |``` pwd                               ```| print full working directory                     |
> |``` ls -la PATH                       ```| list path files and folders                      |
> |``` cd PATH                           ```| change working directory                         |
> |``` mkdir NAME                        ```| creates Directory with NAME                      |
> |``` /usr/share/applications           ```| System wide application menu shortcuts           |
> |``` ~/.local/share/applications       ```| User-specific application menu shortcuts         |
> |``` echo user:passwd \| chpasswd      ```| change password of user to passwd                |
> |``` du -sh PATH                       ```| List used diskspace of specified path            |
> |``` where programm                    ```| List install path(-s) of PROGRAMM                |
> |``` find ROOT_PATH -type f -name REGEX```| Search from given root for file matching regex   |
> |``` find ROOT_PATH -type d -name REGEX```| Search from given root for folder matching regex |

### Mounting WebDav
> - Required packages/drivers: ```davfs2```
> - Add user to webdav group: ```sudo usermod -aG davfs2 //Optional```
> - Mount drive: ```mkdir -p FULL_PATH_MOUNT_FOLDER && sudo mount -t davfs WEBDAV_URL FULL_PATH_MOUNT_FOLDER```
> - fstab entry: ```WEBDAV_URL FULL_PATH_MOUNT_FOLDER davfs rw,uid=USER, nofail 0 0```
>   - user credentials: ```mkdir -p ~/.davfs2/ && echo "WEBDAV_URL WEBDAV_USER WEBDAV_PASSWORD" >> ~/.davfs2/secrets && chmod 600 ~/.davfs2/secrets```
>     - global (fstab) credentials: `/etc/davfs2/secrets`

### Mounting FTP storage
>> Manual Mount
>> ```bash
>>  sudo pacman -S curlftpfs
>>  sudo mkdir /media/PC-FOLDER/ && sudo chown $(whoami):$(whoami) /media/PC-FOLDER/
>>  curlftpfs ftp://FTP-USER:FTP-PASSWORD@FTP-ADDRESS/FTP-SUBFOLDER /media/PC-FOLDER/
>>  ```
>>  fstab: ``` curlftpfs#FTP-USER:FTP-PASSWORD@FTP-ADDRESS/FTP-SUBFOLDER /media/PC-FOLDER/ fuse auto,user,uid=1000,allow_other,_netdev 0 0 ```
> 
>> 
>>
>>
>>
>>
>
>> To prevent the password to be shown in the process list, create a .netrc file in the home directory of the user running curlftpfs:
>> ```bash
>> echo "machine FTP-ADDRESS login FTP-USER password FTP-PASSWORD" >> ~/.netrc && chmod 600 ~/.netrc
>> curlftpfs --netrc ftp://FTP-ADDRESS/FTP-SUBFOLDER /media/PC-FOLDER/ 
>> ```

### BTRFS quick guide
Prequesit: `btrfs`

#### BTRFS management
> Snapshots and subvolumes
> |                                 Command                                 |            Description             |
> |:------------------------------------------------------------------------|:-----------------------------------|
> |``` sudo btrfs subvolume list PATH_TO_MOUNTED_VOLUME                  ```| List Subvolumes of a partition     |
> |``` btrfs filesystem label DEVICE NEW_LABEL ```                          | Change btrfs fs label to NEW_LABEL |
> |``` btrfs subvolume create PATH_TO_SUBVOL                             ```| Creates subvolume                  |
> |``` btrfs subvolume snapshot /mnt/btrfs /mnt/btrfs/snapshot_of_root   ```| Create Snapshot as Subvolume       |
> |``` btrfs su cr MOUNTPOINT/@SNAPSHOT_NAME ```                            | Create Subvolume Snapshot          |

#### Ext3/4 to Btrfs conversion (TODO)
> **BACKUP YOUR DATA FIRST**
> Prequisits: `btrfs-convert`
> 
> ```btrfs-convert /dev/partition```

#### BTRFS + timeshift
> Recommended Subvolumes: `@` , `@root` , `@home` , `@srv` , `@cache` , `@log` , `@tmp` , ( `@.snapshots` )
>
> Create Subvolumes: ```btrfs subvolume create ./@ && ... ```
> 
> Mount Subvolumes (/etc/fstab): ``` UUID=MAIN_BTRFS_PARTITION_UUID   /MOUNT_TO_PATH   btrfs   subvol=/SUBVOL_PATH,defaults,noatime,compress=zstd   0   0 ```
> - `/SUBVOL_PATH` with the setup above would be e.g. `/@home` for the `@home` subvolume
> 
> |    SUBVOL    | MOUNT_TO_PATH |                             Args                           |
> |--------------|---------------|------------------------------------------------------------|
> | `@`          | `/`           | `defaults,noatime,compress=zstd,subvol=/@                ` |
> | `@root`      | `/root`       | `defaults,noatime,compress=zstd,subvol=/@root            ` |
> | `@home`      | `/home`       | `defaults,noatime,compress=zstd,subvol=/@home            ` |
> | `@srv`       | `/srv`        | `defaults,noatime,compress=zstd,subvol=/@srv             ` |
> | `@cache`     | `/var/cache`  | `defaults,noatime,compress=zstd,subvol=/@cache           ` |
> | `@log`       | `/var/log`    | `defaults,noatime,compress=zstd,subvol=/@log             ` |
> | `@tmp`       | `/var/tmp`    | `defaults,noatime,compress=zstd,subvol=/@tmp             ` |



### NTFS mount parameters
> Prequisites: `ntfs-3g` 
> 
> Mount Options: ```defaults,windows_names,users,uid=USER,gid=USER,rw,exec,nofail,x-gvfs-show,remove_hiberfile```
> - `users,uid=USER,gid=USER` will set the given user called `USER` ownership (Current user values: `id -u` (userID), `id -g` (groupID), `$USER` (username))
> - `rw,exec` enables read, write and execute permissions for the above user
> - `nofail,x-gvfs-show,remove_hiberfile` still boot if mount not possible, show in user interface, attempt to still mount rwx if previous windows hypbernation exists
> - `windows_names` Usefull to not get in conflict with reserved characters in windows for files
>
> Example (/etc/fstab): ``` LABEL=Data   /media/Data   ntfs-3g   defaults,windows_names,users,uid=USER,gid=USER,rw,exec,nofail,x-gvfs-show,remove_hiberfile   0   0```

### exFat mount parameters
> Prequisites: `exfatprogs`
> 
> Mount Options: ```nosuid,nodev,relatime,uid=user,gid=user,fmask=0000,dmask=0000,allow_utime=0022,iocharset=utf8,errors=remount-ro,rw,exec,nofail,x-gvfs-show,user```
> - [Documentation](https://www.kernel.org/doc/Documentation/filesystems/vfat.txt)
> 
> Example (/etc/fstab): ``` LABEL=Game_Drive   /media/Game_Drive   exfat   nosuid,nodev,relatime,uid=USER,gid=USER,fmask=0000,dmask=0000,allow_utime=0022,iocharset=utf8,errors=remount-ro,rw,exec,nofail,x-gvfs-show,user   0   0```
>
> NOTE: To use steam proton on exfat it is neccessary to mount compdata & shadercache of steam to a unix supported filesystem (because of symlinks)
> ```/home/USER/.local/share/Steam/steamapps/compatdata   /media/Game_Drive/SteamLibrary/steamapps/compatdata   none   defaults,bind,nofail   0   0```
> ```/home/USER/.local/share/Steam/steamapps/shadercache   /media/Game_Drive/SteamLibrary/steamapps/shadercache   none   defaults,bind,nofail   0   0```


### Git usefull commands
> ```git
> // Throw away local changes & reset BRANCH to origin (online version) 
> git reset --hard origin/BRANCH
> git pull origin BRANCH
> ```
> ```git
> // Merge local BRANCH1 into local BRANCH2 (Update BRANCH2 with BRANCH1 changes)
> git checkout Branch3
> git merge Branch1
> git checkout Branch2
> git rebase Branch1
> ```
> ```git
> // Get current branch name
> git branch
> ```
> ```git
> // Update local branches, use '--dry-run' insted for fetch simulation only
> git fetch --all
> ```

### Offline saving Webpage(-s)
> ```bash
> wget \
>     --recursive \
>     --no-clobber \
>     --page-requisites \
>     --html-extension \
>     --convert-links \
>     --restrict-file-names=windows \
>     --no-parent \
>     ----limit-rate=128k \
>         http://www.example.com/starting_page_for_download
> ```
>  This saves a webpage on a folder structure and with lnks redirecting to the files not the connected webpage 

----
## Linux Fixes

##### NTFS troubbleshooting
> Fix NTFS ro mode: ``` sudo ntfsfix -d /dev/DRIVE_PARTITION ```
> Also make sure you had windows shutdown correctly and without fastboot before
> - Prevent windows hybernation ```powercfg.exe /hibernate off``` (exchange `off` with `on` for re-enabling hibernation)


### Nextcloud troubbleshooting
> ```sudo -u www-data php /var/www/nextcloud/occ status``` - get current nc status
>
#### Nextcloud Sync-Client no persistent login
> Fix login prompt on every run ```yay -S gnome-keyring seahorse```

#### Server Side
> ##### Maintenece mode
> ```sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --on```
>
> ```sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --off```
>
> ##### Fix 503 error after update
> Delete opcache: ```mv /data-ro/ncdata/data/.opcache /data-ro/ncdata/data/.opcache_old && mkdir /data-ro/ncdata/data/.opcache```
> Restart server / docker container
>
> ##### Fix Undefined class constant 'MAJOR_VERSION'
> 1) Check `/var/www/nextcloud/config/config.php` and (backup first then) remove apps from `'app_install_overwrite'`
> 2) Check `/var/www/nextcloud/apps/` for the above removed apps folders and remove them (you may want to backup first)

> ##### MariaDB management
> ```mysqlshow nextcloud``` - list tables of db named "nextcloud" if not existent check
>
> ```mysql -uroot -p``` - access SQL (mariadb) shell, may ask for pw try with empty pw
>
> ```USE nextcloud; SHOW TABLES;``` - in SQL select nextcloud database, now SQL commands can be run on table like normal 
>
> #### MariaDB remove File-Lock
> ```DELETE FROM oc_file_locks WHERE 1;``` - Remove all file locks in nextcloud

> #### MariaDB statements
> ``` BEGIN TRANSACTION; {SQL statements}; COMMIT TRANSACTION;``` - best practice for SQL commands that change db 
>
> ``` {SQL statements}; COMMIT;``` - best practice simlified version for SQL commands that change db 

> ##### OCC update file database (“Got an error reading communication packets”)
> ```sudo -u www-data php /var/www/nextcloud/occ files:scan-app-data``` - !!! may take a long time !!!
>
> ```pstree -apl `pidof cron``` - check ammount of unning crontabs

> #### OCC fix database 
> ```sudo -u www-data php /var/www/nextcloud/occ maintenance:repair``` - fix interrupted upgrades etc.
>
> ```sudo -u www-data php /var/www/nextcloud/occ db:add-missing-primary-keys``` fix primary key issues
>
> ```sudo -u www-data php /var/www/nextcloud/occ db:add-missing-indices``` - update table structure
>
> ```sudo -u www-data php /var/www/nextcloud/occ db:add-missing-columns``` - update table structure
>
> ##### OCC rescann files (server-side)
> ```sudo -u www-data php /var/www/nextcloud/occ files:scan -v USERNAME``` - scann all files in nc USER directory
>
> ##### OCC manage Nextcloud apps
> ```sudo -u www-data php /var/www/nextcloud/occ app:list``` - list local apps 
>
> ```sudo -u www-data php /var/www/nextcloud/occ app:enable APPNAME``` - {enable,disable} apps

----

## Linux Container and VM's

### Docker misc
> NOTE: If you use docker and ufw you also need to install `ufw-docker` for the filerwall to include docker container ports
> 
> ```sudo wget -O /usr/local/bin/ufw-docker \ https://github.com/chaifeng/ufw-docker/raw/master/ufw-docker && sudo chmod +x /usr/local/bin/ufw-docker && ufw-docker install```

> | Command                                      | Description                                                    |
> |:---------------------------------------------|:---------------------------------------------------------------|
> |``` docker update CONTAINER --cpus=".X"   ``` | [Limit container CPU usage to X%](https://docs.docker.com/config/containers/resource_constraints/#cpu)|
> |``` docker update CONTAINER --memory="Xg" --memory-swap="Xg"   ```| [Limit container memory to X GiB](https://docs.docker.com/config/containers/resource_constraints/#--memory-swap-details) /
> |``` docker update CONTAINER --restart=unless-stopped```| [Set Docker-container auto restart policy](https://docs.docker.com/config/containers/start-containers-automatically/#use-a-restart-policy)
> |``` docker ps --format '{{ .ID }} \| {{ .Names }} \| {{.Image}} \| {{ .State }} \| {{ .Ports }}' \| sed 's/:::.*,//g' \| sed 's/0.0.0.0>//g' ```| [Running docker container custom overview](https://docs.docker.com/config/containers/resource_constraints/#limit-a-containers-access-to-memory) /
> |``` docker stats --no-stream               ```| Basic Docker Resoruces tabular (CPU%, RAM%, ...)view            |
> |``` docker ps -a                           ```| List all docker containers on system                            |
> |``` docker system prune                    ```| Clean unused containers, networks, images; Optionally: volumes  |
> |                                              |                                                                 |
> |``` docker run -it CONTAINER /bin/bash     ```| Öffnet interaktive (bash) im Docker container (falls vorhanden) |
> |``` docker start CONTAINER                 ```| Startet spezifizierten Docker-container                         |
> |``` docker stop  CONTAINER                 ```| Stoppt spezifizierten Docker-container                          |
> |``` docker logs  CONTAINER                 ```| Show logs of the specified container                            |
> |``` docker inspect CONTAINER               ```| Information about a docker container (storage etc.)             |
> |``` docker update CONTAINER ARGS           ```| Updated existierenden CONTAINER mit ARGS                        |
> |``` docker cp CONTAINER:SRC_PATH DEST_PATH ```| Copy Files from Container SRC_PATH to Host DEST_PATH            |
> 
> Note: Portangaben sind wie folgt: ```host_port:container_port``` e.g. ```8080:80``` host expose ```8080```, container thinks it is exposing ```80```

#### Build/Compose docker container
> - build: `docker build -t NAME --no-cache --build-arg--no-cache --build-arg var="val" .`
> - compose: `docker-compose up -d --no-cache .`

#### Edit existing docker container (e.g. Ports)
> `docker stop <container-id>`
> 'cd /var/lib/docker/containers/<container-id>'
> 'nano ./hostconfig.json'
> 'systemctl restart docker' # Note: this will restart whole docker setup
> 'docker start <container-id>'
> 'docker ps -a' # Verify the change

#### Docker use Ipv6
> Docker deamon config: Add the following to `/etc/docker/daemon.json`
> ```json
> {
> "ipv6": true,
> "fixed-cidr-v6": "2001:db8:1::/64",
> "experimental": true,
> "ip6tables": true
> }
> ```
> - `ipv6` enable IPv6 networking on default net
> - `fixed-cidr-v6` assigns subnet to default bridge net (dyn. IPv6 addr alloc)
> - `ip6tables` enables additional IPv6 packet filter rules (network isolation & port mapping)
>   - parameter requires `experimental` to be set to true

#### Docker compose quick reference
> See: [The Compose file Docs](https://docs.docker.com/compose/compose-file/03-compose-file/)
>```docker-compose.yaml
> version: 3 # Optional, default is latest version
> services: 
>   service1: # Add docker container
>     container_name: example-container
>     labels: # Add Metadata
>       com.example.description: "Service with host internet"
>     build: .
>     platform: linux/arm64/v8  # Set host platform for container
>     image: test/test-container # Container Base-Image
>     configs:
>      - config1
>     networks:
>       hostnet: {}
>     expose: # Set additional ports avalable from container
>       - "3000"
>       - "8000"
>     links: #TODO
>     volumes: # Mount static file systems
>       - volume1:/path-container-folder
>     tmpfs:   # Mount temporary file systems
>        - /run
>     environment:
>       - ENV_NAME="ENV_VALUE"
>     logging: # TODO
>     restart: unless-stopped # no/always/on-failure/unless-stopped
>   service2:
> 
> networks: # Default network "host", also exist "none"
>   network1: # Name ot docker-network e.g. frontend
>     name: docker-net1
>     labels:
>       com.example.description: "Example network"
>     driver: custom-driver-1
>     external: true
>           config:
>             subnet: 172.28.0.0/16
>             ip_range: 172.28.5.0/24
>             gateway: 172.28.5.254
>             aux_addresses:
>               host1: 172.28.1.5
>               host2: 172.28.1.6
>   hostnet:
>     external: true
>     name: host
>   nonet:
>     external: true
>     name: none
>
> volumes:
>   volume1:
>     name: "actual-volume-name" # volume ID hard-coded but actuald ID set on runtime
>     labels:
>       com.example.description: "Database volume"
>     external: true # volume already exists & lifecycle is managed externaly
> configs:
>  # TODO: https://docs.docker.com/compose/compose-file/08-configs/
> secrets:
>   certificate-secret:
>     external: true
>     file: ./path-to-file.cert
>   parameter-secret:
>     environment: "${OAUTH_TOKEN}" # Value Interpolation (docker build '--build-arg VAR=VAL')
>  # TODO: https://docs.docker.com/compose/compose-file/09-secrets/
>```

#### Pi-hole docker setup
```compose.yaml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    # For DHCP it is recommended to remove these ports and instead add: network_mode: "host"
    ports: # host_port:container_port
      - "53:53/tcp"
      - "53:53/udp"
#     - "67:67/udp" # Only required if you are using Pi-hole as your DHCP server
      - "8080:80/tcp" # Website
    environment:
      TZ: 'Europe/Berlin' # Current Linux: $(timedatectl | grep "Time zone" | awk '{print $3}')      WEBPASSWORD: LhDZyNyG 'set a secure password here or it will be random'
    # Volumes store your data between container upgrades
    volumes: # host_path:container_path , '/usr/share/' common app data directory
      - '/usr/share/docker-data/pihole/etc-pihole:/etc/pihole'
      - '/usr/share/docker-data/pihole/etc-dnsmasq.d:/etc/dnsmasq.d'
    #   https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
#   cap_add:
#     - NET_ADMIN # Required if you are using Pi-hole as your DHCP server, else not needed
    restart: unless-stopped

# Start/Update running: `docker compose up -d`
# Pihole Web-Interface accessable on: `http://localhost:8080/admin`
```

#### Nextcloud docker setup (btrfs)
> ##### fstab mount: 
> 
>``` LABEL=DRIVE_LABEL /DRIVE_MOUNT_PATH  btrfs defaults,subvol=/,compress,autodefrag,inode_cache,nofail 0 0 ```
>
>``` curlftpfs#IP_ADDRESS/REMOTE_PATH_TO_BACKUPS/ /LOCAL_PATH_TO_BACKUPS  fuse  allow_other,user,uid=USERNAME,gid=USERNAME,x-systemd.automount,x-systemd.requires=network-online.target,x-s>  ```

> ##### btrfs partition setup 
>``` mkfs.btrfs /dev/DRIVE_PARTITION ```
>
>``` btrfs filesystem label /dev/DRIVE_PARTITION DRIVE_MOUNT_PATH ```
>
>``` btrfs su cr /DRIVE_MOUNT_PATH/@nextcloud ```
>
>``` mkdir /DRIVE_MOUNT_PATH/@nextcloud/nc_data ```
>
>```bash
> # Create docker container 
> docker run -d -p 4443:4443 -p 443:443 -p 80:80 \\
> -v /DRIVE_MOUNT_PATH/@nextcloud/nc_data:/data \\
> -v /REMOTE_PATH_TO_BACKUPS/ncp-backups:/LOCAL_PATH_TO_BACKUPS/ncp-backups \\
> --name nextcloudpi ownyourbits/nextcloudpi  DDNS_URL
>```

> ##### PHP module "imagick" 
> ``` PHP module "imagick" ```
> Ignore warning and leave disabled as it is a common security problem.
> - See: https://stack.watch/product/php/imagick/

### KVM Hypervisor Setup
> ```bash
> # Prequisits: Check if KVM HW-Acceleration can be used
> sudo kvm-ok
> 
> # Installation:   HW-Emu Hypervis VM-running-deamon     VM-Network   VM-manage-utils 
> sudo apt install -y qemu qemu-kvm libvirt-daemon-system bridge-utils virtinst 
> sudo systemctl enable libvirtd --now
> 
> # Add user to kvm group (Insecure?, Optional)
> sudo usermod -aG kvm USERNAME
> 
> # Check if services are running -> "active: (running)"
> sudo systemctl status libvirtd
> 
> # Check virtual network existence ("virbr0" or simmilar)
> ip addr
> 
> # GUI Frontend software (Optional)
> sudo apt install -y virt-manager
> ```

### Remote Container Management (Cockpit)
> ```bash
> sudo apt install -y cockpit cockpit-machines cockpit-podman
>     # OPTIONAL: virt-viewer cockpit-ovirt-dashboard cockpit-networkmanager
> sudo systemctl enable cockpit.socket --now
> 
> # Open Firewall (Optional, Situational)
> sudo firewall-cmd --permanent --zone=public --add-service=cockpit
> sudo firewall-cmd --reload
> 
> # Check if service is running:
> sudo systemctl status cockpit.socket
> ```
>
> #### Remote Management via Cockpit (User Setup)
> - Web-Access: https://SERVER-IP:9090/ // On the same pc use http://localhost:9090/
> - Login: User-credentials of a server's user

----

## Other

### Linux printer setup (CUPS)
```shell
sudo apt install cups ghostscript        # Common Unix Printing System & Typescript
sudo apt install gutenprint              # Printer driver collection
sudo systemctl enable --now cups.service # Enable cups on system
```

### Linux Antivirus install (ClamAV)
```
sudo apt-get install clamav clamav-freshclam clamav-docs clamtk # clamtk GUI App
sudo freshclam # Update clamav database
sudo systemctl start clamav-daemon.service && sudo systemctl enable clamav-daemon.service

# Location of config file: /etc/clamav/clamd.conf

# Test if clamAV is working
curl https://secure.eicar.org/eicar.com.txt | clamscan - | grep "Win.Test.EICAR_HDB-1 FOUND"

## How to scan:
clamscan PATH_TO_FILE/FILE                                # scann file
clamscan --recursive --infected PATH_TO_FOLDER/FOLDER     # scann folder
clamscan -ir DRIVE_ROOT/ | grep FOUND >> clamAvReport.txt # scann drive
```

### Nice to know stuff
> | Programm              | Description                       |
> |:----------------------|:----------------------------------|
> |``` neofetch ```       | fancy disply of system info       |
> |``` gtop ```           | graphical htop                    |
> |``` mdam --detail ```  | create/watch raid configuration   |
>
> #### Open-VPN split tunneling 
> To <a url="https://medium.com/@Dylan.Wang/how-to-split-tunnel-traffic-with-openvpn-6420d1440fa">not route homenet ip's via vpn</a> add ```route-nopull \n route 192.168.255.0 255.255.255.0 ``` to openvpn configurationfile 


> ### Usefull Links
> #### <a url="https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet">Markdown cheat sheet (syntax)</a>
> #### <a url="https://www.jwillikers.com/btrfs-layout">BTRFS layout</a>  ;  <a url="https://btrfs.readthedocs.io/en/latest/Administration.html">BTRFS mount options</a>
> #### <a url="https://www.reddit.com/r/linux_gaming/comments/oz3uw4/share_the_steam_library_with_windows_using_exfat/">Exfat Mount options for steam</a>
> #### <a url="https://ohmyposh.dev/">Oh my posh website</a>
> #### <a url="https://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/User/Modules/#pulseaudiomodules">Pulse Audio modules</a>
> #### <a url="https://linuxconfig.org/set-boot-password-with-grub">Grub password protection</a>
> #### <a url="https://wiki.archlinux.org/title/CurlFtpFS">CurlFtpFS Arch Doc</a>
> #### <a url="https://www.techrepublic.com/article/how-to-set-up-an-sftp-server-on-linux/">SFTP Server Setup</a>
> #### <a url="https://www.cyberciti.biz/faq/howto-limit-what-users-can-log-onto-system-via-ssh/">Restrict users login via SSH</a>
> #### <a url="https://github.com/pi-hole/docker-pi-hole/#quick-start">Pihole Docker</a>
> #### <a url="https://github.com/chaifeng/ufw-docker#install">UFW + Docker</a>
> #### <a url="https://wiki.archlinux.org/title/ClamAV">Setup ClamAV</a>
> #### <a url="https://unix.stackexchange.com/questions/339840/how-to-start-and-use-ssh-agent-as-systemd-service">SSH-Agent service</a>
> #### <a url="https://www.togaware.com/linux/survivor/nextcloud.html">General software setup guides</a>
> #### <a url="https://wiki.archlinux.org/title/Davfs2">WebDav mounts</a>
