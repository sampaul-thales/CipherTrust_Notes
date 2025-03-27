## CipherTrust Manager | Root of Trust (RoT) using Luna Network HSM 7

This section describes how to set up a Luna Network HSM as the Root of Trust (RoT) for CipherTrust Manager. I'll show you how to complete the RoT setup using ksctl and the CM Web UI.

<br>

> [!CAUTION]
> Setting the Root of Trust requires resetting the CipherTrust Manager, which will result in the deletion of all existing data.
> Please ensure you take a backup before configuring the RoT.



### Prerequisites
+ A machine (Windows or Linux) with Luna Client installed.
+ A firewall rule allowing connections to ports 22 and 1792 on the Luna Network HSM.
+ Credentials for accessing the Network HSM via SSH.
+ A partition initialised with Crypto Officer role.

<br>

### Step 1) Preparing keys and certificates.

#### - Generating client key and certificate for your CipherTrust Manager.
<pre>
sampaul@ct-ub2204:~$ vtl createcert -n vciphertrust-i.daenerys.home -c CA -s BC -o Thales -u Sales Engineering -l Vancouver
vtl (64-bit) v10.7.2-16. Copyright (c) 2024 Thales Group. All rights reserved.

Private Key created and written to: /usr/safenet/lunaclient/cert/client/vciphertrust-i.daenerys.homeKey.pem
Certificate created and written to: /usr/safenet/lunaclient/cert/client/vciphertrust-i.daenerys.home.pem
</pre>

#### - Import server certificate from the Network HSM.
<pre>
sampaul@ct-ub2204:~$ scp spaul@sehsm3.thaleselab.com:server.pem sehsm3.pem
spaul@sehsm3.thaleselab.com's password:
server.pem                          100% 1159    12.3KB/s   00:00
</pre>

<br>

### Step 2) Registering the client and assigning a partition to it.

#### - Export the client certificate onto the Network HSM.
<pre>
sampaul@ct-ub2204:~$ scp /usr/safenet/lunaclient/cert/client/vciphertrust-i.daenerys.home.pem spaul@sehsm3.thaleselab.com:
spaul@sehsm3.thaleselab.com's password:
vciphertrust-i.daenerys.home.pem      100% 1237    13.1KB/s   00:00
</pre>

#### - Register the client and assign partition to it.
<pre>
sampaul@ct-ub2204:~$ ssh spaul@sehsm3.thaleselab.com << EOF
> client register -client vciphertrust-i.daenerys.home -hostname vciphertrust-i.daenerys.home
> client assign -client vciphertrust-i.daenerys.home -partition sp_sehsm3
> EOF
Pseudo-terminal will not be allocated because stdin is not a terminal.
spaul@sehsm3.thaleselab.com's password:

Luna Network HSM Command Line Shell v7.8.5-20. Copyright (c) 2024 Thales Group. All rights reserved.

'client register' successful.
Command Result : 0 (Success)

'client assignPartition' successful.
Command Result : 0 (Success)
</pre>

#### - Check if the client is registered and has a partition assigned to it.
<pre>
sampaul@ct-ub2204:~$ ssh spaul@sehsm3.thaleselab.com client show -c vciphertrust-i.daenerys.home
spaul@sehsm3.thaleselab.com's password:
ClientID:     vciphertrust-i.daenerys.home
Hostname:     vciphertrust-i.daenerys.home
Partitions:   "sp_sehsm3"
</pre>

<BR>

### Setup RoT for CipherTrust using ksctl utility.

#### - Get partition label and its serial number.
<pre>
sampaul@ct-ub2204:~$ ssh spaul@sehsm3.thaleselab.com par list
spaul@sehsm3.thaleselab.com's password:
                                                                              Storage (bytes)
                                                       Partition    ----------------------------------
Partition            Name                              Version      Objects  Total     Used     Free
===================  ================================  ===========  =======  =======  =======  =======
1582775433934        sp_sehsm3                         0                  0   648381        0   648381
</pre>

#### - Register RoT
> [!NOTE]  
> For this step, since I am not using a configuration file, I will pass all the information required by ksctl directly to register the Root of Trust, to demonstrate what information is necessary.

<pre>
sampaul@ct-ub2204:~$ ksctl hsm setup luna --url https://vciphertrust-i.daenerys.home --user sampaul --nosslverify --hsm-host sehsm3.thaleselab.com --partition-name sp_sehsm3 --serial 1582775433934 --server-cert-file sehsm3.pem --partition-name sp_sehsm3 --serial 1582775433934 --client-cert-file /usr/safenet/lunaclient/cert/client/vciphertrust-i.daenerys.home.pem --client-cert-key-file /usr/safenet/lunaclient/cert/client/vciphertrust-i.daenerys.homeKey.pem --reset
password:
Partition password is required for HSM setup.
Please provide the partition password:

WARNING - Reset is a destructive operation and will wipe all data in the CipherTrust Manager!
Are you really sure you want to continue? [y/N] y
{
        "id": "12d3786f-c244-4e43-ae92-e09ab084c466",
        "type": "luna",
        "config": {
                "host": "sehsm3.thaleselab.com",
                "partition_name": "sp_sehsm3",
                "serial": "1582775433934",
                "server-cert": "-----BEGIN CERTIFICATE-----\nMIIDKzCCAhOgAwIBAgIBADANBgkqhkiG9w0BAQsFADBZMQswCQYDVQQGEwJDQTEQ\nMA4GA1UECAwHT250YXJpbzEPMA0GA1UEBwwGT3R0YXdhMRYwFAYDVQQKDA1DaHJ5\nc2FsaXMtSVRTMQ8wDQYDVQQDDAY2Nzk3OTcwHhcNMjQwOTA1MTI1NzE0WhcNMzQw\nOTA3MTI1NzE0WjBZMQswCQYDVQQGEwJDQTEQMA4GA1UECAwHT250YXJpbzEPMA0G\nA1UEBwwGT3R0YXdhMRYwFAYDVQQKDA1DaHJ5c2FsaXMtSVRTMQ8wDQYDVQQDDAY2\nNzk3OTcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDLglQghGGHz1lD\nyu5MDxPYet94QNfmld9lqEFXTkI/chy8S+G8vSxsY8WgIQTeXsN8LNkY/A8gIulc\nTMTnlA0MwSTBMJz5JfNdQ8LaqdGW5BQirCF3F3l5R4w9/tBqiZsQ8htgK3R2v2vb\n701GUKn5AoW2Kqgqt3LQAj+bTn29BvIPFHtXQ0h0Ka9ilhDvI/AgfpJI2LVQTlXL\nxx2reWYbOB2/zNceWo2Fb1uxLvrth6DnFJfFRJIgRXQofRhgJcoTNVJOMmWrl4OZ\nNRT4kKNjumcka4Cv/pBPTXR/RNuKFnhY9+uxmi4aY+vsRhbgXPXj9iKqzJqE8Png\n9Do+/bbbAgMBAAEwDQYJKoZIhvcNAQELBQADggEBABXJNi4HFGAmFLLpfz/gzqfb\nbBlQFUvrfzM+WS0K2Cke1ZZGWnsXN6xJcxOijW9wG9wF0FkqW87hLcheGEX7JY6B\nOjjxSgPzupFt8KI9JtrSz1feqK3bwxAUZ7RNdgf18KK3mxhZ8WZGbNxag1FGDXZF\nXHEpMgE1q1A6RvVQh+4eP6EZ5pQ+TecZZlSWSrR3OP3kb2zPoLTMWBHk8MoAotR2\n79uE2mVBSwecoXrOs+o9G/Nb3LVX8l0R8oWvdHfNmh/ZfNsrvT/0z8aIXihEwg2d\ntaDV6nNVO4v4JekVOcf2CvAQxCsj2xnftsliTnA0M2wXNp9E2U+5qu48ibcS1pA=\n-----END CERTIFICATE-----\n"
        }
}
</pre>

#### - Upon completion, you should be able to see two new objects in the partition.
<pre>
[sehsm3.thaleselab.com] lunash:>par showContents -partition sp_sehsm3
  Please enter the Crypto Officer's password:
  > *********
   Partition Name:                                  sp_sehsm3
   Partition SN:                                1582775433934
   Partition Label:                                 sp_sehsm3
   Partition Version:                                       0
   Storage (Bytes): Total=648381, Used=496, Free=647885
   Number objects:  2

   Object Label:  DARKSTARKEY-mac-key
   Object Type:   Symmetric Key
   Object Handle: 37

   Object Label:  DARKSTARKEY-encryption-key
   Object Type:   Symmetric Key
   Object Handle: 34
</pre>

> [!NOTE]  
> Upon completion of the RoT setup, you will be required to log in as the user 'admin' with the default password 'admin'. You will be forced to change the password upon successful login.

<br>

### Setup RoT for CipherTrust using CipherTrust Web UI.
+ Login as an administrator.
+ Expand "Admin Settings" > "Root of Trust".
+ Read the warning that resetting CipherTrust Manager will delete all existing data. Click 'Yes, configure an HSM' if you are prepared to proceed with the reset.
![P1](https://github.com/user-attachments/assets/c1b55987-9233-4be0-b040-320164dda014)

+ From the list of supported Hardware Security Modules, select "Thales Luna Network HSM" and then click "Next".
![P2](https://github.com/user-attachments/assets/2988d7cc-19d8-48b2-bd55-24c8009b237d)

+ Enter the required information and upload the certificates and the key file. Click "next".
![P3](https://github.com/user-attachments/assets/1549bb39-f447-40bb-a1a8-584a53ef76b5)

+ Click on "Confirm loss of key material and system restart!".
![P4](https://github.com/user-attachments/assets/e0e73e8a-1e5c-4df6-92bb-e4f9c7013179)

+ Wait for the setup to complete. You may get logged out upon completion of RoT setup.
![P5](https://github.com/user-attachments/assets/f4aa20c9-aae8-4153-b210-47ddb69ae8b2)

> [!NOTE]  
> Upon completion of the RoT setup, you will be required to log in as the user 'admin' with the default password 'admin'. You will be forced to change the password upon successful login.
