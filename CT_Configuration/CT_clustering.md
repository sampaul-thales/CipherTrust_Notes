## Clustering CipherTrust Manager

This section of my notes describes how to set up a cluster of two or more CipherTrust Managers. A **CLUSTER** is a group of CipherTrust Manager instances that work together synchronously, sharing information with each other. The primary objective of using a cluster is to provide High Availability, thereby improving uptime. It can also help distribute the load among nodes to enhance performance.

<BR>

> [!NOTE]
> + All nodes in a cluster communicate over port 5432. A firewall rule must be created to allow bidirectional communication among all nodes. 
> + All nodes must be synchronized to the same time; otherwise, communication between nodes may fail. NTP server is recommended.
> + All nodes must use the same firmware version.
> + Maximum size of a cluster is 20.

> [!IMPORTANT]
> Clustering CipherTrust Manager also changes the lock codes used for licence activation.
> All nodes in the cluster will have the same Connector Lock Code once clustering is complete.
> Please ensure that clustering is performed before activating the licences.

<BR>

### Creating cluster using CipherTrust Manager's Web UI.

#### <ins>Part 1: Creating a Cluster</ins>

+ Log in as an administrative user (Admin).

+ Expand the "Admin Settings" menu on the left side, then click on "Cluster".

+ Click on the "Manage Cluster" option on the right, and select "Add Cluster".

+ Click "Next" on the "Add Cluster" screen.

+ On the following screen, enter the Private IP or the hostname to be used for all communication between nodes.

+ Enter the public address or hostname for accessing that specific node, and click "Add Cluster".

+ At this point, your cluster should be ready for use and prepared to accept another node to join.


#### <ins>Part 2: Join node to a cluster</ins>

+ Log in as an administrative user if you're logged out.

+ Expand the "Admin Settings" menu on the left side, then click on "Cluster".

+ Click on the "Manage Cluster" option on the right, and select "Add Node".

+ Enter the IP address or the hostname used for accessing the WEB UI of the node you're adding. Click "Add Node".

+ Clicking on "Add Node" should open a login screen for the joining node. Enter the administrative username and password to log in.

+ Upon successful login, you should see the "Join Node to a Cluster" screen. Click on "Join".

+ It may take a few seconds or a minute for the node to join the cluster. Click on "Finish" once the process is completed.

+ After the new node addition is complete, you should see the newly added node on the cluster screen.

<br>

### Creating Cluster using KSCTL utility.

> [!TIP]
> Instead of entering the password for every KSCTL command, you could store it as KSCTL_PASSWORD environment variable.
<pre>
sampaul@ct-ub2204:~$ export KSCTL_PASSWORD=--my_ciphertrust_manager_password--
</pre>

#### - Check if a cluster already exists.
<pre>
sampaul@ct-ub2204:~$ ksctl cluster info
{
        "nodeID": "",
        "status": {
                "code": "none",
                "description": "not clustered"
        }
}
</pre>

#### - To create a cluster.
<pre>
sampaul@ct-ub2204:~$ ksctl cluster new --host 10.164.50.16 --public-address https://vCipherTrust-i.daenerys.home
{
        "nodeID": "78f3f9e693544460aae137cb81be713c",
        "status": {
                "code": "r",
                "description": "ready"
        },
        "nodeCount": 1
}
</pre>

#### - Display list of nodes in a cluster.
<pre>
sampaul@ct-ub2204:~$ ksctl cluster nodes list
{
        "skip": 0,
        "limit": 256,
        "total": 1,
        "resources": [
                {
                        "nodeID": "78f3f9e693544460aae137cb81be713c",
                        "status": {
                                "code": "r",
                                "description": "ready"
                        },
                        "host": "10.164.50.16",
                        "port": 5432,
                        "state": null,
                        "isThisNode": true,
                        "publicAddress": "https://vCipherTrust-i.daenerys.home"
                }
        ]
}
</pre>

#### - Add another node to a cluster.
<pre>
sampaul@ct-ub2204:~$ ksctl cluster fulljoin --member=10.164.50.16 --newnodehost=10.164.50.17 --newnodeurl=https://vciphertrust-ii.daenerys.home --newnodeuser=sampaul --newnodepass=$KSCTL_PASSWORD
When you add a node to a cluster, the existing data of the node is deleted.

Are you sure want to join? [y/N] y
Checking if member is ready
Checking if joining node is ready
Getting CSR from joining node
Getting Certificate and CA Chain from member node
Joining new node to cluster
</pre>

#### - Get list of nodes in a cluster.
<pre>
sampaul@ct-ub2204:~$ ksctl cluster nodes list | grep host
                        "host": "10.164.50.16",
                        "host": "10.164.50.17",
</pre>

<br>

[Back to previous page](README.md)
