## CCKM | AZURE KEY VAULT

In this section, I will demonstrate how to setup CKKM to manage keys in Azure Key Vault.

<br>

### Setup CCKM Application in Azure.

#### - Step 1 : Register CCKM as an application in Azure.

*The first step in this integration is to register CCKM as an application in Azure.*

- Log in to https://portal.azure.com with your Azure account with administrative privileges.
- Click on **App Registration** from the dashboard, or search for *App registration* to navigate to it.
![SS_2](https://github.com/user-attachments/assets/d67b9f64-93dd-4a4c-be43-029dbf8606d5)

- Click on **+ New registration** on the app registration page.
- Enter the required information and click **Register** option. Make sure to record the **CLIENT ID** and **TENANT ID** from the page that appears after successful registration.
![SS_3](https://github.com/user-attachments/assets/ffd97d17-6793-466f-908e-664f72ea4e65)

- On the same page, click **Certificate & Secrets** option on the left, then click **New Client secret**.
![SS_4](https://github.com/user-attachments/assets/82b6d501-346b-4fd4-8190-b6edbd29ff8e)

- Add a description and expiry on the **Add a client secret** page, then click **Add**.
- Make sure to record the *Secret ID* and *Value*.


#### - Step 2) Subscribe CCKM Application on Azure.
*The second steps is to associate the newly registered CCKM application to an active Azure Subscription.*
- From the Azure portal, select **Subscriptions** option, then choose the active subscription you wish to use.
- On the subscription page, click **Access control (IAM)** from the left-pane.
- Click the **+ Add** button, then select **Add role assignment**.

- Choose the **Reader** role from the list of built in roles, then click **Next**.
- Click **+ Select Member**, then search for and select the CCKM application you registered earlier.
- Click **Review + Assign** to complete the process.
- You should now see your CCKM application listed as a *Reader* under **Role Assignments**.
![SS_17](https://github.com/user-attachments/assets/919cfcf8-5301-4fe4-96a5-7ab13fc688b3)


#### - Step 3) Assign API Permissions for CCKM application.
*The third step is to grant API permissions to the newly registered CCKM application*.
- From the Azure portal, go to **App Registration** and select your registered CCKM application.
- Select **API Permission** option under **Manage**, then click **Add a permission**.
- Choose **Azure Key Vault**, then select **Delegated Permissions**.
- Select **user_impersonation** *(Have full access to the Azure Key Vault service)*, then click **Add Permissions**.
- Click **+Add a permission** again, and select **Azure Service Management**.
- Choose **user_impersonation** *(Access Azure Resource Manager as organization users)*, then click **Add Permission**.

#### - Step 4) Assign access permissions to CCKM Application.
*The fourth steps is to assign the appropriate permissions to the CCKM application to access the Key Vault.*
> [!NOTE]
> This step may vary depending on the type of access control in use; either Access Policies or Role-based Access Control (RBAC).

- **ACCESS POLICY**
    + From Azure portal, navigate to Key Vaults and select the relevant Key Vault.
    + Go to **Access Policies**, then click **+ Create**.
    + Tick *Select All* or choose the required operations under 
        - **Key Management Operations**.
        - **Privileged Key Operation**.
        - **Certificate Management Operations**. 
    + Click **Next**.
    + Under Principal, select the CCKM application, then click **Next**, and finally **Create**.

- **Role-based Access Control (RBAC)**
    + Click **Access control (IAM)** in the left-hand pane.
    + Under Grant access to this resource, click **+ Add role** assignment.
    + From the list of roles, select **Key Vault Administrator**, then click Next.
    + Under the **Members** section, click **+ Select members** and choose the CCKM application.
    + Click **Review + Assign** to complete the process.

<BR>

### Adding Connection to Azure Key Vault in CipherTrust Manager.

*In this section, I will explain how to connect CCKM to the CCKM application registered in Azure Cloud.*

> [!IMPORTANT]  
> If you plan to log in as a user other than the admin, that user must be assigned the Connection Admins role.

- Log in to Ciphertrust Manager.
- Click **Access Management** on the left, then select **Connections**.
- Click **Add Connection** on the right.
- From the **Select Cloud type** dropdown, choose **Azure** as the cloud provider, then click **Next**.
![SS_1](https://github.com/user-attachments/assets/50946b86-0ed1-4977-a105-230e31a7d4c0)

- Enter a suitable name and description, then **Next**.
- Enter the **Client ID** and **Tenant ID** obtained during the CCKM application registration.
- Select your **Cloud Name** and choose the **Authentication** method (either *Client Secret* or *Certificate).
- Click **Test Credentials** to validate the information. If you see *Status: OK*, Click **Next**.
![SS_6](https://github.com/user-attachments/assets/0abbe838-3ec4-4dd8-93d5-c474459ece6f)

- In the **Add Products** section, tick **Cloud Key Manager**, then click **Add connection**.
![SS_7](https://github.com/user-attachments/assets/0ff914dd-5736-4c48-8e2c-9013f0b10a9b)

- You should now see a new connection to Azure Key Vault listed under **Connections**.
![SS_8](https://github.com/user-attachments/assets/a9bff958-0dc2-4b72-b7b3-dcdfa7f7d30f)

<BR>

### Assigning an Azure Key Vault to CCKM.
*Once a connection has been successfully added, the next step is the assign a key vault to CCKM.*
- Click **Products** on the left, then select **Cloud Key Manager**.
![SS_9](https://github.com/user-attachments/assets/04ee4999-857f-4a0d-ae3c-71bd7a18d7c1)

- Under **KMS Containers**, click **Azure Key Vaults**.
![SS_13](https://github.com/user-attachments/assets/d97c1b9c-4a6e-4448-814f-74250425e418)

- Click the **Add Existing Vault** button on the right.
![SS_11](https://github.com/user-attachments/assets/91d230f5-bfb4-492f-85df-2932ffe475bd)

- Under **Azure Connection**, select the newly added connection, then choose the appropriate **Subscription**.
- Tick the checkbox for the desired **Vault Name**, then click **Save**.
![SS_14](https://github.com/user-attachments/assets/43d88a65-9399-44c9-8684-5ee97183285a)

- The selected Key Vault should now appear in the list. 
![SS_15](https://github.com/user-attachments/assets/bf4a8871-249b-4891-8726-3d52f87b0a50)

<BR>

### Viewing the keys
*After assigning a Key Vault to CCKM, you should be able to retrieve a list of objects stored in the vault.*
- Select **Cloud Keys** from the top-left section, then choose **Azure**.
- Click the **Refresh** option on the right and wait for CCKM to fetch the list of keys.
- Once the **Refresh is completed** message is displayed, you should be able to view all your keys, certificates, and secrets.
![SS_16](https://github.com/user-attachments/assets/e5e2ce05-bd9a-4778-bfb3-275366b60939)


<br>

[Back to previous page](README.md)
