## Upgrading CipherTrust Manager.

This section explains how to upgrade CipherTrust Manager to a newer version. For this demonstration, I shall upgrade CipherTrust Manager from version 2.20 to version 2.21.

<br>

Upgrade package : **610-000611-034_SW_Upgrade_Package_CipherTrust_Manager_v2.21.0_RevA.tar.gz.gpg**

<br>

#### Step 1: Upload the upgrade package to CipherTrust Manager.

<pre>
C:\Users\sampaul\Downloads>scp -i CTM.key 610-000611-034_SW_Upgrade_Package_CipherTrust_Manager_v2.21.0_RevA.tar.gz.gpg ksadmin@vciphertrust-i.daenerys.home:
610-000611-034_SW_Upgrade_Package_CipherTrust_Manager_v2.21.0_RevA.tar.gz.gpg         100% 5245MB  36.0MB/s   02:25
</pre>

<br>

#### Step 2: Log in to CipherTrust Manager as ksadmin user.
<pre>
C:\Users\sampaul\Downloads>ssh -i CTM.key ksadmin@vciphertrust-i.daenerys.home

#####################################################################################################


   _______       __             ______                __     __  ___
  / ____(_)___  / /_  ___  ____/_  __/______  _______/ /_   /  |/  /___ _____  ____ _____ ____  _____
 / /   / / __ \/ __ \/ _ \/ ___// / / ___/ / / / ___/ __/  / /|_/ / __ `/ __ \/ __ `/ __ `/ _ \/ ___/
/ /___/ / /_/ / / / /  __/ /   / / / /  / /_/ (__  ) /_   / /  / / /_/ / / / / /_/ / /_/ /  __/ /
\____/_/ .___/_/ /_/\___/_/   /_/ /_/   \__,_/____/\__/  /_/  /_/\__,_/_/ /_/\__,_/\__, /\___/_/
      /_/                                                                         /____/

                                        Version 2.20.0+15016

#####################################################################################################
</pre>

<br>

#### Step 3: Start the upgrade.
<pre>
ksadmin@vCipherTrust-I:~$ ls -l
total 5370980
-rw-r--r-- 1 ksadmin staff 5499876577 Sep  2 13:57 610-000611-034_SW_Upgrade_Package_CipherTrust_Manager_v2.21.0_RevA.tar.gz.gpg

ksadmin@vCipherTrust-I:~$ sudo /opt/keysecure/ks_upgrade.sh -f 610-000611-034_SW_Upgrade_Package_CipherTrust_Manager_v2.21.0_RevA.tar.gz.gpg

IMPORTANT: Do not restart or power cycle the device during the upgrade.
Some services may fail to start correctly if the device is restarted before the upgrade completes.
Contact customer support if your device fails to restart.

Detected a terminal, launching a screen session, session recorded in log file (ks_upgrade_2025-09-02-142858.log).
[screen is terminating]
Showing the last 20 lines of the log file (ks_upgrade_2025-09-02-142858.log), for convenience. Open the file for more details.

2025-09-02 14:09:58-0700: status: error
2025-09-02 14:09:08-0700: status: error
2025-09-02 14:09:18-0700: status: error
2025-09-02 14:09:28-0700: status: error
2025-09-02 14:09:38-0700: status: error
2025-09-02 14:09:48-0700: status: error
2025-09-02 14:09:58-0700: status: error
2025-09-02 14:09:08-0700: status: error
2025-09-02 14:09:18-0700: status: error
2025-09-02 14:09:28-0700: status: error
2025-09-02 14:09:38-0700: status: error
2025-09-02 14:09:48-0700: status: starting
2025-09-02 14:09:58-0700: status: starting
2025-09-02 14:09:08-0700: status: starting
2025-09-02 14:09:18-0700: status: starting
2025-09-02 14:09:28-0700: status: starting
2025-09-02 14:09:38-0700: status: starting
2025-09-02 14:09:43-0700: started
2025-09-02 14:09:43-0700: Upgrade to 2.21.0+15451 succeeded
2025-09-02 14:09:43-0700: Reboot the instance to finish the upgrade
</pre>

<br>

#### Step 4: Reboot CipherTrust Manager once the upgrade has completed.
<pre>
ksadmin@vCipherTrust-I:~$ sudo reboot
Connection to vciphertrust-i.daenerys.home closed by remote host.
Connection to vciphertrust-i.daenerys.home closed.
</pre>

<br>

#### Step 5: Log in to CipherTrust Manager once it has rebooted. The upgraded version number will be displayed on the login banner.
<pre>
C:\Users\sampaul\Downloads>ssh -i C:\Users\sampaul\Dojo\LAB\CTM.key ksadmin@vciphertrust-i.daenerys.home

#####################################################################################################


   _______       __             ______                __     __  ___
  / ____(_)___  / /_  ___  ____/_  __/______  _______/ /_   /  |/  /___ _____  ____ _____ ____  _____
 / /   / / __ \/ __ \/ _ \/ ___// / / ___/ / / / ___/ __/  / /|_/ / __ `/ __ \/ __ `/ __ `/ _ \/ ___/
/ /___/ / /_/ / / / /  __/ /   / / / /  / /_/ (__  ) /_   / /  / / /_/ / / / / /_/ / /_/ /  __/ /
\____/_/ .___/_/ /_/\___/_/   /_/ /_/   \__,_/____/\__/  /_/  /_/\__,_/_/ /_/\__,_/\__, /\___/_/
      /_/                                                                         /____/

                                        Version 2.21.0+15451

#####################################################################################################


 * Support:        https://supportportal.thalesgroup.com
Last login: Tue Sep  2 14:25:36 2025 from 172.16.20.2
</pre>

<br>

[Back to previous page](README.md)
