tar 
	tar -cf archivefilename inputfiles
	tar -tvf show contents
	-z or -j for compression
	-x expand
md5sum, sha256sum, sha512sum  -- **sha256sum ftu.txt > sha256.txt**
	use -c to check --  **sha256sum -c sha256.txt**
cut
	**cut -d: -f1**   -d divider  -f field number
wc -- -l -w -c
tr - translate/squeeze
split
less
nl
od -- output in octal
	**od -x ftu.txt**  -- output in hex
sort 
	-r reverse -d dict -n numeric -h human-numeric 
find -- 
	`find startpoint -name "name" -exec somecmd {} \;`
	`sudo find /usr/bin -perm -u+s -exec ls -lah {} \;`

	-size can be + or - some size
	-mtime or -mmin
dd -- can write to block devices
	**dd if=oldfile of=newfile** conv=someconversion
sed  --- sed -e PATTERN FILE
	s/FIND/REPLACE/ to replace
	s/hda/sda/g  global replace
grep
	-v reverse
awk
	 ps aux | awk ' {print $4, $11}'|sort -r

ssh
	**ssh-keygen** -- create a key pair
	**ssh-copy-id -i <public_key> user@cloud_server** -- copies to remote server into authorized_keys
which -- where is executable - path, can be more than one with -a
type - gives type of command (not file)
test - tests expressions. also can give file types. 
	see also [[Bash Shell#Conditional Expressions]]
	`if [ -n "${OUTPUTDIR}" -a -d "${OUTPUTDIR}/.git" ]; then`
	`[ -f file ]`
	-e file exists
	-f file exists and is regular file
	-d exists and is directory
### Package Managers
#### Debian style
dpkg -- does not install dependencies
	-i install  -r remove  -I inspect
apt -- advanced, installs all deps
	apt-get and apt-cache are old style. Use apt.
	update, install, remove, purge
	clean, search, show
	install -f fix deps
	sources in /etc/apt/sources.list
#### Red Hat Style
rpm -- does not install dependencies
	-i install  -e erase  -U upgrade and install  -F freshen installed -h hash marks
	-qa  query all installed pkgs  -qi query info on a pkg  -ql query list what is in pkg
	-qip  give a pkg filename as input
yum -- YellowDog Updater Modified
	install   update  remove | erase
	search  whatprovides  info 
	repos: /etc/yum.repos.d/  /etc/yum.conf  or yum-config-manager
dnf -- yum for fedora
zypper -- for SUSE

## Virtualization/Hypervisors
xen
KVM -- libvirt -- virt-manager GUI
	disk provisioning
		COW - copy on write = thin provision -- qcow2
		raw -- fully provisioned
VirtualBox (Oracle)

### Containers
docker 
openshift
can use cgroups to allocate resources

Network tools
nc , ncat, nmap-ncat
	Connect to a mail server
		**nc lab2.campus 25**
			