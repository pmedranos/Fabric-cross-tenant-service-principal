# How to Add and Use Cross-Tenant Service Principals in Fabric

## 1. Setting Up the Service Principal in Tenant A (Fabric Tenant)

### Step 1: Create an Enterprise Application
**A-** Open Azure Portal and navigate to Tenant A (where Fabric is hosted).  
**B-** Go to **Azure Active Directory > App Registrations**.  
**C-** Click **+ New Registration** to create a new Enterprise Application.

### Step 2: Configure Authentication
**A-** In App Registrations, locate the newly created application.  
**B-** Navigate to **Authentication** and click **Add a Platform**.  
![enterprise-app](images/enterprise-app-platform-config.png)

**C-** On the next screen, select **Web**.  
![enterprise-app-web](images/enterprise-app-platform-web.png)

**D-** Copy and paste the following **Redirect URI**:

```
https://login.microsoftonline.com/common/oauth2/nativeclient
```

Click **Configure**.  

Still within Authentication Scroll down and you will see the next:

![Authentication Settings](images/enterprise-app-platform-authentication.png)

Make sure you choose the options that you see on the screenshot. This should be all within Authentication.

### Step 3: Assign API Permissions
Go to **API permissions**.  
Click **Add a permission** and choose the necessary APIs.  
Ensure the following permissions are included:  
![API Permissions](images/api-permissions.png)

After adding permissions, click **Grant admin consent**.  
Remove any unnecessary permissions, such as `Item.Read.All`, to improve security.

### Step 4: Create a Client Secret
Navigate to **Certificates & secrets**.  
Click **+ New client secret** and store the generated secret securely.  
You will not be able to retrieve it again.

### Step 5: Create a Security Group and Add the SPN
Go to **Microsoft Entra ID > Groups**.  
Click **+ New group**, and set the type to **Security**.  
Add the SPN as a member:  
![Add SPN to Group](images/add-spn-group.png)

---

## 2. Enabling Permissions in Fabric

Go to the **Fabric Admin Portal**.  
Navigate to **Admin settings** and enable the following:

- ✅ Service principals can use Fabric APIs  
- ✅ Service principals can access read-only admin APIs  
- ✅ Service principals can access admin APIs used for updates  

Add the security group created earlier to these permissions.  
![Fabric Admin Settings](images/fabric-admin-settings.png)

---

## 3. Grant Access to the Fabric Workspace

In Fabric, navigate to the workspace where the Data Warehouse resides.  
Add the SPN as a **Viewer**:  
![Add SPN to Workspace](images/add-spn-workspace.png)

---

## 4. Registering the Service Principal in Tenant B

In Tenant B (ADF side), use the following URL to register the SPN from Tenant A:

```
https://login.microsoftonline.com/TENANT_B_ID/adminconsent?client_id=APP_CLIENT_ID
```

Replace:
- `TENANT_B_ID` with the tenant ID from Tenant B
- `APP_CLIENT_ID` with the client ID from the service principal in Tenant A

Accept the permissions prompt to finalize the setup.

---

## 5. Connecting from Azure Data Factory (ADF)

### Required Information:
- Tenant A ID
- Application (Client) ID
- SPN Client Secret

### Retrieve Fabric Workspace & Warehouse IDs

In Fabric, click **... > Copy SQL Endpoint** on the target Data Warehouse.  
The URL format will look like:

```
https://app.fabric.microsoft.com/groups/<workspace_ID>/warehouses/<warehouse_ID>
```

Use this information to build the ADF **Linked Service**:  
![ADF Linked Service Example](images/adf-linked-service.png)

---

## ✅ Done!
Your cross-tenant integration is now ready using service principals between Azure Data Factory and Microsoft Fabric.
# Fabric-cross-tenant-service-principal
Documentation of fabric cross tenant connection using service principals solution

How to Add and Use Cross-Tenant Service Principals in Fabric
1. Setting Up the Service Principal in Tenant A (Fabric Tenant)
Step 1: Create an Enterprise Application
1.	Open Azure Portal and navigate to Tenant A (where Fabric is hosted).
2.	Go to Azure Active Directory > App Registrations.
3.	Click + New Registration to create a new Enterprise Application.
Step 2: Configure Authentication
1.	In App Registrations, locate the newly created application.
2.	Navigate to Authentication and click Add a Platform.
 
3.	In the next screen, select Web.
 
4.	Copy and paste the following Redirect URI:
https://login.microsoftonline.com/common/oauth2/nativeclient
5.	Click Configure
Still within Authentication Scroll down and you will see the next:
 
Make sure you choose the options that you see on the screenshot. This should be all within Authentication.
Step 3: Assign API Permissions
1.	Go to API permissions.
2.	Click Add a permission and choose the necessary APIs.
3.	Make sure to match the permissions as per the screenshot.
4.	After adding permissions, click Grant admin consent.
5.	For security, consider removing unnecessary permissions, such as Item.Read.All, to minimize access.
 

Step 4: Create a Client Secret
1.	Navigate to Certificates & secrets.
2.	Click + New client secret and create a secret.
3.	Store the secret securely as it will not be visible after creation.

Step 5: Create a Security Group and Add the SPN
1.	Go to Microsoft Entra ID > Groups.
2.	Click + New group.
3.	Group type should be set to Security.
 
4.	Click on No Members selected and add the SPN to the security group.

2. Enabling Permissions in Fabric
1.	Open Fabric Admin Portal.
2.	Navigate to Admin settings and enable the following permissions:
o	✅ Service principals can use Fabric APIs
o	✅ Service principals can access read-only admin APIs
o	✅ Service principals can access admin APIs used for updates
3.	Add the security group created in the previous step to these permissions.

3. Grant Access to the Fabric Workspace
1.	In Fabric, go to the workspace where the Data Warehouse lives.
2.	Add the SPN as a user and grant it Viewer access.
3.	This is required for the SQL Endpoint to work properly.

4. Registering the Service Principal in Tenant B
1.	In Tenant B (where ADF is hosted), register the SPN from Tenant A using the following URL:
https://login.microsoftonline.com/TENANT_B_ID/adminconsent?client_id=APP_CLIENT_ID
TENANT_B_ID is the subscription ID of tenant B
APP_CLIENT_ID is Tenant's A client ID from the service principal.
2.	Accept the permissions

5. Connecting to Azure Data Factory (ADF)
Required Information
To connect ADF to Tenant A’s Fabric environment, you will need:
•	Tenant A ID (Find it in the App overview)
•	Application (Client) ID
•	SPN Client Secret
 
Retrieve Fabric Workspace & Warehouse IDs
1.	In Fabric, go to the Data Warehouse.
2.	Click on the ... (More options) and select Copy SQL Endpoint.
 
3.	Extract the Workspace ID and the Warehouse ID from the Fabric URL:
https://app.fabric.microsoft.com/groups/<workspace_ID>/warehouses/<warehouse_ID>
This is how the Linked Service within ADF should look like:
 







