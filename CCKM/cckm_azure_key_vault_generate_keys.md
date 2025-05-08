## CCKM | GENERATING KEYS

The notes here are a continuation of [Configuring Azure Key Vault](cckm_configure_azure_key_vault.md). Here, I will demonstrate different methods for generating keys. I will cover four different methods for generating keys.

<br>

### Generating Keys using Azure Key Vault's Native API.

This section demonstrates how to use CCKM to generate keys within Azure Key Vault directly. CCKM will make use of the Azure's Key Vault API to generate keys.

- Log in to Ciphertrust Manager.
- Select **Cloud Key Manager**, then choose **Azure** under **Cloud Keys** on the left.
- Click the **+ Add Key** button on the right. --SS1--
- A new page titled *Add Azure Key* will appear. Under **Select Method**, choose **Create/Upload New Key Material**.
- Set the source to **Microsoft Azure (Native)**, then click **Next**. --SS2--.
- In the **Configure Source Key** section:
    + Enter a key label under **Azure Key Name**. 
    + Select your **Azure Key Vault**.
    + Choose the **Key Type** (RSA or EC).
    + Select **Key Size** or **Curve**.
    + Optionally **Set Activation Date** and / or **Set Expiration Date**.
    + Set the required **Key Attributes** and an optional **Tag**.
    + Click **Next** to continue. --SS3--.
- If applicable, select a Rotation Schedule, otherwise click **Next**.
- Review in the **Review and Add** section, confirm the details and click **Add Key** to complete the process. --SS5--.
- Upon successful key generation, the newly created key will appear in the list with **Native** as the origin. --SS6--.
- You can also confirm the presense of that key in your Azure Key Vault. --SS7--.

<br>

### Generating Keys directly using Azure Key Vault.
- If you choose to generate keys directly using Azure Key Vault, that is also supported. --SS8--
- Key generated directly through Azure Key Vault will appear in CCKM with **External (Unknown)** as the origin. --SS9--

<br>

### Generating keys using CipherTrust Manager.
- Instead of allowing Azure to generate a key for you, you can choose to use CipherTrust Manager as the source.
- Ciphertrust Manager's RNG will be used to generate the key, which will then be synchronized with Azure Key Vault.
- When generating a key:
    - Select **CipherTrust (Local)** as the source. --SS10--.
    - Specify the **Key Name** and **Key Size**. --SS11--.
    - If applicable, select a **Rotation Schedule**, otherwise click **Next**.
    - Specify **Azure Key Label** and select the **Vault** to use. --SS12--.
    - Review the information and click **Add Key** to complete the process. --SS13--.
- The newly created key will appear in the list with **CCKM** as the origin. --SS14--.
- You can also confirm the presense of that key in your Azure Key Vault. --SS15--.

<br>

[Back to previous page](README.md)
