## Logs
#### rsyslog
	`RELP` stands for _Reliable Event Logging Protocol_ and extends the functionality of the syslog protocol to provide reliable delivery of messages.
`/var/log/auth.log`
Activities related to authentication processes: logged users, `sudo` information, cron jobs, failed login attempts, etc.
`rsyslogd` gets the information from the sockets or memory buffers.

Note: A socket is a special file used to transfer information between different processes. To list all sockets on your system, you can use the command `systemctl list-sockets --all`.

`rsyslog` configuration file is `/etc/rsyslog.conf`

syslog reception on TCP/UDP port 514

`/var/log/syslog`
A centralized file for practically all of the logs captured by `rsyslogd`. Because it includes so much information, logs are distributed across other files according to the configuration supplied in `/etc/rsyslog.conf`.

`/var/log/messages`
Informative messages which are not related to the kernel but to other services. It is also the default remote client log destination in a centralized log server implementation.

`/var/run/utmp` and `/var/log/wtmp`
Successful logins. Binary files
Use `who` (or `w`) to read wtmp
who
	who is logged in now
w
	who is logged in now and what they are running
last | less
	shows all successful logins since ...?

`/var/log/btmp`
Failed login attempts, e.g. brute force attack via ssh. Binary file.
**`lastb`** to read
`last -f` to read or 
**utmpdump /var/log/btmp**


`/var/log/faillog`
Failed authentication attempts. binary
**faillog -a | less** to read

`/var/log/lastlog`
Date and time of recent user logins. binary
**lastlog | less** to read


There are also graphical tools for reading log files, for example: **`gnome-logs`** and **`KSystemLog`**.

The `**logger**` command comes in handy for shell scripting or for testing purposes. `logger` will append any message it receives to `/var/log/syslog` (or to `/var/log/messages` when logging to a remote central log server.
#### Service Logs
cups
apache2 or httpd
samba

### Rules
in /etc/rsyslog.conf
#### Examples of rules
`mail.crit                 /var/log/mail.crit`
`mail.alert                        /var/log/mail.urgent`
`*.*;cron.none;ntp.none                 /var/log/allmessages`
`mail.* @@192.168.1.88:514`
`*.=warning                        -/var/log/warnings`

#### omusrmsg
built-in module which facilitates notifying users (it sends log messages to the user terminal).
`*.emerg                        :omusrmsg:root,carol`

#### rsyslog in client-server mode
##### Server setup
	**systemctl status rsyslog**
	server config
		/etc/rsyslog.d/remote.conf
		or in regular rsyslog.conf
		uncomment either UDP or TCP reception
`Provides UDP syslog reception`
`$ModLoad imudp`
`$UDPServerRun 514`

`Provides TCP syslog reception`
`$ModLoad imtcp`
`$InputTCPServerRun 514`

**firewall-cmd --permanent --add-port 514/tcp**
**firewall-cmd --reload**
**systemctl restart rsyslog**
**netstat -nltp | grep 514**

###### Templates and Filter Conditions

By default, the client’s logs will be written to the server’s `/var/log/messages` file — together with those of the server themselves. However, we will create a _template_ and a _filter condition_ to have our client’s logs stored in clear-cut directories of their own. To do so, we will add the following to `/etc/rsyslog.conf` (or `/etc/rsyslog.d/remote.conf`):

`$template RemoteLogs,"/var/log/remotehosts/%HOSTNAME%/%$NOW%.%syslogseverity-text%.log"`
`if $FROMHOST-IP=='192.168.1.4' then ?RemoteLogs`
`& stop`

- Template directive (`$template`)
  - Template name (`RemoteLogs`)
- Template text (`"/var/log/remotehosts/%HOSTNAME%/%$NOW%.%syslogseverity-text%.log"`)
- Options (optional)
**Filter Condition**
The remaining two lines correspond to the filter condition and its associated action:
- Expression-Based Filter (`if $FROMHOST-IP=='192.168.1.4'`)  
- Action (`then ?RemoteLogs`, `& stop`)
The first line checks the IP address of the remote host sending the log and — if it equals that of our Debian client — it applies the `RemoteLogs` template. The last line (`& stop`) guarantees that messages are not being sent simultaneously to `/var/log/messages` (but only to files in the `/var/log/remotehosts` directory).

|   |   |
|---|---|
|Note|To learn more about templates, properties and rules, you can consult the manual page for `rsyslog.conf`.|
##### Client setup in `/etc/rsyslog.conf`	
**systemctl status rsyslog**
`remote host is: name/ip:port, e.g. 192.168.0.1:514, port optional`
`*.* @@remote-host:514`
**systemctl restart rsyslog**

### Log Rotation
logrotate
	adding a suffix with an integer is the common practice
	`messages.4.gz` will be deleted and lost.
	The content of `messages.1` will be moved to `messages.2.gz`.
	The content of `messages` will be moved to `messages.1` and `messages` will be empty and ready to register new log entries.
	To read messages that are 1 week old, we will consult `messages.1`
settings in **/etc/logrotate.conf**
example
	weekly
	rotate 4
#### Kernel Ring Buffer
dmesg -- prints this
Since the kernel generates several messages before `rsyslogd` becomes available on boot, a mechanism to register those messages becomes necessary. This is where the _kernel ring buffer_ comes into play. It is a fixed-size data structure and — therefore — as it grows with new messages, the oldest will disappear.

## systemd-journald
One service to take care of all logging
**systemctl status systemd-journald**
config file -- /etc/systemd/journald.conf
`journalctl` is the utility that you use to query `systemd` journal.
	**journalctl -f** like tail -f
	**journalctl -e** jump to the end
	**journalctl -n** print most recent lines, default is 10
	**journalctl -k** print dmesg
	**journalctl -p** filter by priority (severity)
		**journalctl -p err**
	**journalctl --since "19:00:00" --until "19:01:00"**
	**journalctl --since "today" --until "21:00:00"**
		To learn more about the different syntaxes for time specifications, consult the manual page `systemd.time`.
	**journalctl /usr/sbin/sshd**  filter by specific executable
	**journalctl -u** filter by unit
		To print all loaded and active units, use `systemctl list-units`; to see all installed unit files use `systemctl list-unit-files`
#### `systemd-cat`
Just like how the `logger` command is used to send messages from the command line to the system log (as we saw in the previous lesson), the `systemd-cat` command serves a similar — but more well-rounded — purpose with the system journal. It allows us to send standard input (_stdin_), output (_stdout_) and error (_stderr_) to the journal.

#### Persistent Journal Storage

As mentioned previously, you have three options when it comes to the location of the journal:
- Journaling can be turned off altogether (redirection to other facilities such as the console are still possible, though).
- Keep it in memory — which makes it volatile — and get rid of the logs with every system reboot. In this scenario, the directory `/run/log/journal` will be created and used.
- Make it persistent so that it writes logs to disk. In this case, log messages will go into the `/var/log/journal` directory.
##### How to make persistent or volatile
If `/var/log/journal/` exists, logs will be stored persistently there. Should this directory be deleted, `systemd-journald` would not recreate it but write to `/run/log/journal` instead. As soon as we create `/var/log/journal/` again and restart the daemon, persistent logging will be reestablished.

Or in /etc/systemd/journald.conf
Storage=volatile or persistent or auto or none
##### Log storage size
**journalctl --disk-usage**
`systemd` logs **default** to a maximum of 10% of the size of the filesystem where they are stored. For instance on a 1GB filesystem they will not take up more than 100MB. Once this limit is reached, old logs will start to disappear to stay near this value.
Or can be configured in /etc/systemd/journald.conf
**Vacuuming the Journal**
**journalctl --vacuum-time=1months**
**journalctl --vacuum-size=100M**

#### Retrieving Journal Data from a Rescue System
it is necessary that you mount the faulty system’s `rootfs` (`/dev/sda1`) on the rescue system’s filesystem and proceed to read the journal files like so:
root@debian:~# **journalctl -D /media/carol/faulty.system/var/log/journal/**

##### Forwarding Log Data
ForwardToSyslog=yes
ForwardToConsole
ForwardToKMsg