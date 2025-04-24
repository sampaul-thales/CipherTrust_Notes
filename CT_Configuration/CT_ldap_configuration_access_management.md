## Configuring LDAP for Access Management on CipherTrust Manager

This section of my notes demonstrates how to configure LDAP on CipherTrust Manager for access management. I’ll be using OpenLDAP for this demonstration.

<br>

### The configuration of my LDAP server is as follows:
| FIELDS | VALUE | DESCRIPTION |
| --- | --- | --- |
| Server URL | ldaps://openldap.home | URL of the LDAP server. |
| Connection Name | openldap.home | User specified name for LDAP connection. |
| ROOT DN | dc=openldap,dc=home | Top level entry point. |
| BIND DN | cn=admin,dc=openldap,dc=home | Identity used for LDAP authentication. |
| BIND PASSWORD | <BIND_DN_USER_PASSWORD> | Password used by BIND DN identity. |
| USER DN FIELD | dn | For reading username. |
| USER ID FIELD | uid | For reading user id. |
| GROUP BASE DN | ou=group,dc=openldap,dc=home | Entry point used to search for groups. |
| GROUP ID FIELD | cn | Used for identifying the group name. |
| SEARCH FILTER | (objectClass=person) | String used for querying LDAP for accounts. |
| GROUP SEARCH FILTER | (objectClass=posixGroup) | String used for querying LDAP for groups. |
| ROOT CA FILES | labroot.pem:labissuer.pem | CA Certificates required for LDAP TLS verification. |
| GUID FIELD | uidNumber | used for finding users who are part of a group. |
| GROUP MEMBER FIELD | gidNumber | used for finding group that a user is part of. |

#### - LDAP search query to list all groups in LDAP.
<pre>
sampaul@ct-ub2204:~$ ldapsearch -H ldaps://openldap.home -D "cn=admin,dc=openldap,dc=home" -W -b "dc=openldap,dc=home" "(objectClass=posixGroup)" | grep cn
Enter LDAP Password:
dn: cn=administrator,ou=group,dc=openldap,dc=home
cn: administrator
dn: cn=hsmusers,ou=group,dc=openldap,dc=home
cn: hsmusers
dn: cn=user,ou=group,dc=openldap,dc=home
cn: user
dn: cn=cm_admin,ou=group,dc=openldap,dc=home
cn: cm_admin
</pre>

#### - LDAP search query to list all users in LDAP.
<pre>
sampaul@ct-ub2204:~$ ldapsearch -H ldaps://openldap.home -D "cn=admin,dc=openldap,dc=home" -W -b "dc=openldap,dc=home" "(objectClass=Person)" | grep dn
Enter LDAP Password:
dn: cn=Sam Paul,ou=People,dc=openldap,dc=home
dn: cn=Tony Stark,ou=People,dc=openldap,dc=home
dn: cn=Natasha Romanoff,ou=People,dc=openldap,dc=home
dn: cn=Bruce Wayne,ou=People,dc=openldap,dc=home
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
+ If the connection test is successful, click "Add LDAP" to complete the configuration.

#### STEP 2: <ins>Adding User Account from LDAP.</ins>
+ Click on "Access Management" > "Users" > "Add Users".
+ Select "LDAP" as the "Connection Type".
+ Select the connection from the "LDAP Connections" dropdown.
+ Enter the "Username" and other optional details.
+ Click "Add User" to create the LDAP user.
+ Once the user account has been successfully added, you should see the updated Users screen.

#### STEP 3: <ins>Test login using ldap user.</ins>

+ To Log in as LDAP user, you must enter the connection name followed by the | symbol and the username. For example "openldap.home|spaul" for username.
+ Enter your LDAP user password and click "Log In".

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
sampaul@ct-ub2204:~$ ksctl connections test ldap --server-url ldaps://openldap.home --strategy ldap --root-dn "dc=openldap,dc=home" \
--root-ca-files labroot.pem:labissuer.pem --user-id-field uid --bind-dn "cn=admin,dc=openldap,dc=home" \
--test-username spaul --name justtesting
Please enter the bind password for the connection justtesting:
Please enter the password for spaul:
OK
</pre>

#### - Creating an ldap connection.
<pre>
sampaul@ct-ub2204:~$ ksctl connections create ldap --name openldap.home --server-url ldaps://openldap.home \
--root-dn "dc=openldap,dc=home" --bind-dn "cn=admin,dc=openldap,dc=home" --bind-password <BIND_PASSWORD> \
--user-id-field uid --user-dn-field dn --search-filter "(objectClass=person)" --group-member-field gidNumber \
--group-base-dn "ou=group,dc=openldap,dc=home" --group-id-field cn --group-search-filter "(objectClass=posixGroup)" --guid-field uidNumber \
--root-ca-files labroot.pem:labissuer.pem
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
                        "options": {
                                "server_url": "ldaps://openldap.home",
                                "root_dn": "dc=openldap,dc=home",
                                "uid_field": "uid",
                                "user_dn_field": "dn",
                                "bind_dn": "cn=admin,dc=openldap,dc=home",
                                "search_filter": "(objectClass=person)",
                                "group_base_dn": "ou=group,dc=openldap,dc=home",
                                "guid_field": "uidNumber",
                                "group_filter": "(objectClass=posixGroup)",
                                "group_id_field": "cn",
                                "group_member_field": "gidNumber",
                                "root_cas": [
                                        "-----BEGIN CERTIFICATE-----\nMIID4DCCAsigAwIBAgIUG9yEQTgKNVC/+eM6np6kAv6oOblSBwrepAWGxhmb\n-----END CERTIFICATE-----\n",
                                        "-----BEGIN CERTIFICATE-----\nMIIEEzCCAvugAwIBAgIIEuijT7cK8Aq56qHOA7KCLaJ1mw7Uoj4\n-----END CERTIFICATE-----\n"
                                ]
                        },
                        "inherited_from": {},
                        "id": "196589c3-2aae-4bae-8abd-23b6acb80a1d",
                        "created_at": "2025-04-24T18:05:02.331797+00:00",
                        "updated_at": "2025-04-24T18:05:02.331797+00:00"
                }
        ]
}
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

#### - Upon succesfull login, you should be able to see a new user registered for access mananagement.
> [!NOTE]  
> When **--disable-auto-create** is used during creation of LDAP connection, new users will not be created automatically.
> In that case, you will need to add that user manually.

<pre>
sampaul@ct-ub2204:~$ export KSCTL_USERNAME=admin
sampaul@ct-ub2204:~$ ksctl users list | grep 'name\|email'
                        "email": "admin@local",
                        "name": "admin",
                        "nickname": "admin",
                        "username": "admin",
                                        "name": "root"
                        "auth_domain_name": "root",
                        "email": "spaul@openldap.home",
                        "name": "spaul",
                        "nickname": "spaul",
                        "username": "spaul",
                        "auth_domain_name": "root",
</pre>

<br>

### Deleting LDAP connection.
<pre>
sampaul@ct-ub2204:~$ ksctl connections delete --id "196589c3-2aae-4bae-8abd-23b6acb80a1d"
</pre>

<br>

[Back to previous page](README.md)
