## Users and Groups
useradd
	-d custom home dir
	-f time limit to change password
	-m create a home dir
	-s specify a shell
		shell could be ** /sbin/nologin** to block logging in to the account
passwd
usermod -- similar to useradd
userdel
	-r also delete the home dir
id
groups -- shows groups a user belongs to
groupadd
groupmod
groupdel
gpasswd
newgrp
chage -- change max age of password -- expire age of account
#### The `/etc/login.defs` File
Controls defaults for users and groups
#### The `passwd` Command
has SUID bit -- therefore user can change his own password without sudo
can set max lifetime, disable/lock account,

#### /etc/skel 
default structure of home dir. 
Note that there are hidden files in there

#### `/etc/passwd`

A file of seven colon-delimited fields containing basic information about users.

#### `/etc/group`

A file of four colon-delimited fields containing basic information about groups.

#### `/etc/shadow`

A file of nine colon-delimited fields containing encrypted user passwords.

#### `/etc/gshadow`

A file of four colon-delimited fields file containing encrypted group passwords.

## cron
Each line in a user crontab contains six fields separated by a space:

- The minute of the hour (0-59).
- The hour of the day (0-23).
- The day of the month (1-31).
- The month of the year (1-12).
- The day of the week (0-7 with Sunday=0 or Sunday=7).
- The command to run.

00 02 * * * find /var/log/secure* -mtime +7 -type f -exec rm {} \; > /dev/null 2>&1
30 02 * * * find /var/log/messages* -mtime +7 -type f -exec rm {} \; > /dev/null 2>&1

#### User crontabs 
	in /var/spool/cron

#### System crontabs
`/etc/crontab` and all files in the `/etc/cron.d` directory are system crontabs.
has additional mandatory field that specifies which user will run the cron job. Therefore, each line in a system crontab contains seven fields separated by a space.

Unless output is redirected to a file as in the example above (or the `MAILTO` variable is set to a blank value), all output from a cron job will be sent to the user via e-mail. A common practice is to redirect standard output to `/dev/null` (or to a file for later review if necessary) and to not redirect standard error. This way the user will be notified immediately by e-mail of any errors.

#### Access to cron
`/etc/cron.allow` and `/etc/cron.deny` files

#### systemd timer
`/etc/systemd/system/foobar.timer` unit file.

`[Unit]`
`Description=Run the foobar service`

`[Timer]`
`OnCalendar=Mon *-*-1..7 05:30:00`
`Persistent=true`

`[Install]`
`WantedBy=timers.target`

 **systemctl enable foobar.timer**
 **systemctl start foobar.timer**
 systemctl list-timers

### Schedule Jobs with at
**at now +5 minutes**
warning: commands will be executed using /bin/sh
at> **date**
at> **Ctrl+D**

at -l or atq 
	list

#### systemd-run
**systemd-run --on-calendar='2019-10-06 11:30' ./foo.sh**

## Localization

### Time and Date
timedatectl

/etc/localtime is sym link to /usr/share/zoneinfo/ where files have detailed info about daylight time

timezone
	 cat /etc/timezone
America/Sao_Paulo

tzselect -- interactively select the timezone

#### Setting Time Using timedatectl

If NTP is unavailable, it is recommended to use `timedatectl` rather than `date` or `hwclock` to set time:
 **timedatectl set-time '2011-11-25 14:00:00'**
The process is similar to that of `date`. The user can also set time independent of date using the format HH:MM:SS.
#### Setting Timezone Using timedatectl
**timedatectl list-timezones**
**timedatectl set-timezone Africa/Cairo**
 **timedatectl set-ntp no**
	 disables NTP
#### Setting Time Zone Without timedatectl
**ln -s /usr/share/zoneinfo/Canada/Eastern /etc/localtime**
**hwclock --systohc**
	set the _hardware clock_ from the _system clock_

#### Setting Date and Time Without timedatectl
**date --set="11 Nov 2011 11:11:11"**
**date +%Y%m%d -s "20111125"**
**date +%T -s "13:11:00"**
**hwclock --systohc**

#### Using hwclock
**hwclock --set --date "4/12/2019 11:15:19"**
**hwclock --hctosys**

#### NTP
Reference clocks are atomic clocks
Stratum 1 machines are not public
Stratum 2 machines can be referenced. But best to set small number of computers connect to Stratum 2+ servers, and then have those machines provide NTP to all other machines. In this way, demands on Stratum 2 machines can be minimized.
**Insane Time**
	If the offset between system time and NTP time is greater than 17 minutes, ntpd will not update system time. Need to adjust manually to get it within 17 mins.
**Jitter**
	Jitter refers to the amount of **drift** since the last time a clock was queried.
##### NTP with systemd
timedatectl set-ntp true
**systemctl status systemd-timesyncd**
	relies on this SNTP. not ntpd.
timedatectl status
**timedatectl show-timesync --all**
_systemd-timesyncd_ is a daemon that has been added for synchronizing the system clock across the network. It implements an **SNTP** client. In contrast to NTP implementations such as [chrony](https://wiki.archlinux.org/title/Chrony "Chrony") or the NTP reference server this only implements a client side, and does not bother with the full NTP complexity, focusing only on querying time from one remote server and synchronizing the local clock to it.
/etc/systemd/timesyncd.conf
`[Time]`
`NTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org`
`FallbackNTP=0.pool.ntp.org 1.pool.ntp.org 0.fr.pool.ntp.org`

##### NTP Daemon
**systemctl status ntpd**
**systemctl enable ntpd && systemctl start ntpd**
NTP queries happen on TCP Port 123. If NTP fails, ensure that this port is open and listening.
/etc/ntp.conf
	server 0.centos.pool.ntp.org iburst
	server 1.centos.pool.ntp.org iburst
these urls are pools
**ntpdate**
	During initial setup, system time and NTP might be seriously de-synchronised. If the _offset_ between system and NTP time is greater than 17 minutes, then the NTP daemon will not make changes to system time. In this scenario manual intervention will be required. 
	Firstly, if `ntpd` is running it will be necessary to _stop_ the service. Use `systemctl stop ntpd` to do so.
	Next, use `ntpdate pool.ntp.org` to perform an initial one-time synchronisation, where `pool.ntp.org` refers to the IP address or URL of an NTP server. More than one sync may be required.
**ntpq**
`ntpq` is a utility for monitoring the status of NTP. Once the NTP daemon has been started and configured, `ntpq` can be used to check on its status:

### chrony
`chrony` is yet another way to implement NTP. It is installed by default on some Linux systems, but is available to download on all major distributions. `chronyd` is the chrony daemon, and `chronyc` is the command line interface. It may be required to start and enable `chronyd` before interacting with `chronyc`.
/etc/chrony.conf
**chronyc tracking**
**chrony ntpdata**
chronyc sources

#### NTP vs SNTP
same port UDP 123
same packet structure -- interoperable
Simple Network Time Protocol (SNTP) allows computers with less processing power to implement clock synchronization. Typically, it is used by small network devices, such as IP cameras, DVR’s, IP phones, routers and consumer devices.
### Language

**cat /etc/locale.conf**
LANG=pt_BR.UTF-8

locale -- command to display all env vars

#### Encoding
iconv  -- converts between encodings
`iconv -f ISO-8859-1 -t UTF-8 original.txt > converted.txt`
	(or `--from-code=ISO-8859-1`)  (or `--to-code=UTF-8`)
	or `-o converted.txt` or `--output converted.txt`
	Command `iconv` will replace all characters outside the target character set with a question mark.
	iconv -f UTF-8 -t ASCII//TRANSLIT -o ascii.txt readme.txt
		replaces unknown characters with a similar looking char in the target encoding

