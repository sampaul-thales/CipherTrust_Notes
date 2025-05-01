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

![ss1](https://github.com/user-attachments/assets/c8487d97-1e34-49f1-a1e7-c978ddbc3011)

- Under **Internal Connection**, click **Add HSM Server**.
    + Enter your HSM's hostname or IP address
    + Upload the Luna HSM server certificate
    + Add an optional description.
    + Tick the Cloud Key Manager option
    + Click **Add HSM Server**.

![ss2](https://github.com/user-attachments/assets/1e03a35c-e49e-441e-ab63-f59195442b7b)

- You should now see the newly added Luna HSM server.
![ss3](https://github.com/user-attachments/assets/4f73aa85-d253-4e62-8d2a-a0b02b62c072)

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

![ss4](https://github.com/user-attachments/assets/74778f31-7b07-486a-bd20-681afdc84fea)

- Choose "HSM" as the category, and select **Luna Network Connection** as the hsm type.

![ss5](https://github.com/user-attachments/assets/9b2f0a43-fa74-4962-a447-c696ca9ae924)

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

![ss6](https://github.com/user-attachments/assets/12b39b28-3ed8-48a0-9fa8-bc22c3b8fea4)

- Tick the **Cloud Key Manager** option, then click "Add Connection".

![ss7](https://github.com/user-attachments/assets/c130a459-f3aa-4866-b653-8da4add480ed)

- You should now see the new connection listed. Optionally, you can use "Test Connection" to confirm that the connection works.

![ss8](https://github.com/user-attachments/assets/69bfa834-33fa-4512-99fd-144c73c33058)

<br>

### Step 3 : Adding Luna HSM Partition.
- Click **Products** options on the left and select **Cloud Key Manager**.
- Under **KMS Containers** section, select **Luna HSM Partitions**.
- Click **+ Add Partition** option on the right.
- On the "Add Existing Partition" screen, choose the connection that you added from the **Select Connection** dropdown.

![ss10](https://github.com/user-attachments/assets/3cfd9537-aa15-4104-8bb0-fcefebfa1642)

- Once selected, the label of the partition assigned to the CCKM client should be displayed. Click **Add** to proceed.
- You should now see the newly added partition listed.

![ss11](https://github.com/user-attachments/assets/94322ae3-20bb-4846-a933-de26cbd8ce01)

<br>

### Step 4 :  Refreshing keys.
- Under **Cloud Keys** category on the left, select **Luna**.
- Click on **Refresh All** option on the right, and wait for CCKM to update the list of keys stored in the Luna HSM partition.

![ss9](https://github.com/user-attachments/assets/929da0ae-77dc-4796-96ec-7ceaedbe199d)

- The time required to refresh the keys depends on the number of keys in the Luna Partition and the network latency.

![ss12](https://github.com/user-attachments/assets/589f49df-6489-4110-b720-e7676821a4fb)

<br>

[Back to previous page](README.md)
