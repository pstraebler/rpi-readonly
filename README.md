# Make Raspberry Pi file system read-only

	# apt update && apt upgrade -y
	# apt remove triggerhappy logrotate dphys-swapfile
	# apt install busybox-syslogd
	# apt remove rsyslog
	# apt autoremove
	# echo "fastboot noswap ro" >> /boot/cmdline.txt
	# mkdir -pv /root/before-ro/var/lib
	# mkdir -pv /root/before-ro/etc
	# mv /var/lib/dhcp before-ro/var/lib/
	# mv /var/lib/dhcpcd/ before-ro/var/lib/
	# mv /var/spool/ before-ro/var/
	# mv /etc/resolv.conf before-ro/etc/
	# ln -s /tmp /var/lib/dhcp
	# ln -s /tmp /var/lib/dhcpcd5
	# mkdir /var/spool
	# chmod 755 /var/sppol
	# touch /tmp/dhcpcd.resolv.conf
	# ln -s /tmp/dhcpcd.resolv.conf /etc/resolv.conf
	# mv /var/lib/systemd/random-seed /root/before-ro/var/lib/
	# ln -s /tmp/random-seed /var/lib/systemd/random-seed
	
**/etc/fstab** :
		
	proc            /proc           proc    defaults          0       0
	PARTUUID=4cb3ccb0-01  /boot           vfat    defaults,ro          0       2
	PARTUUID=4cb3ccb0-02  /               ext4    defaults,noatime,ro  0       1
	tmpfs	/tmpfs	tmpfs	defaults,size=200M	0	0
	tmpfs        /tmp            tmpfs   nosuid,nodev         0       0
	tmpfs        /var/spool            tmpfs   defaults,noatime,nosuid,nodev,noexec,mode=0755,size=64M	0       0
	tmpfs        /var/log        tmpfs   nosuid,nodev         0       0
	tmpfs        /var/tmp        tmpfs   nosuid,nodev         0       0
  
  
**/lib/systemd/system/systemd-random-seed.service** :

	[...]
	[Service]
	Type=oneshot
	RemainAfterExit=yes
	ExecStartPre=/bin/echo "" >/tmp/random-seed
	ExecStart=/lib/systemd/systemd-random-seed load
	ExecStop=/lib/systemd/systemd-random-seed save
	[...]
	
**/etc/bash.bashrc** :

	alias ro='mount -o remount,ro / ; mount -o remount,ro /boot'
	alias rw='mount -o remount,rw / ; mount -o remount,rw /boot'
	
**/etc/bash.bash_logout** :

	if [ $UID = "0" ] ; then
		mount -o remount,ro /
		mount -o remount,ro /boot
	fi
	
### Synchro NTP

	# apt install ntp ntpdate
	# systemctl stop ntp
	# ntpd -gqc /etc/ntpd.conf
	# systemctl enable --now ntp
	
**/lib/systemd/system/ntp.service** :

PrivateTmp=**true** ==> PrivateTmp=**false**
	
Commande pour consulter les logs :

	# logread
