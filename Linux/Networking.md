### IP
provides host addressing
#### IPv4
	32 bits: divided into 4 x 8 bit octets

### Classes
The following table outlines the details between IP address classes.
This classification system is known as classful addressing. Classful addressing relies on rigid divisions between the network ID and host ID. These divisions always occur at the eighth, 16th or 24th bits in the address. (/24 /16 or /8 CIDR)

|Name|First octet|Number of subnets|Number of hosts|Description|
|---|---|---|---|---|
|Class A|1 to 126|126|Approximately 16.7 million|Many hosts per network.|
|Class B|128 to 191|16,384|65,536|Many hosts per network.|
|Class C|192 to 223|Approximately 2.1 million|254|Many networks with fewer hosts per network.|
|Class D|224 to 239|n/a|n/a|Multicasting.|
|Class E|240 to 254|n/a|n/a|Experimental.|

Class D addresses are for a specific type of communication known as multicasting, which lets a host transmit thousands of data streams to other devices. Class E also isn't used with standard IPv4 networking. Instead, IP address researchers use Class E addresses for experimental and developmental purposes. 

#### Classless Addressing (CIDR)
Classless addressing, on the other hand, lets network administrators define network IDs at any point for more efficient use of the available addresses. For example, to satisfy a network with 2,000 host IDs, administrators can use a subnet mask of 255.255.248.0/21 to provide 2,048 total host IDs. The extra number at the end of the subnet mask is called a Classless Inter-Domain Routing ([CIDR](https://www.techtarget.com/searchnetworking/definition/CIDR)) notation, which specifies a number of bits in the subnet mask. This method lets admins add more IP addresses without using a different class.

### Subnets

Network administrators typically create subnets for the following reasons:
- **Security.** Subnets isolate traffic for privacy.
- **Performance.** Subnets manage traffic to reduce network congestion.

#### Reserved Private Addresses
Classes and the private addresses within each class

| Class | First Octet | Range of Class                  | Private IPs                       | Private CIDR Notation |
| ----- | ----------- | ------------------------------- | --------------------------------- | --------------------- |
| A     | 1-126       | `1.0.0.0` – `126.255.255.255`   | `10.0.0.0` – `10.255.255.255`     | 10.0.0.0/8            |
| B     | 128-191     | `128.0.0.0` – `191.255.255.255` | `172.16.0.0` – `172.31.255.255`   | 172.16.0.0/12 (16-31) |
| C     | 192-223     | `192.0.0.0` – `223.255.255.255` | `192.168.0.0` – `192.168.255.255` | 192.168.0.0/16        |
|       |             |                                 |                                   |                       |
### Netmask

Network devices use the subnet mask to determine which part of a host's IP address is the network ID and which part is the host ID. Essentially, subnets are an area of the network, while subnet masks help devices determine the network area to which they belong.

|Decimal|Binary|CIDR|
|---|---|---|
|`255.0.0.0`|`11111111.00000000.00000000.00000000`|`8`|
|`255.255.0.0`|`11111111.11111111.00000000.00000000`|`16`|
|`255.255.255.0`|`11111111.11111111.11111111.00000000`|`24`|
CIDR is how many bits are for the network. That is, how many are masked.
The remaining bits are for the hosts.
#### Examples
CIDR 24 
192.168.8.12 / 255.255.255.0 / 24
Range
`192.168.8.0` - `192.168.8.255`
Network Address 
`192.168.8.0`
Broadcast Address
`192.168.8.255`
Hosts
`192.168.8.1` - `192.168.8.254`

CIDR 25 -- subdivide 24 into 2 subnets
  
11111111.11111111.11111111.10000000 = 255.255.255.128 = 25
(there are 25 ones in the network above) (7 zeros give room for 128. 0 is network 127 is bc. leaves 126 hosts)
We have then the following subnets: (first address is network address, broadcast is last)
192.168.8.0   - 192.168.8.127
192.168.8.128 - 192.168.8.255

#### Routes
`route`  (deprecated in favor of ip)
or 
`ip route`

 machines that are within the same logical network/subnet can communicate directly via the IP protocol
 For machines in diff networks, need a router or gateway
To enable this, each host on the network must have configured what is called the _default route_. The default route indicates the IP to which all packets whose destination is an IP that is **not** part of the host’s logical network must be sent.
Example
the default route for machines on the `192.168.10.0/24` network will be the IP `192.168.10.1`
The default route is also used so that machines on the private network (LAN) have access to the Internet (WAN), through a router.

The network portion of an address is used by an IPv4 or IPv6 machines to lookup which interface a packet should be sent out on in its routing table. When an IPv4 or IPv6 host with routing enabled receives a packet that is not for the host itself, it attempts to match the network portion of the destination to a network in the routing table. If a matching entry is found, it sends the packet to the destination specified in the routing table.
If no entries are found and a default route is configured, it is sent to the default route. If no entry is found and no default route are configured, the packet is discarded.

#### DHCP
If the client system can't reach the DHCP server, it doesn't receive an IP address configuration. It assigns itself a default configuration with Automatic Private IP Addressing ([APIPA](https://www.techtarget.com/whatis/definition/Automatic-Private-IP-Addressing-APIPA)). APIPA addresses begin with 169.254. Network administrators should investigate why the client can't reach the DHCP server if this occurs. 

##### NAT
The NAT (_Network Address Translation_) feature allows hosts on an internal network, which uses private IPs, to have access to the Internet as if they were directly connected to it, with the Public IP used on the gateway.

## TCP and UDP
Transport protocols
Use 16-bit port numbers
The services (destination) use ports 1 to 1023, which are called _privileged ports_ because they have root access to the system. 
The origin of the connection will use the range of ports from 1024 to 65,535, called _non-privileged ports_, or socket ports.
On a Linux system, standard service ports are listed in the `/etc/services` file.
##### TCP
Connection, flow control is guaranteed

##### UDP
Connection but with no flow control

##### ICMP
Internet Control Message Protocol
Ping
traceroute

### IPv6

Each IPv6 address has 128 bits, divided into 8 groups of 16 bits, represented by hexadecimal values.
For example:
	`2001:0db8:85a3:08d3:1319:8a2e:0370:7344`
	`2001:0db8:85a3:0:0:0:0:7344`
	`2001:0db8:85a3::1319:0:7344`
	`:: can appear only once`
	`[2001:0db8:85a3:08d3:1319:8a2e:0370:7344`]:443
3 types
	unicast
	multicast
	anycast
	broadcast is not a type. Send to ff02::1 to achieve broadcast. reaching all hosts on the local network.

### Network Configuration
#### Network Interface
There is always loopback. the **lo** interface
other interfaces assigned to the network hardware found by the system
created by the kernel on boot
provide a route through which local data can be sent and remote data can be received
ip link
	shows minimal link info
ip addr 
	shows link and addr
nmcli device show
	gives full itemized details
##### Interface Names
Legacy convention - problematic because the names could get switched 
	eth0
	wlan0
New standard
	prefixes: en, ib, sl, wl, ww
lspci -- lists devices

#### Interface Management
ifconfig
	old method
	install net-tools to install
	-a show all interfaces
	**ifconfig eth2 192.168.50.50 netmask 255.255.255.0**
	**ifconfig enp0s8 add 2001:db8::10/64**
	need add keyword for IPv6
	**ifconfig enp0s8 up**
	**ifconfig enp0s3 mtu 1500**
ip
	new method
	ip addr
	**ip addr add 192.168.5.5/24 dev enp0s8**
	**ip addr add 2001:db8::10/64 dev enp0s8**
	**ip link set dev enp0s8 down**
	**ip link set enp0s8 mtu 2000**
	
/etc/network/interfaces file
	standard
	example
		`auto lo`
		`iface lo inet loopback`

		auto enp3s5
		iface enp3s5 inet dhcp
Static IP
	In networks without a DHCP server, the `static` method could be used instead and the IP settings provided manually in `/etc/network/interfaces`. For example:
	Example
		iface enp3s5 inet static
		    address 192.168.1.2/24
		    gateway 192.168.1.1
Interfaces using the `static` method do not need a corresponding `auto` directive, as they are brought up whenever the network hardware is detected.

If the same interface has more than one `iface` entry, then all of the configured addresses and options will be applied when bringing up that interface. This is useful to configure both IPv4 and IPv6 addresses on the same interface, as well as to configure multiple addresses of the same type on a single interface.

/etc/sysconfig/network-scripts/ directory
	used by Centos
ifup and ifdown
	work on /etc/network/interfaces

Note: 
	Network configuration methods used by `ifup` and `ifdown` are not standardized throughout all Linux distributions. CentOS, for example, keeps the interface settings in individual files in the `/etc/sysconfig/network-scripts/` directory and the configuration format used in them is slightly different from the format used in `/etc/network/interfaces`.

#### Route Configuration
netstat -r
	show routing table
ip route
	with no options, show table
	ip route add default via 192.168.1.1
	ip route save > /root/routes/route_backup
	**ip route restore < /root/routes/route_backup**
	
route
	with no options, show table
	route add -net 192.56.76.0 netmask 255.255.255.0 dev eth0
	**route add default gw 192.168.1.1**
route -6
ip -6 route
Flags
	U: up
	G: gateway
	!: not used

### DNS
/etc/resolv.conf
	Example
		nameserver 8.8.4.4
		nameserver 8.8.8.8
		domain mydomain.org
		search mydomain.net mydomain.com
	domain: set the domain as the local domain. queries for names within this domain will be allowed to use short names relative to the local domain
	search: accepts a list of domains to try when a short name is provided. By default, it contains only the local domain name.
	Keep in mind that `domain` and `search` are mutually exclusive. If both are present, the last instance in the file is used.
##### _Name Service Switch_ configuration file
/etc/nsswitch.conf
	defines how to resolve names
	The far left column is the type of name database. The rest of the columns are the methods the resolution functions should use to lookup a name. The methods are followed by the functions from left to right. Columns with `[]` are used to provide some limited conditional logic to the column immediately to the left of it.
	
	The _hosts_ database keeps track of the mapping between host names and host numbers. The line inside `/etc/nsswitch.conf` beginning with `hosts` defines the services accountable for providing the associations for it:
	
	 `hosts:          dns [!UNAVAIL=return] files`
	 `networks:       nis [NOTFOUND=return] files`
	 `services:       nis [NOTFOUND=return] files`

	`hosts: files dns`

In this example entry, `files` and `dns` are the service names that specify how the lookup process for host names will work. First, the system will look for matches in local files, then it will ask the DNS service for matches.

#### systemd-resolved
DNS
It provides mDNS, DNS, and LLMNR. When it is running, it listens for DNS requests on `127.0.0.53`. It does _not_ provide a full fledged DNS server. Any DNS requests it receives are looked up by querying servers configured in `/etc/systemd/resolv.conf` or `/etc/resolv.conf`. If you wish to use this, use `resolve` for `hosts` in `/etc/nsswitch.conf`. Keep in mind that the OS package that has the `systemd-resolved` library may not be installed by default.
##### Hosts file
The most common use for `/etc/hosts` is for hosts and addresses where DNS is not possible or you want to override DNS with a local definition.
`/etc/hosts`, a simple text file that associates IP addresses with hostnames, one line per IP address
	`127.0.0.1 localhost`
	`192.168.1.10 foo.mydomain.org foo`
	`::1 localhost ip6-localhost ip6-loopback`
- Host names may contain only alphanumeric characters, minus signs and periods.
- Host names must begin with an alphabetic character and end with an alphanumeric character.
##### Hostname
`/etc/hostname` 
	if the file exists, the operating system will use the contents of the first line as its local name, thereafter simply called the _hostname_.
	can be edited directly
**hostnamectl set-hostname storage**
	this sets the hostname and changes the hostname file
	3 types of hostname
		--static (regular name default)
		--pretty
		--transient
**hostnamectl status**
	gives details
	can be run without status -- default action
hostname (legacy command to show or set hostname)
	
### NetworkManager
arranges route changes, IP address fetching and updates to the local list of DNS servers, if necessary. 
When both wired and wireless connections are available, NetworkManager prioritizes the wired connection by default. 
NetworkManager will try to keep at least one connection active all the time, whenever it is possible.
the NetworkManager daemon controls the network interfaces **not mentioned** in the `/etc/network/interfaces` file.
Wireless setup: handled and stored for next boot
Client applications
	Desktop: _nm-tray_, _network-manager-gnome_, _nm-applet_ or _plasma-nm_
	Command line: nmcli, nmtui
nmcli
	general
		Connectivity: full or portal
		WIFI
		WWAN: cellular
		HW: device vs system. May be turned off to save power
	device
		**nmcli device wifi connect Hypnotoad**
		**nmcli device wifi connect Hypnotoad password MyPassword**
	connection
		**nmcli connection down Hypnotoad**
	radio
		**nmcli radio wifi off**
### systemd-networkd\
/etc/systemd/network
	highest priority
	name the files with numbers for ordering
/run/systemd/network
	next prio
/lib/systemd/network
	lowest prio
config files
	.netdev
		devices
	.link
		interfaces
	.network
		setup network addresses and routes
			`[Match]`
			`MACAddress=00:16:3e:8d:2b:5b`
			
			[Network]
			Address=192.168.0.100/24
			Gateway=192.168.0.1
			or
			DHCP=yes
wireless setup
	**wpa_passphrase MyWifi > /etc/wpa_supplicant/wpa_supplicant-wlo1.conf**
	systemctl start wpa_supplicant@wlo1.service
	Finally, a `.network` file matching the `wlo1` interface must be present in `/etc/systemd/network/`, as systemd-networkd will use it to configure the interface as soon as WPA supplicant finishes the association with the access point.
	
	
### Mail
MTAs
	sendmail, Postfix, qmail and Exim
	all MTAs have a 'sendmail' command
Port 25
Need to set up SMTP IP address and port. 
Take care if the MTA will be open to the Internet
By default, MTAs will only accept messages to local recipients.
SMTP connection
	Connect to a mail server
		**nc lab2.campus 25**
		**HELO lab1.campus**
		250 lab2.campus Hello lab1.campus [10.0.3.134], pleased to meet you
		**MAIL FROM: emma@lab1.campus**
		250 2.1.0 emma@lab1.campus... Sender ok
		**RCPT TO: dave@lab2.campus**
		250 2.1.5 dave@lab2.campus... Recipient ok
		**DATA**
		354 Enter mail, end with "." on a line by itself
		**Subject: Recipient MTA Test**
		
		**Hi Dave, this is a test for your MTA.**
		**.**
		250 2.0.0 xAG0G7Y0000595 Message accepted for delivery
		**QUIT**
		221 2.0.0 lab2.campus closing connection

`[maintuser@ip-10-0-16-146 ~]$ nc localhost 25`
`220 ip-10-0-16-146.ec2.internal ESMTP Postfix`
`HELO localhost`
`250 ip-10-0-16-146.ec2.internal`
`mail from: maintuser`
`250 2.1.0 Ok`
`Mait To: miantuser^[[D^[[D^[[D`
`502 5.5.2 Error: command not recognized`
`Mail to: root`
`503 5.5.1 Error: nested MAIL command`
`Rcpt to: root`
`250 2.1.5 Ok`
`data`
`354 End data with <CR><LF>.<CR><LF>`
`subject: testing 123`
`test test`
`.`
`250 2.0.0 Ok: queued as 844FB1FFB`
`quit`
`221 2.0.0 Bye`


#### Mailbox
	**cat /var/spool/mail/dave**
		`From maintuser@ip-10-0-16-146.ec2.internal  Thu Aug  1 08:05:18 2024`
		`Return-Path: <maintuser@ip-10-0-16-146.ec2.internal>`
		`X-Original-To: root`
		`Delivered-To: root@ip-10-0-16-146.ec2.internal`
		`Received: from localhost (localhost [127.0.0.1])`
		        `by ip-10-0-16-146.ec2.internal (Postfix) with SMTP id 844FB1FFB`
		        `for <root>; Thu,  1 Aug 2024 08:03:32 +0000 (UTC)`
		`subject: testing 123`
		`Message-Id: <20240801080416.844FB1FFB@ip-10-0-16-146.ec2.internal>`
		`Date: Thu,  1 Aug 2024 08:03:32 +0000 (UTC)`
		`From: maintuser@ip-10-0-16-146.ec2.internal`
		
		`test test`

		
Sendmail will store incoming messages in a file named after the corresponding inbox owner, for example `/var/spool/mail/dave`. Other MTAs, like Postfix, may store the incoming email messages in locations like `/var/mail/dave`, but the file content is the same.

mailq
	check the queue
	The default location for the outbox queue is `/var/spool/mqueue/`
#### The `mail` Command and Mail User Agents (MUA)
Although possible, it is not convenient to read the mailbox file directly. It is recommended to use a email client program instead (e.g. Thunderbird, Evolution, or KMail)
Desktop apps or web apps
`mail` command is provided by the _mailx_ package or **mailutils**
If an email address is provided as an argument to the command `mail`, it will enter the send mode, otherwise it will enter the normal (read) mode.
##### Command line send
	`mail -s "Maintenance fail" henry@lab3.campus <<<"The maintenance script failed at date"`
##### Attach a file and pipe the body
uname -a | mail -a logs.tar.gz emma@lab1.campus
#### email aliases
/etc/aliases
After modifying the `/etc/aliases` file, the command `newaliases` should be executed to update the MTA’s aliases database and make the changes effective.
Aliases are defined one per line, in the format `<alias>: <destination>`. In addition to the ordinary local mailboxes, indicated by the corresponding username, other destination types are available:
- A full path (starting with `/`) to a file. Messages sent to the corresponding alias will be appended to the file.
- A command to process the message. The `<destination>` must start with a pipe character and, if the command contains special characters (like blank spaces), it must be enclosed in double quotes. For example, the alias `subscribe: |subscribe.sh` in `lab2.campus` will forward all messages sent to `subscribe@lab2.campus` to the standard input of the command `subscribe.sh`. If sendmail is running in _restricted shell mode_, the allowed commands — or the links to them — should be in `/etc/smrsh/`.
- An include file. A single alias can have multiple destinations (separated by commas), so it may be more practical to keep them in an external file. The `:include:` keyword must indicate the file path, as in `:include:/var/local/destinations`
- An external address. Aliases can also forward messages to external email addresses.
- Another alias.

#### .forward file
Allows users to forward all mails to other addresses
the `.forward` file must be writable by its owner only and it is not necessary to execute the `newaliases` command after modifying it.
$ **cat ~/.forward**
emma@lab1.campus

## Networking tools

tcpdump or wireshark
	capture network packets
hex viewers
ip
	ip addr help
ping and ping6
	some reasons ping fails:
	 - The remote host is down.
	   - A router ACL is blocking your ping.
	- The remote host’s firewall is blocking your ping.    
	- You may be using an incorrect host name or address.    
	- Your name resolution is returning an incorrect address.    
	- Your machine’s network configuration is incorrect.    
	- Your machine’s firewall is blocking it.    
	- The remote host’s network configuration is incorrect.    
	- Your machine’s interface(s) are disconnected.    
	- The remote machine’s interface(s) are disconnected.    
	- A network component such as a switch, cable, or router between your machine and the remote’s is no longer functioning.

traceroute (need to install)
tracepath or tracepath6 (MTU)
	on the last line it outputs the smallest MTU on the entire link
nc (known as netcat or ncat)
	makes a connection and sends data
	In connect mode, Ncat works as a client. In listen
       mode it is a server.
	-e execute a command  All input from the remote client will be sent to the application and
           responses sent back to the remote client over the socket, thus making your command-line application
           interactive over a socket. Combined with --keep-open, Ncat will handle multiple simultaneous
           connections to your specified port/application like inetd. 
       -ssl can do SSL
	**nc -l 1234** 
		start a listener on port 1234
##### netstat and ss
	use sudo to see all
	netstat -nap
		all including UNIX sockets, process name
	netstat -tulnp
		only TCP and UDP, listeners, process
	ss -tulnp
		TCP, UDP, listen, port, queue packets
	sudo netstat -lnp |grep :22

#### DNS tools
getent
	The `getent` utility is used to display entries from name service databases. It can retrieve records from any source configurable by `/etc/nsswitch.conf`.
	To use `getent`, follow the command with the type of name you wish to resolve and optionally a specific entry to lookup.
		`getent hosts google.com`
			2607:f8b0:4004:c09::8a google.com
			2607:f8b0:4004:c09::65 google.com
			2607:f8b0:4004:c09::66 google.com
			2607:f8b0:4004:c09::71 google.com
	
host
	if `host` is given a **name**, it returns the A, AAAA, and MX record sets. If given an IPv4 or IPv6 address, it outputs the PTR record if one is available
	 `host google.com`
	google.com has address 142.251.179.113
	google.com has IPv6 address 2607:f8b0:4004:c1f::64
	google.com mail is handled by 10 smtp.google.com.
	if given an **address**...
	 `host 142.251.179.113`
	113.179.251.142.in-addr.arpa domain name pointer pd-in-f113.1e100.net.
	`host pd-in-f113.1e100.net`
	pd-in-f113.1e100.net has address 142.251.179.113
	pd-in-f113.1e100.net has IPv6 address 2607:f8b0:4004:c1f::71
	**-t by type**
	host -t NS google.com
		google.com name server ns3.google.com.
		google.com name server ns4.google.com.

	Resolve using a diff DNS server
		host -t MX lpi.org dns1.easydns.com
#### The `dig` Command
By default, `dig` queries for A records.

### Superdaemon to Listen for Incoming Network Connections
	inetd and xinetd
	systemd.socket
##### Example: use xinetd to set up sshd to start automatically
With sshd running, check that it is listening on port 22
	**lsof -i :22**
**sudo systemctl stop sshd.service**
systemctl disable sshd.service
create the xinetd configuration file `/etc/xinetd.d/ssh`
Note: You can find some example config files in /etc/xinet.d/
But, live config files must be in /etc/xinetd.d/
`service ssh`
`{`
	`disable		= no`
	`socket_type	= stream`
	`protocol	= tcp`
	`wait		= no`
	`user		= root`
	`server		= /usr/sbin/sshd`
	`server_args 	= -i`
	`flags		= IPv4`
	`interface	= 192.168.178.1`
`}`
In the service line, you can put a port number or a service name to be looked up in /etc/services
`socket_type`
	You can choose `stream` for TCP sockets or `dgram` for UDP sockets and more.
**sudo systemctl restart xinetd.service**
Now check what is listening on port 22
	**lsof -i :22**
	it is xinetd, not sshd

##### Example 2: User systemd socket
Make sure xinetd and sshd are not running
start the ssh socket
	**sudo systemctl start ssh.socket**
	or
	systemctl start sshd.socket
check what is listening on 22
	**sudo lsof -i :22 -P**


