### Files with the SUID and SGID Set
#### SUID
The SUID bit will allow the file to be executed with the privileges of the file’s owner. It is numerically represented by `4000` and symbolically represented by either `s` or `S` on the **owner**’s _execute_ permission bit.
Like passwd. It lets anyone execute like root. So users can change their own passwords
`**ls -l /usr/bin/passwd**`
`-rwsr-xr-x 1 root root 63736 jul 27  2018 /usr/bin/passwd`
The lowercase `s` in `rws` indicates the presence of the SUID on the file — together with the _execute_ permission. An uppercase `S` instead (`rwS`) would mean the underlying _execute_ permission is not set.
#### SGID
Like SUID, SGID is symbollically represented by either `s` or `S` on the **group**’s _execute_ permission bit. Numerically, it is represented by `2000`
**sudo chmod 2755 shared_directory/**
**ls -ld shared_directory**
drwxr-sr-x 2 carol carol 4096 may 30 23:55 shared_directory
#### To find
Use find -perm
Using -perm
	u+s
		exactly. Not too useful
	-u+s
		leading - sign means 'all' specified permissions. but not only those.
	/6000
		leading / means any of the specified permissions. This would include 2000 and 4000.
		Note that / replaces + for this mode. + was confusing.
**find . -perm 4000**
**find . -perm u+s**
`find /usr/bin/ -perm -2000`
or `find /usr/bin/ -perm -g+s`
**sudo find /usr/bin -perm /6000**

#### passwd
Change your password or if root change any password
Can lock and unlock accounts, force a user to change their password on the next login and delete a user’s password
passwd -S -- get status
	sudo passwd -S maintuser
	-l 
		lock password. account can still be accessed by ssh public key
	-u unlock
	-d delete the password. account will have no password
	-e expires the password. user wil need to change it on next login
usermod
	-L lock
	-U unlock
chage (change age)
	-m set minumum age. before that, you cant change it. 0= change any time
	-M set maximun age. after that, you need to change it. 99999= never expire
	-I days after expiration to lock account. 0= dont lock ever
	-E expire date

### Discovering Open Ports
need sudo to see processes owned by others or root

lsof
	list open files
	sudo lsof -i (internet files)
fuser
	file's user
	sudo fuser -v .
	sudo fuser -v /
	-n network
	**sudo fuser -vn tcp 80**
netstat and ss
	see [[Linux/Networking#netstat and ss]]
nmap
	port scanner
	can scan single or multiple hosts
	can use range 
		nmap 192.168.1.3-20
	can scan subnets
		nmap 192.168.1.0/24
	-v ping scan, DNS scan
	 nmap localhost
		`Starting Nmap 6.40 ( http://nmap.org ) at 2024-08-04 13:50 UTC`
		`Nmap scan report for localhost (127.0.0.1)`
		`Host is up (0.0012s latency).`
		`Not shown: 997 closed ports`
		`PORT    STATE SERVICE`
		`22/tcp  open  ssh`
		`25/tcp  open  smtp`
		`111/tcp open  rpcbind`
	nmap google.com
		`Nmap scan report for google.com (142.250.31.100)`
		`Host is up (0.0021s latency).`
		`Other addresses for google.com (not scanned): 142.250.31.101 142.250.31.102 142.250.31.113 142.250.31.138 142.250.31.139`
		`rDNS record for 142.250.31.100: bj-in-f100.1e100.net`
		`Not shown: 998 filtered ports`
		`PORT    STATE SERVICE`
		`80/tcp  open  http`
		`443/tcp open  https`
	nmap 10.0.16.0/24
		`Starting Nmap 6.40 ( http://nmap.org ) at 2024-08-04 13:56 UTC`
		`Nmap scan report for ip-10-0-16-146.ec2.internal (10.0.16.146)`
		`Host is up (0.00047s latency).`
		`Not shown: 998 closed ports`
		`PORT    STATE SERVICE`
		`22/tcp  open  ssh`
		`111/tcp open  rpcbind`
		`Nmap done: 256 IP addresses (1 host up) scanned in 9.18 seconds`

### Limits on Users Logins, Processes and Memory Usage

ulimit
	Hard limits can only be increased by the root user.
	regular users can decrease hard limits and increase soft limits up to the value of hard limits. 
	To make new limit values persistent across reboots, you must write them to the `/etc/security/limits.conf` file. This is also the file used by the administrator to apply restrictions on particular users.
	It is a bash builtin
	-S soft limits
	-H hard limits
	 ulimit -a
		`core file size          (blocks, -c) 0`
		`data seg size           (kbytes, -d) unlimited`
		`scheduling priority             (-e) 0`
		`file size               (blocks, -f) unlimited`
		`pending signals                 (-i) 3732`
		`max locked memory       (kbytes, -l) 64`
		`max memory size         (kbytes, -m) unlimited`
		`open files                      (-n) 1024`
		`pipe size            (512 bytes, -p) 8`
		`POSIX message queues     (bytes, -q) 819200`
		`real-time priority              (-r) 0`
		`stack size              (kbytes, -s) 8192`
		`cpu time               (seconds, -t) unlimited`
		`max user processes              (-u) 3732`
		`virtual memory          (kbytes, -v) unlimited`
		`file locks                      (-x) unlimited`
### Logged in Users
last
	`last` prints a listing of the last logged in users with the most recent information on top
	Includes all logins in the history of the machine -- so best to use a pager
	-F to get full login and logout dates
lastb
	bad logins
	-F for full dates
who
	-a all
w
	shows what they are doing
	 `w`
 `14:40:07 up 34 days, 19:14,  1 user,  load average: 0.00, 0.01, 0.05`
`USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT`
`maintuse pts/0    176.231.41.152   Thu05    7.00s  0.41s  0.66s sshd: maintuser [priv]`

#### su and sudo
/etc/sudoers
	must be edited with the 'visudo' command.
		 `User privilege specification`
		`root    ALL=(ALL:ALL) ALL`
		`Allow members of group wheel to execute any command`
		`%wheel   ALL=(ALL:ALL) ALL`
	In the Red Hat family of distributions the `wheel` group is the counterpart to the special administrative `sudo` group of Debian systems.
	Let user exec specific command
		carol   ALL=(ALL:ALL) /usr/bin/systemctl status apache2
		or
		carol   ALL=(ALL:ALL) NOPASSWD: /usr/bin/systemctl status apache2
	can create aliases
		Host_Alias SERVERS = 192.168.1.7, server1, server2
		User_Alias ADMINS = carol, %sudo, PRIVILEGED_USERS, !REGULAR_USERS
		Cmnd_Alias SERVICES = /usr/bin/systemctl *
	then use aliases in definitions
		ADMINS  SERVERS=SERVICES

### ssh
ssh user@server
	asks for password. never use password
	Remote server provides an `ECDSA key fingerprint` of its public key (using the `SHA256` hash function). Once you accept the connection, the public key of the remote server is added to file `~/.ssh/known_hosts`

ssh serena@halof ls .ssh
	run a command on the remote server
#### Key-Based Logins

You can set up your SSH client to not provide any passwords at login but use public keys instead. 
**ssh-keygen -t ecdsa**
ssh-keygen -C ike-laptop -t ecdsa -b 256
id_ecdsa
	private key
id_ecdsa.pub
	public key
The first thing you have to do is create a key pair on the client machine. To do this, you will use `ssh-keygen` with the `-t` option specifying the type of encryption you want (_Elliptic Curve Digital Signature Algorithm_ in our case).
types of encryption
	RSA, DSA, ecdsa, ed25519

ssh-agent /bin/bash
	creates a secure shell to hold your private key with passcode

### OpenSSH Server Host Keys
found in /etc/ssh on the OpenSSH server
the server uses these _host keys_ to identify itself to clients as required.

### SSH Port Tunnels
_port tunnelling_ or _port forwarding_
- It allows you to bypass firewalls to access ports on remote hosts.
- It allows access from the outside to a host on your private network.
- It provides encryption for all data exchange.
#### Local Port Tunnel
AllowTcpForwarding in sshd_config
use -L
**ssh -L 8585:www.gnu.org:80 mylocalhost**
	opens encrypted tunnel on local port 8585 on mylocalhost to remote port 80
	If you now use a web browser to go to `http://mylocalhost:8585`, you will be forwarded to `www.gnu.org`
**ssh -L 8585:www.gnu.org:80 -Nf user@remoteserver**
	similar to above but connects through remote server
	-N dont log in, just forward port
	-f run in background
#### Remote Port Tunnel
also called **reverse** port forwarding
`GatewayPorts` in sshd_config
traffic coming on a port on the remote server is forwarded to the SSH process running on your local host, and from there to the specified port on the destination server (which may also be your local machine)
use -R
**ssh -R 8585:localhost:80 -Nf user@remotehost**
anyone who establishes connection on remotehost:8585 will get to localhost:80

##### X11 Tunnel or Forwarding
X11Forwarding in sshd_config
Start ssh session with -X
Then can run firefox or clock, etc
if you start with -x, X11 forwarding is disabled

### ssh_config Options

### sshd_config Options

## GPG Gnu Privacy Guard
Implements OpenPGP standard
User generates key pair
**gpg --gen-key**
configuration directory `~/.gnupg`
~/.gnugpg/pubring.kbx
	public key ring
opengp-revocs.d
	holds revocation cert
private-keys-v1.d
	holds private keys
trustdb.gpg
	trust database
gpg --list-keys
	`pub   rsa3072 2020-07-03 [SC] [expires: 2022-07-03]`
	      `D18FA0021F644CDAF57FD0F919BBEFD16813034E`
	`uid           [ultimate] carol <carol@debian>`
	shows fingerprint
	key-id is last 8 chars in fingerprint
#### Key Distribution and Revocation
Export public key so you can distribute it
	**gpg --export carol > carol.pub.key**
	gpg --export --armor carol > carol.pub.key
		safer format for emailing
distribute using keyserver
	gpg --keyserver _keyserver-name_ --send-keys _KEY-ID_
	gpg --keyserver _keyserver-name_ --recv-keys _KEY-ID_
		import keys
revocation
	create revoc cert
		**gpg --output revocation_file.asc --gen-revoke sonya**
	revoke your keyring
		**gpg --import revocation_file.asc**
### Use GPG to Encrypt, Decrypt, Sign and Verify Files
Import your friend's key into your keyring
	**gpg --import carol.pub.key**
put message into a file
	**echo "This is the message ..." > unencrypted-message**
encrypt message
	 messages are encrypted with the message recipient’s public key and decrypted with the corresponding private key.
	**gpg --output encrypted-message --recipient carol --armor --encrypt unencrypted-message**
decrypt message
	**gpg --decrypt encrypted-message --output unencrypted-message**

sign a message
	Message signing uses the sender’s private key to sign the message, and his or her public key is used to read the signature.
	**echo "This is the message to sign ..." > message**
	**gpg --output message.sig --sign message**
verify message
	**gpg --verify message.sig**
decrypt signed message
	why does it need to be decrypted if it is signed??
	**gpg --output message --decrypt message.sig**

gpg-agent
	started on demand
	has some options
	
### Checking Services for Unnecessary Daemons
Unnecessary and unused services should be disabled. For example in case you do not need to distribute web pages there is no need to run a web server such as Apache or nginx.
##### On SyS-V-init
Lits all running services
	**sudo service --status-all**
To disable service on Debian
	**sudo update-rc.d SERVICE-NAME remove**
To disable on red hat
	**sudo chkconfig SERVICE-NAME off**

##### On systemd
List all running services
**systemctl list-units --state active --type service**
systemctl disable mongod

##### Check for listening services
**netstat -ltu**
or
**ss -ltu**

### TCP Wrappers as Sort of a Simple Firewall
Nowadays many programs do not obey TCP wrappers anymore.
Not supported on red hat
**Why is this important?**
First we check whether the SSH daemon uses the `libwrap` library which offers TCP wrappers support:
**ldd /usr/sbin/sshd | grep "libwrap"**
Now we add the following line in the file `/etc/hosts.deny`:
	sshd: ALL
Finally we configure an exception in the file `/etc/hosts.allow` for SSH connections from the local network:
	sshd: LOCAL





	

		




