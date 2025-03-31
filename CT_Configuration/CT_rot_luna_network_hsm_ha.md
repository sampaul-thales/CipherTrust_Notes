## CipherTrust Manager | Root of Trust (RoT) using Luna Network HSM 7 in HA

This section describes how to set up High Availability (HA) of Luna Network HSMs as the Root of Trust (RoT) on CipherTrust Manager.

<br>

> [!CAUTION]
> Setting the Root of Trust requires resetting the CipherTrust Manager, which will result in the deletion of all existing data.
> Please ensure you take a backup before configuring the RoT.

<br>


### Prerequisites For Luna Network HSM HA.
+ All prerequisites described in [Luna Network HSM RoT Setup](CT_rot_luna_network_hsm.md).
+ CipherTrust Manager must have network connectivity with all members in HA group.
+ All members in HA must have identical cloning domain.
+ All members in HA must have identical crypto-officer password.
+ All members in HA should have identical FIPS mode configuration.
+ All members in HA should have identical partition policies.

<br>

> [!NOTE]  
> The instructions that follow assume that you've already configured at least one network HSM as the Root of Trust for your CipherTrust. If you haven't done so already, please follow the instructions described in [Luna Network HSM RoT Setup](CT_rot_luna_network_hsm.md).

<br>

### Client registration for CipherTrust.

#### - Export the client certificate you generated for your CipherTrust Manager to the secondary HSM.
<pre>
sampaul@ct-ub2204:~$ scp /usr/safenet/lunaclient/cert/client/vciphertrust-i.daenerys.home.pem spaul@sehsm4.thaleselab.com:
spaul@sehsm4.thaleselab.com's password:
vciphertrust-i.daenerys.home.pem
</pre>

#### - Register the client certificate on the secondary HSM.
<pre>
sampaul@ct-ub2204:~$ ssh spaul@sehsm4.thaleselab.com << EOF
> client register -client vciphertrust-i.daenerys.home -hostname vciphertrust-i.daenerys.home
> client assign -client vciphertrust-i.daenerys.home -partition sp_sehsm4
> client show -client vciphertrust-i.daenerys.home
> EOF
Pseudo-terminal will not be allocated because stdin is not a terminal.
spaul@sehsm4.thaleselab.com's password:

Luna Network HSM Command Line Shell v7.8.5-20. Copyright (c) 2024 Thales Group. All rights reserved.

'client register' successful.

Command Result : 0 (Success)

'client assignPartition' successful.

Command Result : 0 (Success)

ClientID:     vciphertrust-i.daenerys.home
Hostname:     vciphertrust-i.daenerys.home
Partitions:   "sp_sehsm4"

Command Result : 0 (Success)
</pre>

#### - Import the server certificate from the secondary HSM.
<pre>
sampaul@ct-ub2204:~$ scp spaul@sehsm4.thaleselab.com:server.pem sehsm4.pem
spaul@sehsm4.thaleselab.com's password:
server.pem 
</pre>

<br>


### Setting up HA using CM GUI.

+ Log in as an administrator.

+ Expand "Admin Settings" > "Root of Trust".

+ If you've already setup a primary member, then you should be able to see it listed as  Configured HSMs.

+ Click on "Add" option to open "Add to High Availability (HA) Group" wizard. Click "Next" when you're ready.

+ Input all required information and HSM certificate. Optionally, select "Preserve existing data on this partition" option, if the partition is not empty.

+ Click "Next".

+ Confirm the change by clicking "Add HSM". Wait for the new member to be added.

+ Upon successful completion, you should see a new member added to the list of Configured HSMs.

<br>

### Adding new member using ksctl.

#### - List the HSM servers to verify if at least one HSM is configured as a Root of Trust (RoT).
<pre>
sampaul@ct-ub2204:~$ ksctl hsm servers list | grep 'host\|serial'
                                "host": "sehsm3.thaleselab.com",
                                "serial": "1582775433934",
</pre>

#### - Add secondary member.
> [!IMPORTANT]  
> In the command below, I've used the --timeout 120 switch to increase the client timeout period to accommodate the network latency of the member I'm adding. By default, the client has a timeout period of 20 seconds.
>  I'm also using the --force-copy option to retain objects that exist in the partition I'm adding. Typically, CipherTrust expects the partition to be empty for an HA setup.

<pre>
 sampaul@ct-ub2204:~$ ksctl hsm servers add --hsm-host sehsm4.thaleselab.com --serial 1582142134699 --server-cert-file sehsm4.pem --timeout 120 --force-copy
{
        "id": "aba874c1-5ccb-468c-952e-f507886886c3",
        "type": "luna",
        "config": {
                "host": "sehsm4.thaleselab.com",
                "partition_name": "sp_sehsm3",
                "serial": "1582142134699",
                "server-cert": "-----BEGIN CERTIFICATE-----\nMIIDKzCCAhOgAwIBAgIBADANBgkqhkiG9w0BAQsFADBZMQswCQYDVQQGEwJDQTEQ\nMA4GA1UECAwHT250YXJpbzEPMA0GA1UEBwwGT3R0YXdhMRYwFAYDVQQKDA1DaHJ5\nc2FsaXMtSVRTMQ8wDQYDVQQDDAY2Nzk1MjUwHhcNMjQwODExMTUwMTE4WhcNMzQw\nODEzMTUwMTE4WjBZMQswCQYDVQQGEwJDQTEQMA4GA1UECAwHT250YXJpbzEPMA0G\nA1UEBwwGT3R0YXdhMRYwFAYDVQQKDA1DaHJ5c2FsaXMtSVRTMQ8wDQYDVQQDDAY2\nNzk1MjUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC2WGoY+0Shn+3E\nxQ1sTwxLjGUl2O0hbysGIHtG7+OIrEJ3bhnKlkJfyOwaFfIKbUeoeC2we4h2rtOe\nWte7IDlrGwriCc9NHOijM6Rmnm8x6YdZDDMrmfDGnDIHxhha5Rg5NFMGWbp6U1Zl\nOd846qJCJIzD369E4NGYn33a6ZfWWqICoivY7Hly61d6Of0A/ecIaHaPSVEgWXg3\nSBeMv37fEER4B7a/kNmv+aDHQV8F6SJQmhp9mWDcfeQo3i5wQkxkV+YjjrW2FCz7\n5XnBXaQ1cQoMZNX9s2HN+idtR0sVc6LoDFquDfkR65woiUOw7frc46p82ai4rPx4\niOE+b//DAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAEvJke+zf4ESWMnVgd1I3jnV\n16tY2ayzBUf19qhlKa7NiFEJkLPGWHNQD0SkqyniTgz3q/sZt7tpmhKU1/zgdVT9\nphKmAEPc5EpDVc8paiwfslAjFtbZvfgWmupA4VP8Bpd4AOpHRpJ3Nh6zSPrrPq+x\nE4u4zNFee6FeykJDdkdvHrpsy/idpCjEycimGVZ+EDnuqycA1oSkYL5tfzQCEPQj\nWH3bl5qqlsD9gDnTzluwif2C0qvgmJ2kfhPpKBrYsZYkoERj7ZPCK6x6aQSOQb5k\nOuIYoQiceJ69yMWnUGuaTkWWNmV6NMgX5zSPrp9qmJW0GE71PNwaxvkORcYTvZU=\n-----END CERTIFICATE-----\n"
        }
}
</pre>

#### - Listing servers again to check for the newly added server.
<pre>
sampaul@ct-ub2204:~$ ksctl hsm servers list | grep 'host\|serial'
                                "host": "sehsm4.thaleselab.com",
                                "serial": "1582142134699",
                                "host": "sehsm3.thaleselab.com",
                                "serial": "1582775433934",
</pre>

<br>

[Back to previous page](README.md)