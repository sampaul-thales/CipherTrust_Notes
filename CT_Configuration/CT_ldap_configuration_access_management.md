## Configuring LDAP for Access Management on CipherTrust Manager

This section of my notes demonstrates how to configure LDAP on CipherTrust Manager for access management. I’ll be using OpenLDAP for this demonstration.

<br>

### The configuration of my LDAP server is as follows:
| FIELDS | VALUE | DESCRIPTION |
| --- | --- | --- |
| Server URL | ldaps://openldap.home | URL of the LDAP server. |
| Connection Name | openldap.home | user specified name for LDAP connection. |
| ROOT DN | dc=openldap,dc=home | Top level entry point. |
| BIND DN | cn=admin,dc=openldap,dc=home | identity used for LDAP authentication. |
| BIND PASSWORD | <BIND_DN_USER_PASSWORD> | password used by BIND DN identity. |
| USER DN FIELD | cn | used for reading user dn. |
| USER ID FIELD | uid | used for reading username. |
| GROUP BASE DN | ou=groups,dc=openldap,dc=home | entry point used to search for LDAP groups. |
| GROUP ID FIELD | cn | Used for identifying the group name. |
| SEARCH FILTER | (objectClass=posixAccount) | string used for querying LDAP user accounts. |
| GROUP SEARCH FILTER | (objectClass=posixGroup) | string used for querying LDAP groups. |
| ROOT CA FILES | labroot.pem:labissuer.pem | CA Certificates required for LDAP TLS verification. |
| GUID FIELD | uidNumber | used for reading the username |
| GROUP MEMBER FIELD | memberUid | used for finding users that are part of a group. |

<br>

#### - LDAP search query to list all groups in LDAP.
<pre>
sampaul@ct-ub2204:~$ ldapsearch -H ldaps://openldap.home -D "cn=admin,dc=openldap,dc=home" -W -b "dc=openldap,dc=home" "(objectClass=posixGroup)" | grep cn
Enter LDAP Password:
dn: cn=users,ou=groups,dc=openldap,dc=home
cn: users
dn: cn=ctadmin,ou=groups,dc=openldap,dc=home
cn: ctadmin
dn: cn=hsmusers,ou=groups,dc=openldap,dc=home
cn: hsmusers
</pre>

#### - LDAP search query to list all users in LDAP.
<pre>
sampaul@ct-ub2204:~$ ldapsearch -H ldaps://openldap.home -D "cn=admin,dc=openldap,dc=home" -W -b "dc=openldap,dc=home" "(objectClass=posixAccount)" | grep uid
Enter LDAP Password:
uid: spaul
uidNumber: 1000
uid: eolsen
uidNumber: 1001
uid: rdowney
uidNumber: 1002
uid: sjohansson
uidNumber: 1003
</pre>

<BR>

### Configuring LDAP using CipherTrust WebUI.

#### STEP 1: <ins>Adding LDAP connection for Access Management.</ins>
+ Log in as an administrative user (Admin).
+ Expand the "Access Management" menu on the left side, then click "LDAP".
+ Click on the "Add LDAP" option on the right, which will open a new screen titled "LDAP Connection".
+ Fill in the following mandatory fields.
	- Connection Name
	- Server URL
	- Root DN for users
	- User's login name attribute
+ Fill in the following fields to help CipherTrust search for users and groups.
	- Bind DN.
	- Server Bind Password.
        - User's distinguished name.
	- Unique Identifier Field.
	- Search filter for listing users.
	- Search filter for listing groups.
        - Root DN for groups.
	- Group id attribute for group mapping.
        - User's group membership attribute.
	- Username/Password for test.
+ For LDAPS, you need to upload the CA certificates (Server Certificates) for verification, else Tick "Disable verifying the server certificate".
+ Tick "Disable Automatic Creation of a User" if you don't want LDAP users to be automatically added upon successful login.
+ Enter Username and Password for testing and click "Test Connection.".

![ld2](https://github.com/user-attachments/assets/06afee36-9b3f-4651-9a57-160e11c7079e)

+ If the connection test is successful, click "Add LDAP" to complete the configuration.

![ld1](https://github.com/user-attachments/assets/14fefa1b-bd18-4233-8840-9de699cf5a89)

![LD3](https://github.com/user-attachments/assets/b437dec1-43c7-4298-9d26-d0eba01a63f9)


#### STEP 2: <ins>Adding User Account from LDAP.</ins>
+ Click on "Access Management" > "Users" > "Add Users".
+ Select "LDAP" as the "Connection Type".
+ Select the connection from the "LDAP Connections" dropdown.
+ Enter the "Username" and other optional details.
+ Click "Add User" to create the LDAP user.

![LD4](https://github.com/user-attachments/assets/1e507c6d-a809-4720-bf4e-25a3aad401c7)
+ Once the user account has been successfully added, you should see the updated Users screen.

![LD5](https://github.com/user-attachments/assets/7ab0f02d-0afa-4dcb-b605-1069ddc1a182)

#### STEP 3: <ins>Test login using ldap user.</ins>

+ To Log in as LDAP user, you must enter the connection name followed by the | symbol and the username. For example "openldap.home|spaul" for username.
+ Enter your LDAP user password and click "Log In".

![LD6](https://github.com/user-attachments/assets/0fc36783-787b-477b-9b81-3fd40c45b349)

<br>

### Configuring LDAP using KSCTL utility

#### - Ensure that you're using a correct username and url for CM.
<pre>
sampaul@ct-ub2204:~$ export KSCTL_USERNAME=admin
sampaul@ct-ub2204:~$ export KSCTL_URL=https://vciphertrust-i.daenerys.home
sampaul@ct-ub2204:~$ ksctl version
password:
{
        "version": "2.19.0+15144",
        "build_date": "Mon Jan  6 16:21:26 UTC 2025",
        "server_model": "CipherTrust Manager k170v",
        "server_version": "2.19.0+14195"
}
</pre>

#### - Check if an LDAP connection already exists.
<pre>
sampaul@ct-ub2204:~$ ksctl connections list
{
        "skip": 0,
        "limit": 10,
        "total": 0,
        "resources": []
}
</pre>

#### - (Optional) Test LDAP connection before adding it.
<pre>
sampaul@ct-ub2204:~$ ksctl connections test --server-url ldaps://openldap.home --strategy ldap \
--root-dn "dc=openldap,dc=home" --root-ca-files labroot.cer:labissuer.cer --user-id-field uid \ 
--bind-dn "cn=admin,dc=openldap,dc=home" --test-username spaul --name justtesting
Please enter the bind password for the connection justtesting:
Please enter the password for spaul:
OK

</pre>

#### - Creating an ldap connection.
<pre>
sampaul@ct-ub2204:~$ ksctl connections create ldap --name openldap.home --server-url ldaps://openldap.home --root-dn "dc=openldap,dc=home" \
--bind-dn "cn=admin,dc=openldap,dc=home" --bind-password BIND_PASSWORD --user-id-field uid --user-dn-field cn --search-filter "(objectClass=posixAccount)" \ --group-base-dn "ou=groups,dc=openldap,dc=home" --group-id-field cn --group-member-field memberUid --group-search-filter "(objectClass=posixGroup)" \ 
--guid-field uidNumber --disable-auto-create --root-ca-files labroot.pem:labissuer.pem
</pre>

#### - Listing the connections to check if the connection is created.
<pre>
sampaul@ct-ub2204:~$ ksctl connections list
{
        "skip": 0,
        "limit": 10,
        "total": 1,
        "resources": [
                {
                        "name": "openldap.home",
                        "strategy": "ldap",
                        "disable_auto_create": true,
                        "options": {
                                "server_url": "ldaps://openldap.home",
                                "root_dn": "dc=openldap,dc=home",
                                "uid_field": "uid",
                                "user_dn_field": "cn",
                                "bind_dn": "cn=admin,dc=openldap,dc=home",
                                "search_filter": "(objectClass=posixAccount)",
                                "group_base_dn": "ou=groups,dc=openldap,dc=home",
                                "guid_field": "uidNumber",
                                "group_filter": "(objectClass=posixGroup)",
                                "group_id_field": "cn",
                                "group_member_field": "memberUid",
                                "root_cas": [
                                        "-----BEGIN CERTIFICATE-----\nMIIFrDCCA5SgAwIBAgh\nnTCIFrFZmhlvEGcAkm1Gzw==\n-----END CERTIFICATE-----\n",
                                        "-----BEGIN CERTIFICATE-----\nMIIE7TCCAtWgAwIBAgIJAOtWRgCPvEnHNzzdVKw0z40c=\n-----END CERTIFICATE-----\n"
                                ]
                        },
                        "inherited_from": {},
                        "id": "4aeee2a2-7a46-4b70-bc7c-3ed1b07ab2a5",
                        "created_at": "2025-04-30T20:08:16.176563+00:00",
                        "updated_at": "2025-04-30T20:08:16.176563+00:00"
                }
        ]
}
</pre>

<br>

### TESTING LDAP USER LOGIN.

> [!NOTE]  
> - When **--disable-auto-create** is used during the creation of LDAP connection, new users will not be created automatically.
> - Without the **--disable-auto-create** option, you should be able log in as an LDAP user, and the account will be created automatically in CM.

#### - List users before account creation.
<pre>
sampaul@ct-ub2204:~$ ksctl users list | grep username
                        "username": "admin",
</pre>

#### - Here's how to add LDAP user manually.
<pre>
sampaul@ct-ub2204:~$ ksctl users create --userconnection openldap.home --username spaul
{
        "created_at": "2025-04-30T20:11:46.423846Z",
        "email": "spaul@openldap.home",
        "last_login": null,
        "logins_count": 0,
        "name": "spaul",
        "nickname": "spaul",
        "updated_at": "2025-04-30T20:11:46.423846Z",
        "user_id": "openldap.home|spaul",
        "username": "spaul",
        "failed_logins_count": 0,
        "account_lockout_at": null,
        "failed_logins_initial_attempt_at": null,
        "last_failed_login_at": null,
        "password_changed_at": null,
        "password_change_required": false,
        "certificate_subject_dn": "",
        "enable_cert_auth": false,
        "auth_domain": "00000000-0000-0000-0000-000000000000",
        "login_flags": {
                "prevent_ui_login": false
        },
        "auth_domain_name": "root",
        "allowed_client_types": [
                "unregistered",
                "public",
                "confidential"
        ]
}


sampaul@ct-ub2204:~$ ksctl users list | grep '\(username\|user_id\)'
                        "user_id": "local|302bdf62-91ae-4c9d-9024-4332c3d953fd",
                        "username": "admin",
                        "user_id": "openldap.home|spaul",
                        "username": "spaul",
</pre>

#### - Testing user login using LDAP.
<pre>
sampaul@ct-ub2204:~$ unset KSCTL_PASSWORD
sampaul@ct-ub2204:~$ ksctl version --user "openldap.home|spaul" --connection openldap.home
password:
{
        "version": "2.19.0+15144",
        "build_date": "Mon Jan  6 16:21:26 UTC 2025",
        "server_model": "CipherTrust Manager k170v",
        "server_version": "2.19.0+14195"
}
</pre>

<br>

### Deleting LDAP connection.
<pre>
sampaul@ct-ub2204:~$ ksctl connections delete --id "4aeee2a2-7a46-4b70-bc7c-3ed1b07ab2a5"
</pre>

<br>

[Back to previous page](README.md)
