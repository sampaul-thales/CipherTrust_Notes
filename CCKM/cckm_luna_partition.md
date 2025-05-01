## CKKM | Luna Keys

In this section, I will demonstrate how to set up a Luna partition with CCKM, to easily manage the keys generated for the purpose of BYOK and HYOK.

### - Prerequisites
- CipherTrust Manager
- Luna Network HSM with software v7.3.x or higher.
- An initialised partition with Crypto Officer credential.
- Access to Luna Shell (lunash).

> [!NOTE]
> - CCKM does not support partitions from an FM enabled Luna HSM.
> - A CipherTrust Manager user account must have, at a minimum, the CCKM Admins and Connection Admins roles assigned — unless the user has full admin privileges.
> - CCKM is not a solution for managing all objects stored in a Luna partition. It is limited to BYOK/HYOK use cases.

<br>

### Step 1 : Register Luna Network HSM and Client.

- Extract server certificate from your Luna Network HSM.
<pre>
sampaul@ct-ub2204:~$ scp spaul@sehsm3.thaleselab.com:server.pem sehsm3.pem
(spaul@sehsm3.thaleselab.com) Password:
sampaul@ct-ub2204:~$ ls -l
-rw-r--r-- 1 sampaul sampaul 1159 May  1 14:54 sehsm3.pem
</pre>

- Log in to CipherTrust Manager.
- Click on **Access Management** on the left, then select **Connections**.
- Under **Internal Connection**, click **Add HSM Server**.
    + Enter your HSM's hostname or IP address
    + Upload the Luna HSM server certificate
    + Add an optional description.
    + Tick the Cloud Key Manager option
    + Click **Add HSM Server**.
- You should now see the newly added Luna HSM server.
- Next, click **Download Luna Client Cert** to download the CCKM Lunaclient certificate.
- Upload the downloaded CCKM client certificate to the HSM as shown below:
<pre>
sampaul@ct-ub2204:~/luna_certs$ scp cckm-client-1b6844fc-2d83-4267-b557-020b3a89f8d7.pem spaul@sehsm3.thaleselab.com:
(spaul@sehsm3.thaleselab.com) Password:
</pre>
- Register the CCKM client as shown below:

<pre>
sampaul@ct-ub2204:~/luna_certs$ ssh spaul@sehsm3.thaleselab.com << EOF
> client register -client cckm-client-1b6844fc-2d83-4267-b557-020b3a89f8d7 -hostname cckm-client-1b6844fc-2d83-4267-b557-020b3a89f8d7
> client assign -client cckm-client-1b6844fc-2d83-4267-b557-020b3a89f8d7 -partition sp_sehsm3
> EOF
Pseudo-terminal will not be allocated because stdin is not a terminal.
(spaul@sehsm3.thaleselab.com) Password:
'client register' successful.
'client assignPartition' successful.
</pre>

<br>

### Step 2 : Add Connection to Luna Partition for CCKM.

- Click on **Access Management** on the left, select **Connections** and then Click **Add Connection** on the right.
- Choose "HSM" as the category, and select **Luna Network Connection** as the hsm type.
- In the General Info section, fill in the name and description, then click **Next**.
- Enter the *Crypto officer* password in **Partition Password** field.
- Select the Luna HSM server from the dropdown menu, provider enter the **Partition Label** and **Serial Number**. 
- Here's how you can lookup the partition label and serial number:
<pre>
sampaul@ct-ub2204:~/luna_certs$ ssh spaul@sehsm3.thaleselab.com par list
(spaul@sehsm3.thaleselab.com) Password:
                                                                             Storage (bytes)
                                                       Partition    ----------------------------------
Partition            Name                              Version      Objects  Total     Used     Free
===================  ================================  ===========  =======  =======  =======  =======

1582775433934        sp_sehsm3                         0                 23   648381     9408   638973
</pre>

- Click on **Test Credentials** to verify that the *Crypto Officer* password is correct. Click **Next** when you're ready to proceed.
- Tick the **Cloud Key Manager** optioon, then click "Add Connection".
- You should now see the new connection listed. Optionally, you can use "Test Connection" to confirm that the connection works.

<br>

### Step 3 : Adding Luna HSM Partition.
- Click **Products** options on the left and select **Cloud Key Manager**.
- Under **KMS Containers** section, select **Luna HSM Partitions**.
- Click **+ Add Partition** option on the right.
- On the "Add Existing Partition" screen, choose the connection that you added from the **Select Connection** dropdown. 
- Once selected, the label of the partition assigned to the CCKM client should be displayed. Click **Add** to proceed.
- You should now see the newly added partition listed.

<br>

### Step 4 :  Refreshing keys.
- Under **Cloud Keys** category on the left, select **Luna**.
- Click on **Refresh All** option on the right, and wait for CCKM to update the list of keys stored in the Luna HSM partition.
- The time required to refresh the keys depends on the number of keys in the Luna Partition and the network latency.

<br>

[Back to previous page](README.md)