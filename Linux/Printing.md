#### CUPS
Most Linux desktop installations will have the CUPS packages already installed. On minimal Linux installations the CUPS packages may not be installed
**sudo apt install cups**
**sudo systemctl start cups.service**
/etc/cups/cupsd.conf
	set admin
	`Require user @SYSTEM`
	indicates that a user with administrative privileges will be required for the operation. This could be changed to `@groupname` where members of `groupname` can administer the CUPS service or individual users could be provided with a list as in `Require user carol, tim`.
	set classes -- can give diff access to diff printers
/etc/printcap  -- legacy config
`/etc/cups/printers.conf`
	This file contains each printer that is configured to be used by the CUPS system.
	No modifications to the `/etc/cups/printers.conf` file should be made at the command line while the CUPS service is running.
/etc/cups/ppd/
	setup of each printer
	can get it from manufacturer
/var/log/cups/
	log similar to httpd apache
web interface
	If the web interface is enabled, then CUPS can be managed from a browser at the default URL of `http://localhost:631`
##### Install a printer
**sudo lpadmin -p ENVY-4510 -L "office" -v socket://192.168.150.25 -m everywhere**
using -m everywhere lets CUPS use IPP
##### Query PPD files
**lpinfo --make-and-model "HP Envy 4510" -m**
This is deprecated. Use IPP

##### IPP
Future versions of CUPS have deprecated drivers and will instead focus on using IPP (_Internet Printing Protocol_) and standard file formats. The output of the previous command illustrates this with the `everywhere IPP Everywhere` printing capability. IPP can perform the same tasks that a print driver is used for. IPP, just like the CUPS web interface, utilizes network port 631 with the TCP protocol.

lpadmin
	Can be used to manage printers
	Can set access to groups, users
 **lpr report.txt**
	 send a print job
	**lpr -P FRONT-DESK report.txt**
	**lpr -P ACCOUNTING-LASERJET -o landscape -o media=A4 -o two-sided-short-edge finance-report.pdf**
	**lpr -#7 -o collate=true status-report.pdf**
lpstat
	get status. (need some options or it shows nothing)
	lpstat -p (show what printers are installed)
		-t show all status
		-o show jobs
lpoptions -d printername
	set as default
lpq -a 
	show print queue
lprm or cancel
	delete a job
**sudo lpmove ACCOUNTING-LASERJET-20 FRONT-DESK**
	move a job to another printer
##### Remove a printer
**sudo cupsreject -r "Printer to be removed" FRONT-DESK**
	remove all jobs and prevent new jobs with message
**sudo lpadmin -x FRONT-DESK**
	delete printer



