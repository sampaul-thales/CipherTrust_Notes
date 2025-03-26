## Network Configuration for Virtual CipherTrust Manager

### Initial Setup
+ After deploying vCipherTrust Manager, pressing ENTER on the console screen should display the DHCP IP address.
+ Enter that IP address in your browser and it should present you with a screen asking for public key for login.
+ Open Puttygen or any other similar tool to generated keys for SSH login. Paste the public key information in CT web interface and submit.
+ After few mins, you should see a login screen. Use username 'admin' and password 'admin' to login.
+ You will be prompted to immediately change the default password.
+ A login screen would appear again after changing the password. Login as admin to check if your new password works.
+ Logout and close.


### SSH Login
+ CipherManager uses 'ksadmin' as the username to login for initial setup.
+ Use your SSH keys to login as 'ksadmin' to check if you're able to login.



### Network Configuration

#### Get the list of all available network interface.
<pre>
	ksadmin@ciphertrust:~$ nmcli device
	DEVICE       TYPE      STATE      CONNECTION
	enp1s0       ethernet  connected  Wired connection 1
	kylo0        bridge    unmanaged  --
	leia0        bridge    unmanaged  --
</pre>

#### Get information about a network interface.
<pre>
	ksadmin@ciphertrust:~$ nmcli device show enp1s0
	GENERAL.DEVICE:                         enp1s0
	GENERAL.TYPE:                           ethernet
	GENERAL.HWADDR:                         52:54:00:CE:E7:A5
	GENERAL.MTU:                            1500
	GENERAL.STATE:                          100 (connected)
	GENERAL.CONNECTION:                     Wired connection 1
	GENERAL.CON-PATH:                       /org/freedesktop/NetworkManager/ActiveConnection/1
	WIRED-PROPERTIES.CARRIER:               on
	IP4.ADDRESS[1]:                         10.164.50.16/16
	IP4.GATEWAY:                            10.164.50.1
	IP4.ROUTE[1]:                           dst = 0.0.0.0/0, nh = 10.164.50.1, mt = 100
	IP4.ROUTE[2]:                           dst = 0.0.0.0/0, nh = 10.164.50.1, mt = 0, table=100
	IP4.ROUTE[3]:                           dst = 10.164.0.0/16, nh = 0.0.0.0, mt = 100, table=100
	IP4.ROUTE[4]:                           dst = 10.164.0.0/16, nh = 0.0.0.0, mt = 100
	IP4.DNS[1]:                             10.164.50.10
	IP4.DNS[2]:                             10.164.50.1
	IP4.DOMAIN[1]:                          home.lab
	IP6.ADDRESS[1]:                         fe80::31a4:4ccc:2ded:dccf/64
	IP6.GATEWAY:                            --
	IP6.ROUTE[1]:                           dst = fe80::/64, nh = ::, mt = 100
	IP6.ROUTE[2]:                           dst = fe80::/64, nh = ::, mt = 100, table=100
</pre>

#### Set static IP to CTM.
<pre>
	ksadmin@ciphertrust:~$ nmcli conn modify "Wired connection 1" ipv4.method manual ipv4.address 10.164.50.16/16 ipv4.gateway 10.164.50.1 ipv4.dns 10.164.50.1
</pre>

#### Apply changes and activate the network interface.
<pre>
	ksadmin@ciphertrust:~$ nmcli conn up "Wired connection 1"
	Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/2)
</pre>

#### (Optionally) Disable IPv6 if you're not using it.
<pre>
	ksadmin@ciphertrust:~$ nmcli conn modify "Wired connection 1" ipv6.method disabled
	ksadmin@ciphertrust:~$ nmcli conn up "Wired connection 1"
	Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/3)
</pre>

#### Interface information after applying changes.
<pre>
	ksadmin@ciphertrust:~$ nmcli device show enp1s0
	GENERAL.DEVICE:                         enp1s0
	GENERAL.TYPE:                           ethernet
	GENERAL.HWADDR:                         52:54:00:CE:E7:A5
	GENERAL.MTU:                            1500
	GENERAL.STATE:                          100 (connected)
	GENERAL.CONNECTION:                     Wired connection 1
	GENERAL.CON-PATH:                       /org/freedesktop/NetworkManager/ActiveConnection/3
	WIRED-PROPERTIES.CARRIER:               on
	IP4.ADDRESS[1]:                         10.164.50.16/16
	IP4.GATEWAY:                            10.164.50.1
	IP4.ROUTE[1]:                           dst = 10.164.0.0/16, nh = 0.0.0.0, mt = 100
	IP4.ROUTE[2]:                           dst = 10.164.0.0/16, nh = 0.0.0.0, mt = 100, table=100
	IP4.ROUTE[3]:                           dst = 0.0.0.0/0, nh = 10.164.50.1, mt = 0, table=100
	IP4.ROUTE[4]:                           dst = 0.0.0.0/0, nh = 10.164.50.1, mt = 100
	IP4.DNS[1]:                             10.164.50.1
	IP6.GATEWAY:                            --
</pre>


### Changing hostname of CipherTrust Manager

#### To display current hostname.
<pre>
	ksadmin@ciphertrust:~$ kscfg system hostname get
	ciphertrust
</pre>

#### To change the hostname.
<pre>
	ksadmin@ciphertrust:~$ kscfg system hostname set -n vCipherTrust-I
	Note: please run "sudo systemctl restart keysecure" to have new hostname effective in CipherTrust Manager
</pre>

#### Restart KeySecure service.
<pre>
	ksadmin@ciphertrust:~$ sudo systemctl restart keysecure
</pre>

#### Check for hostname again.
<pre>
	ksadmin@ciphertrust:~$ kscfg system hostname get
	vCipherTrust-I
</pre>




### Changing timezone of CipherTrust Manager.

#### Check the current timezone. 
<pre>
	ksadmin@ciphertrust:~$ timedatectl
        	       Local time: Tue 2025-03-25 18:48:14 UTC
	           Universal time: Tue 2025-03-25 18:48:14 UTC
        	         RTC time: Tue 2025-03-25 18:48:14
	                Time zone: Etc/UTC (UTC, +0000)
	System clock synchronized: no
	              NTP service: n/a
	       	  RTC in local TZ: no
</pre>

	  
#### Display list of all timezone codes.
<pre>
	ksadmin@ciphertrust:~$ timedatectl list-timezones | grep US
	US/Alaska
	US/Aleutian
	US/Central
	US/Eastern
	US/Pacific

	ksadmin@ciphertrust:~$ timedatectl list-timezones | grep Canada
	Canada/Atlantic
	Canada/Central
	Canada/Pacific
</pre>


#### Set timezone for your location.
<pre>
	ksadmin@ciphertrust:~$ sudo timedatectl set-timezone Canada/Pacific
</pre>


#### Check timezone again.
<pre>
	ksadmin@ciphertrust:~$ timedatectl
        	       Local time: Tue 2025-03-25 11:51:32 PDT
	           Universal time: Tue 2025-03-25 18:51:32 UTC
        	         RTC time: Tue 2025-03-25 18:51:32
	                Time zone: Canada/Pacific (PDT, -0700)
	System clock synchronized: no
	              NTP service: n/a
        	  RTC in local TZ: no
</pre>


<- [Back to previous page](README.md)
