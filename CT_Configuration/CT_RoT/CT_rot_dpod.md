## CipherTrust Manager | Root of Trust (RoT) using DPoD

This section of my notes describes how to set up the Root of Trust (RoT) for CipherTrust Manager using Data Protection on Demand (DPoD) a.k.a Luna Cloud HSM.

<br>

> [!CAUTION]
> Setting the Root of Trust requires resetting the CipherTrust Manager, which will result in the deletion of all existing data.
> Please ensure you take a backup before configuring the RoT.

<br>

### Prerequisites for configuring DPoD as RoT.
+ You must have the service client zip downloaded from the DPoD portal.
+ DPoD instance/partition/tile must be initialized with a Crypto Officer password.

<br>

### Configuring DPoD as the RoT for CipherTrust using WeB UI.

+ Log in as a user with administrative priveledges.

+ Expand "Admin Settings" > "Root of Trust".

+ When setting up RoT for the first time, a warning will appear. Click "Yes, configure an HSM" when you're ready.

+ Select "Thales" DPoD from the list of supported HSMs.

+ Enter the label of your DPoD instance(partition) in the "Partition Name" field, and enter crypto officer password in the "HSM Partition Password" field.

+ Click browse and upload the setup ZIP file you downloaded from your DPoD customer portal. Click "Next" when ready.

+ A warning about CipherTrust Manager being reset will appear. Click "Confirm loss of key material and system restart!" if you're prepared.

+ Wait for CipherTrust Manager to validate and apply the changes. This may take 2-3 minutes to complete.

+ Once finished, the login page will appear. Log in using the default username 'admin' and password 'admin'. You will be prompted to change the default password upon login.

+ Expand "Admin Settings" > "Root of Trust" again. You should now see your DPoD instance listed as a configured Root of Trust.





### Configuring DPoD as the RoT for CipherTrust using ksctl utility.

#### - Ensure that ksctl is configured to point to the correct CipherTrust Manager instance.
<pre>
sampaul@ct-ub2204:~$ export KSCTL_URL=https://vciphertrust-ii.daenerys.home

sampaul@ct-ub2204:~$ ksctl system info get
password:
{
        "name": "vCipherTrust-II",
        "version": "2.19.0+14195",
        "version_suffix": "null",
        "model": "CipherTrust Manager k170v",
        "vendor": "Thales Group",
        "crypto_version": "1.7.0",
        "uptime": "0 days, 4 hours, 17 minutes",
        "system_time": "2025-04-02T13:46:35-07:00"
}
</pre>

#### - Ensure that you have the following information ready before proceeding.
<pre>
sampaul@ct-ub2204:~$ cat dpod/crystoki-template.ini | grep 'AuthToken\|PartitionData'
AuthTokenConfigURI=< --- REDACTED URL --- >
AuthTokenClientId=< --- REDACTED CLIENT ID --- >
AuthTokenClientSecret=< --- REDACTED CLIENT SECRET --- >
PartitionData00=< --- REDACTED PARTITION DATA --- >
</pre>

#### - List of configured Roots of Trust (RoT) before the setup began.
<pre>
sampaul@ct-ub2204:~$ ksctl hsm servers list
password:
{
        "total": 0,
        "resources": []
}
</pre>

#### - Execute ksctl command as follows to configure RoT.
<pre>
sampaul@ct-ub2204:~$ ksctl hsm setup dpod \
> --reset --co-password userpin123 --partition-name SP_ROT \
> --auth-token-client-id < --- REDACTED CLIENT ID --- > \
> --auth-token-client-secret < --- REDACTED CLIENT SECRET --- > \
> --auth-token-config-uri  < --- REDACTED URL --- >\
> --cv-partition-data "< --- REDACTED PARTITION DATA --- >" \
> --server-cert-file dpod/server-certificate.pem \
> --partition-ca-cert-file dpod/partition-ca-certificate.pem \
> --partition-cert-file dpod/partition-certificate.pem
password:

WARNING - Reset is a destructive operation and will wipe all data in the CipherTrust Manager!
Are you really sure you want to continue? [y/N] y
</pre>

#### - Output displayed after the Root of Trust (RoT) configuration is complete using KSCTL.
<pre>
{

}
</pre>
+ CipherTrust Manager might take 2-3 minutes to finish resetting.
+ Once reset, you will be required to login using default username "admin" and password "admin".
+ You will be prompted to change the password upon login.

#### - List of configured Roots of Trust (RoT) after the setup completed.
<pre>
sampaul@ct-ub2204:~$ ksctl hsm servers list
password:
{
        "total": 1,
        "resources": [
                {
                    "id": "6a5f6065-209f-42c5-aeda-18b4118bc2fc",
                    "type": "dpod",
                    "config": {
                            "auth_token_client_id": "< --- REDACTED CLIENT ID --- >",
                            "auth_token_config_uri": "< --- REDACTED URL --- >",
                            "cv_partition_data": "< --- REDACTED PARTITION DATA --- >"
                    }
                }
        ]
}
</pre>

<br>

[Back to previous page](README.md)
