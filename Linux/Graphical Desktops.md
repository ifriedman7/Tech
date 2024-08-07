X Window -- underlying system
Gtk -- Gnome libraries
	first choice in distributions like Fedora, Debian, Ubuntu, SUSE Linux Enterprise, Red Hat Enterprise Linux, CentOS, etc.
Qt -- Libraries for KDE
	default in openSUSE, Mageia, Kubuntu, etc.
Xfce
	Low resources
	modular -- can remove unneeded modules
Cinnamon
	Fork of Gnome 3
MATE
	Fork of Gnome 2
LXDE
	Low consumption
## Remote Desktops

### X Display Manager Control Protocol (XDMCP)
High bandwidth
no security
privileged -- the local display communicates with a privileged remote X display manager to execute remote procedures, so an eventual vulnerability could make it possible to execute arbitrary privileged commands on the remote machine.
#### VNC -- Virtual Network Computing
Uses Remote Frame Buffer protocol (RFB)
	events produced by the local keyboard and mouse are transmitted to the remote desktop, which in turn sends back any screen updates to be displayed locally. It is possible to run many VNC servers in the same machine, but each VNC server needs an exclusive TCP port in the network interface accepting incoming session requests. By convention, the first VNC server should use TCP port 5900, the second should use 5901, and so on.
	does not need special privileges
The `~/.vnc/xstartup` file is a shell script executed by the VNC server when it starts
Does **not provide modern encryption** and **authentication methods** natively, so it should be used in conjunction with a third-party application that provides such features. Methods involving VPN and SSH tunnels are often used to secure VNC connections.
#### RDP
The Remote Desktop Protocol (RDP) is mainly used to remotely access the desktop of a Microsoft Windows operating system through the TCP 3389 network port. Although it uses Microsoft’s proprietary RDP protocol, the client implementation used in Linux systems are open-source programs licensed under the GNU General Public License (GPL) and has no legal restrictions on use.

### Spice
Simple Protocol for Independent Computing Environments (Spice) comprises a suite of tools aimed at accessing the desktop environment of _virtualised_ systems, either in the local machine or in a remote location. In addition to that, the Spice protocol offers native features to integrate the local and remote systems, like the ability to access local devices (for example, the sound speakers and the connected USB devices) from the remote machine and file sharing between the two systems.

#### Remmina Remote Desktop client -- supports all protocols

## Accessibility -- for disabilities
keyboard related accessibility features: _Sticky keys_, _Bounce keys_ and _Slow keys_.
_Mouse Keys_ allows the user to control the mouse pointer itself with the numerical keypad
### Visual impairment
High Contrast
Large Text
Cursor Size
Magnifier/Zoom
Screen reader -- Orca
Braille display
