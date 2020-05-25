# TIL

A list of little helpers I came across over the years

<!-- ./gh-md-toc --insert --no-backup README.md -->
## Overview

<!--ts-->
   * [TIL](#til)
      * [Overview](#overview)
      * [Browsers](#browsers)
         * [Firefox](#firefox)
            * [Change the scroll speed](#change-the-scroll-speed)
            * [Speedup the browser loading time](#speedup-the-browser-loading-time)
      * [Cloud](#cloud)
         * [Connect to DB behind a Bastian host](#connect-to-db-behind-a-bastian-host)
      * [Crypto](#crypto)
         * [Extracting hashes with CAIN format](#extracting-hashes-with-cain-format)
      * [Docker](#docker)
         * [Add docker to user group](#add-docker-to-user-group)
         * [Jump into container](#jump-into-container)
      * [Git](#git)
         * [Basic commands](#basic-commands)
         * [Fixing and rebasing](#fixing-and-rebasing)
         * [Search code line in commits](#search-code-line-in-commits)
         * [Configuration](#configuration)
         * [Retrieve author of a branch (kinda)](#retrieve-author-of-a-branch-kinda)
         * [Change the last commit message](#change-the-last-commit-message)
         * [Revert a PR merge](#revert-a-pr-merge)
      * [Linux](#linux)
         * [Boot time](#boot-time)
         * [Clipboard](#clipboard)
         * [Fix crazy shell](#fix-crazy-shell)
         * [Copy ISO to USB](#copy-iso-to-usb)
         * [Set Python3.X as the default version](#set-python3x-as-the-default-version)
         * [Networking](#networking)
            * [IP tables](#ip-tables)
               * [Port redirection](#port-redirection)
            * [Permanent static IP](#permanent-static-ip)
            * [Temporary static IP](#temporary-static-ip)
         * [System health check](#system-health-check)
            * [Lynis](#lynis)
         * [User management](#user-management)
            * [Group management](#group-management)
            * [User management](#user-management-1)
         * [Verify shell scripts](#verify-shell-scripts)
         * [Watch dconf changes](#watch-dconf-changes)
      * [Tools](#tools)
         * [Curl](#curl)
            * [Get own IP](#get-own-ip)
         * [Figlet](#figlet)
         * [GPG](#gpg)
            * [Seahorse](#seahorse)
            * [Resolve missing keys](#resolve-missing-keys)
         * [Grep](#grep)
            * [Search for email addresses](#search-for-email-addresses)
            * [Search for IP addresses](#search-for-ip-addresses)
            * [Exclusive grep](#exclusive-grep)
            * [Show lines before or/and after](#show-lines-before-orand-after)
         * [Nmap](#nmap)
            * [Host discovery (Ping scan)](#host-discovery-ping-scan)
            * [Port discovery scan](#port-discovery-scan)
            * [Scanning for ports](#scanning-for-ports)
            * [TCP SYN 1k ports](#tcp-syn-1k-ports)
            * [Scan for UDP port 161 (SNMP)](#scan-for-udp-port-161-snmp)
         * [PCAP](#pcap)
            * [Analyze dump](#analyze-dump)
         * [SSH](#ssh)
            * [Enable SSH port to be open](#enable-ssh-port-to-be-open)
            * [Session cache](#session-cache)
               * [Add key to cache](#add-key-to-cache)
               * [Delete session cache](#delete-session-cache)
         * [Wget](#wget)
            * [Download file](#download-file)
               * [Download robots.txt](#download-robotstxt)
            * [Download files](#download-files)
            * [Download using username/password](#download-using-usernamepassword)
      * [(De)Coding things](#decoding-things)
         * [Search for Base64](#search-for-base64)
         * [URL decoding](#url-decoding)
      * [File manipulation](#file-manipulation)
         * [Remove duplicate lines](#remove-duplicate-lines)
         * [Split file into smaller files](#split-file-into-smaller-files)
         * [Remove first line](#remove-first-line)
         * [Intersection](#intersection)
         * [Find and replace](#find-and-replace)
         * [Sort](#sort)
         * [Grouping](#grouping)
      * [Image manipulation](#image-manipulation)
         * [Padding](#padding)
      * [String manipulation](#string-manipulation)
         * [Trim a string (both sides)](#trim-a-string-both-sides)
         * [Split a string at a delimiter](#split-a-string-at-a-delimiter)
         * [Remove quotes in a string](#remove-quotes-in-a-string)
      * [Java](#java)
         * [Maven](#maven)
            * [Start docker containers specified in POM file](#start-docker-containers-specified-in-pom-file)



<!--te-->


## Browsers

### Firefox

#### Change the scroll speed
To change the scroll amount of a single mouse scroll, open `about:config` and set the following variable to a desired value (default should be 5)
```
mousewheel.min_line_scroll_amount -> 60
```

#### Speedup the browser loading time
Stolen from https://askubuntu.com/questions/326281/firefox-use-lot-of-cpu

- First check if HW accelertation is available
```
sudo apt install mesa-utils
glxinfo | grep "direct rendering"
```

If enabled, open `about:config` and set the following settings
```
layers.acceleration.force-enabled -> True

network.http.pipelining -> True
network.http.pipelining.aggressive -> True
Network.http.pipelining.maxrequests -> 16
Network.http.pipelining.ssl -> True
```



## Cloud

### Connect to DB behind a Bastian host
If a DB is hosted in its own VPC behind a bastian host, it's usually not possible to connect to it directly from a local device. 
The common process is to connect to the bastian host via SSH and then access the DB from there. 
However, one can also create an SSH tunnel from a local device to the DB via the bastian host.  

Run the following in terminal 1
```
ssh ec2-user@<IP> -i <key> -L 3307:<RDS_URL>:3306 -N
```
In case of the following error
```
Received disconnect from 13.54.11.39 port 22:2: Too many authentication failures
Disconnected from 13.54.11.39 port 22
```
use the additional flag
```
-o IdentitiesOnly=yes
```

In a second terminal execute
```
mysql --host=localhost --port=3307 --ssl-ca=<rds_crt> --enable-cleartext-plugin --user=<user> --password=<TOKEN> --verbose --protocol=TCP
```



## Crypto

### Extracting hashes with CAIN format
When having a file with password hashes in CAIN format, they can be extracted with
```
cat hashes.txt | awk -F "\"*\t\"*" '{ if ($4) {print $1 "\t" $4} }'
```

## Docker

### Add docker to user group 
After installing `docker` on a Linux machine, it needs `sudo` to run.  
To be able to execute `docker` without the `sudo` command, add it to the user group with
```
sudo usermod -aG docker ${USER}
su - ${USER} # to enable it immediately (only in the current terminal)
```

### Jump into container
Connect a shell to a running docker container
```
docker exec -it <container> /bin/bash
```



## Git

### Basic commands
A list of the most basic git commands

| Command | Description |
| ------- | ----------- |
| `git init` | Initialize a local Git repository |
| `git clone <ME>` | Create a local copy of a remote repository |
| `git status` | Check status |
| `git add [file-name.txt]` | Add a file to the staging area |
| `git add -A` | Add all new and changed files to the staging area |
| `git commit -m "[commit message]"` | Commit changes |
| `git rm -r [file-name.txt]` | Remove a file (or folder) |
| ------- | ----------- |
| `git branch` | List branches (the asterisk denotes the current branch) |
| `git branch -a` | List all branches (local and remote) |
| `git branch [branch name]` | Create a new branch |
| `git branch -d [branch name]` | Delete a branch |
| `git push origin --delete [branch name]` | Delete a remote branch |
| `git checkout -b [branch name]` | Create a new branch and switch to it |
| `git checkout -b [branch name] origin/[branch name]` | Clone a remote branch and switch to it |
| `git branch -m [old branch name] [new branch name]` | Rename a local branch |
| `git checkout [branch name]` | Switch to a branch |
| `git checkout -` | Switch to the branch last checked out |
| `git checkout -- [file-name.txt]` | Discard changes to a file |
| `git merge [branch name]` | Merge a branch into the active branch |
| `git merge [source branch] [target branch]` | Merge a branch into a target branch |
| `git stash` | Stash changes in a dirty working directory |
| `git stash clear` | Remove all stashed entries |
| ------- | ----------- |
| `git push origin [branch name]` | Push a branch to your remote repository |
| `git push -u origin [branch name]` | Push changes to remote repository (and remember the branch) |
| `git push` | Push changes to remote repository (remembered branch) |
| `git push origin --delete [branch name]` | Delete a remote branch |
| `git pull` | Update local repository to the newest commit |
| `git pull origin [branch name]` | Pull changes from remote repository |
| `git remote add origin ssh://git@github.com/[username]/[repository-name].git` | Add a remote repository |
| `git remote set-url origin ssh://git@github.com/[username]/[repository-name].git` | Set a repository's origin branch to SSH |
| ------- | ----------- |
| `git log` | View changes |
| `git log --summary` | View changes (detailed) |
| `git log --oneline` | View changes (briefly) |
| `git diff [source branch] [target branch]` | Preview changes before merging |
| ------- | ----------- |
| `git rebase --interactive origin/master` | Rebasing |

### Fixing and rebasing
If something gets commited that contains bugs that can be fixed without creating a new commit on top of it, it is useful to squash the fix into the relevant commit.  
First lets look at the history log to see which commit to apply the fix to

```
git log --oneline master..
```

After finding what we were looking for, lets fix the commit
```
git commit --fixup <commit> <file1> [<file2>]
```

After all fixes have been applied, rebase things
```
git rebase --interactive --autosquash origin/master
```
or rebasing just the branch rather then on top of master
```
git rebase --interactive --autosquash $(git rev-list master.. | tail -1)^
```

### Search code line in commits
Search for a commit that contains a specific code part
```
git log --oneline master.. -G <regexp>`
```

### Configuration
Set the default editor to be used on edits
```
git config --global core.editor nano
```

### Retrieve author of a branch (kinda)
The following will determine the author for a branch
```
git for-each-ref --format='%(committerdate) %09 %(authorname) %09 %(refname)' | sort -k5n -k2M -k3n -k4n | grep 'origin/<branch_name>'
```

### Change the last commit message
```
git commit --amend
```

### Revert a PR merge
To revert a merged PR, lookup the merge commit and run the following

```git revert <merge commit> [-m1]``` 

m1 specifies the parent number


## Linux 

### Boot time
Monitor boot times
```
systemd-analyze blame
systemctl disable NetworkManager-wait-online.service
```

### Clipboard
To copy an output from a command or pipe directly into the clipboard install the `xclip`(handy to setup as an alias)
```
sudo apt install xclip
cat file | xclip -selection clipboard
```

### Fix crazy shell
In case subshells get stuck in limbo, this should bring things back to reality
```
stty sane
```

### Copy ISO to USB
Copy and extract and ISO to a device (e.g. installing Linux live distro)

```
sudo dd bs=4M status=progress if=ubuntu-16.04-desktop-amd64.iso of=/dev/sdX
```

### Set Python3.X as the default version
*DON'T DO THIS*
```
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.8 1
```
this will cause issues with installed packages since they can't find stuff anymore

DO THIS
```
alias python='python3.8'
```


### Networking

#### IP tables

##### Port redirection
```
iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port <listenPort>   
```

#### Permanent static IP
To set a permanent static IP modify the interface file

```
sudo nano /etc/network/interfaces
```
and add the following to it
```
audo enp0s25
allow-hotplug enp0s25
iface enp0s25 inet static
  address X.X.X.X
  network x.x.x.x     [OPTIONAL]
  broadcast x.x.x.255 [OPTIONAL]
  netmask 255.255.255.128
  gateway x.x.x.1
```
Optionally also change the DNS
```
sudo nano /etc/resolv.conf
```
and add
```
nameserver 8.8.8.8
```

#### Temporary static IP
To set a temporary static IP address for a specific iface
```
sudo enp0s25 down
sudo ifconfig enp0s25 192.168.100.1 netmask 255.255.255.0
sudo enp0s25 up
```

### System health check

#### Lynis
Security auditing tool for Linux, macOS, and UNIX-based systems (https://github.com/CISOfy/Lynis)

After running the health check important data can be extracted from the reports

```
cat /var/log/lynis-report.dat | grep warning | sed –e ‘s/warning\[\]\=//g’
cat /var/log/lynis-report.dat | grep installed_package | sed –e ‘s/installed_package\[\]\=//g’
cat /var/log/lynis-report.dat | grep available_shell | sed –e ‘s/available_shell\[\]\=//g’
```

### User management

#### Group management
Add a user to group(s)
```
sudo usermod -aG <groupA>[,<groupB>]
```

#### User management
Add a new user AND create its user directory in a non default /home directory 
```
adduser --home /<dir> <user_name>
```

### Verify shell scripts
https://www.shellcheck.net/

### Watch `dconf` changes
```
dconf watch /
```



## Tools

### Curl

#### Get own IP
```
curl ipinfo.io/ip
```

### Figlet
Figlet genertates large characters made up of ordinary screen characters

- Download fonts from http://www.figlet.org/fonts
  ```
  wget http://www.figlet.org/fonts/banner3.flf -O /tmp/banner3.flf
  ```
  Usage
  ```
  wget http://www.jave.de/figlet/fonts/details/broadway.flf -O /tmp/broadway.flf
  mv /tmp/*.flf /usr/share/figlet
  figlet -f broadway.flf -t
  ```

### GPG

#### Seahorse
Import a new GPG key
```
gpg2 --import <key>.asc
```

#### Resolve missing keys
```    
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys <missing_key>
```

### Grep

#### Search for email addresses
```
grep -E -o "\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,6}\b"
```

#### Search for IP addresses
```
grep -o '[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}'
```

#### Exclusive grep 
Grep everything except 
```
egrep -vi "XXX" file.csv
```

#### Show lines before or/and after
Show lines of the context before or/and after the match was found
```
-A 1    -> get 1 line after 
-B 1    -> get 1 line before
-C 1    -> get 1 before and after   
```

### Nmap

#### Host discovery (Ping scan)
```
nmap -sn x.x.x.x/24
```

#### Port discovery scan
```
nmap -sT x.x.x.x
```

#### Scanning for ports
Scan for all specified ports to discover online hosts
```
nmap -n --max-hostgroup 30 --host-timeout 30m -Pn -sS -p 21,22,23,80,111,139,443,1433,3389,3306,5900,8080 -iL hosts.txt -oA output
```

#### TCP SYN 1k ports
Send a TCP SYN to the first 1000 ports with a list of open hosts
```
nmap -sS --top-ports 1000 -Pn -iL open_hosts.txt -oA tcp_1k
```

#### Scan for UDP port 161 (SNMP)
Scan a predefined host list for port SNMP port
```
nmap -n --max-hostgroup 30 --host-timeout 30m -Pn -sU -p 161 -iL host_list.txt -oA udp_snmp.txt
```

### PCAP

#### Analyze dump
Analyze an existing dump with `netsniff-ng` and apply filters (BPF syntax)
```
netsniff-ng --in input.pcap -f 'host 10.0.0.1'
```
Analyze multiple files
```
for f in *.pcap; do netsniff-ng --in $f -f 'host 10.0.0.1'; done
```
Another tool is `ngrep`

### SSH

#### Enable SSH port to be open
```
systemctl enable ssh
```

#### Session cache

##### Add key to cache
```
ssh-add <key>
```

##### Delete session cache
```
ssh-add -D
```

### Wget

#### Download file
```
wget URL -O <filepath>
```

##### Download robots.txt
```
wget -U Mozilla https:/x.com/robots.txt -q -O -
```

#### Download files

```
wget -r -np -R "index.html*" https://X.com/

-np -> do not ascend to the parent directory when querying recursively
-r -> recursive
-R -> reject files
```

#### Download using username/password
```
wget https://127.0.0.1:5000/files --no-check-certificate --user=test --ask-password -q
```



## (De)Coding things

### Search for Base64
Search for a base64 encoded string
```
strings -ael -n250
```

### URL decoding
```
echo "%22Something%20OR%20%22Not%20" | sed "s@+@ @g;s@%@\\\\x@g" | xargs -0 printf "%b"
```



## File manipulation

### Remove duplicate lines
```
awk '!seen[$0]++'
```

### Split file into smaller files
Split a large file into multiple smaller files
```
split -l <max_lines_per_file> file.txt some_prefix -d 
```

### Remove first line
Removes the first line in a file
```
tail -n +2 file.txt
```

### Intersection
Exclude everythig from file2 in file1
```
grep -f file2.txt -v -F -x file1.txt
```

### Find and replace
```
find . -type f -exec sed -i 's/find/replace/g' {} + 
```

### Sort
Simple sort of lines in a file (or output)
```
sort -n
```

Multithreaded sorting of file
```
LC_ALL=C sort --parallel=8 -u breachcompilation.txt -o breachcompilation.sorted.txt
```        

### Grouping
Grouping of lines in a file (or output)
```
sort -n | uniq -c
```



## Image manipulation

### Padding
Add padding to an image
```
convert <original_img> -resize 1024x1024 -gravity center -background "rgb(255,255,255)" -extent 1024x1024 out.jpeg
```



## String manipulation

### Trim a string (both sides)
```
awk '{$1=$1;print}'
```

### Split a string at a delimiter
Split a string by a delimiter 'x' and print the 2nd part 
```
awk -F "\"*x\"*" '{print $2}'
```

### Remove quotes in a string
```
awk '{gsub(/\"/,"")};1'
```



## Java

### Maven

#### Start docker containers specified in POM file
To start the docker containers specified with `fabric8` in the pom.xml
```
mvn docker:start
```
