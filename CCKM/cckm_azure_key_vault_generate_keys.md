## CCKM | GENERATING KEYS

The notes here are a continuation of [Configuring Azure Key Vault](cckm_configure_azure_key_vault.md). Here, I will demonstrate different methods for generating keys. I will cover four different methods for generating keys.

<br>

#### - Generating Keys using Azure Key Vault's Native API.

This section demonstrates how to use CCKM to generate keys within Azure Key Vault directly. CCKM will make use of the Azure's Key Vault API to generate keys.

- Log in to Ciphertrust Manager.
- Select **Cloud Key Manager**, then choose **Azure** under **Cloud Keys** on the left.
- Click the **+ Add Key** button on the right.

![SS1](https://github.com/user-attachments/assets/f87d3f13-4044-474b-91a8-25ec7dba8a32)
- A new page titled *Add Azure Key* will appear. Under **Select Method**, choose **Create/Upload New Key Material**.
- Set the source to **Microsoft Azure (Native)**, then click **Next**.

![SS2](https://github.com/user-attachments/assets/71edb449-c37c-4b98-8090-a5e04d5fffb0)
- In the **Configure Source Key** section:
    + Enter a key label under **Azure Key Name**. 
    + Select your **Azure Key Vault**.
    + Choose the **Key Type** (RSA or EC).
    + Select **Key Size** or **Curve**.
    + Optionally **Set Activation Date** and / or **Set Expiration Date**.
    + Set the required **Key Attributes** and an optional **Tag**.
    + Click **Next** to continue.

![SS3](https://github.com/user-attachments/assets/b04bb4cb-9ee1-46d5-9403-2e880850eee3)
- If applicable, select a Rotation Schedule, otherwise click **Next**.
- Review in the **Review and Add** section, confirm the details and click **Add Key** to complete the process.

![SS5](https://github.com/user-attachments/assets/0587cdce-a61d-4439-bd4d-78bdef3373d4)
- Upon successful key generation, the newly created key will appear in the list with **Native** as the origin.

![SS6](https://github.com/user-attachments/assets/83a93f27-ad9a-4a3f-923a-5658040106fd)
- You can also confirm the presense of that key in your Azure Key Vault.

![SS7](https://github.com/user-attachments/assets/ded5da17-8132-4a0b-953e-73dc082ed71d)

<br>

#### - Generating Keys directly using Azure Key Vault.
- If you choose to generate keys directly using Azure Key Vault, that is also supported.

![SS8](https://github.com/user-attachments/assets/54f18d5a-e25f-4952-927a-69406045d311)

- Key generated directly through Azure Key Vault will appear in CCKM with **External (Unknown)** as the origin.
![SS9](https://github.com/user-attachments/assets/2f52d724-a3a2-4855-a421-268cacb72e73)

<br>

#### - Generating keys using CipherTrust Manager.
- Instead of allowing Azure to generate a key for you, you can choose to use CipherTrust Manager as the source.
- Ciphertrust Manager's RNG will be used to generate the key, which will then be synchronized with Azure Key Vault.
- When generating a key:
    - Select **CipherTrust (Local)** as the source.

![SS10](https://github.com/user-attachments/assets/d938af9c-9f5b-4712-b5d6-129247b51f94)
    - Specify the **Key Name** and **Key Size**.

![SS11](https://github.com/user-attachments/assets/33f43dc9-51a0-4cd9-a349-f48ef3e69039)
    - If applicable, select a **Rotation Schedule**, otherwise click **Next**.
    - Specify **Azure Key Label** and select the **Vault** to use.

![SS12](https://github.com/user-attachments/assets/4f1ccea3-a7e9-4bda-9a59-905037f95c53)
    - Review the information and click **Add Key** to complete the process.

![SS13](https://github.com/user-attachments/assets/50d49b0d-c8ac-47ab-a430-80bdd8edd893)
- The newly created key will appear in the list with **CCKM** as the origin.

![SS14](https://github.com/user-attachments/assets/b89d274a-66f0-4aca-ae0d-665b18f4b5cc)
- You can also confirm the presense of that key in your Azure Key Vault.

![SS15](https://github.com/user-attachments/assets/7c511660-bca5-48a3-974b-3ea978f667d7)

<br>

[Back to previous page](README.md)
