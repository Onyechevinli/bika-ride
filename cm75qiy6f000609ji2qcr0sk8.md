---
title: "Storing Confidential document in Azure Blob storage."
datePublished: Sat Feb 15 2025 05:06:11 GMT+0000 (Coordinated Universal Time)
cuid: cm75qiy6f000609ji2qcr0sk8
slug: storing-confidential-document-in-azure-blob-storage
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1739587025487/249e54ed-dcfe-4349-b67c-de20521a2cba.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1739595896205/4587a240-9ced-4885-b35f-59e95e1c3406.png
tags: azure, azure-storage

---

How can a confidential document be stored in Azure Blob while ensuring it is not publicly accessible? However, you must share the document with an external user for 5 minutes without granting full access to the storage account.

LOG IN TO THE AZURE PORTAL:

Before getting started, please create [**free account on the portal**](https://portal.azure.com/) or use [**pay as you go**](https://go.microsoft.com/fwlink/?linkid=2234155&clcid=0x409&l=en-us) subscription to sign in to the Azure portal with your credentials.

1. From the home page, go to the search bar, type **storage accounts** and hit enter
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738714522974/76d47447-eaca-4028-93b1-42706c10f28c.png?auto=compress,format&format=webp align="left")

2. Click on **Create storage account**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738714739121/b0dcebce-4615-45f2-abb3-42dc44f2ee4f.png?auto=compress,format&format=webp align="left")

3. Create a new resource group by clicking **create new** and enter your resource group name
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738715084043/cfda323a-e156-4983-bf7e-b6f002327a70.png?auto=compress,format&format=webp align="left")

4. Come to **Instance details** and input a unique name for your **storage account name** and then select a closer **region** to you in the region tab
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739588509731/163d9db9-defb-4761-a758-75d40cfe3539.png align="center")
    

5. For the primary service, select **Azure Blob storage or Azure Data Lake Storage Gen 2**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738715713350/ff01fe88-fdb5-4f0c-a7bc-555a37ea0b78.png?auto=compress,format&format=webp align="left")

6. For the **Performance**, leave the default, **Standard**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738716054521/164af4a7-11c6-4233-8836-6aeb01b379ee.png?auto=compress,format&format=webp align="left")

7. For the redundancy choose **Geo-redundant storage (GRS)** and mark the read access to data available in the event of regional unavailability. then click the next button.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738716365927/21cced0b-0a29-4eee-8877-da79ca165ce7.png?auto=compress,format&format=webp align="left")

8. In the **Advanced** tab, for security, leave it as default.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738716608106/1ee45b42-2118-4334-8489-9e70d5b39144.png?auto=compress,format&format=webp align="left")

9. In the **Blob storage** select **Hot(…)** and click **next**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738717058316/386c3be8-4475-41fb-9991-e065cf6394fd.png?auto=compress,format&format=webp align="left")

10. In the **Networking tab**, don’t change anything, click next
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738717501941/88ba2915-d233-4bcb-bd59-2253954519d2.png?auto=compress,format&format=webp align="left")

11. In the Data protection, don’t change anything, click next
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738717625880/b97163d8-bff5-40c8-b4d5-f2b62072c1cf.png?auto=compress,format&format=webp align="left")

12. In the Encryption tab, keep it as shown and then click review + create button
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738717768052/75ce18a0-ed0d-463d-b6e4-925b76c5fe6b.png?auto=compress,format&format=webp align="left")

13. Click the **Review+create** tab, after which you review the summary page and then click **Create** button
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738717899159/6a3189e8-acfb-48de-80af-a14a5e90164f.png?auto=compress,format&format=webp align="left")

14. Once deployment is complete, click on **Go to resource** button
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738718007197/1536d7a1-c66e-4f3e-bf22-1cf837ee8e0f.png?auto=compress,format&format=webp align="left")

## Create a Container for the storage account:

1. On the overview page, click the **Data storage** dropdown menu and then click **Containers**
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738748400111/82af6f37-0b48-4ad8-8e1f-6ab5cfdb0a47.png?auto=compress,format&format=webp align="left")
    
2. Click on the “ + Container” button and then a new page with the name new container is displayed on the right corner. Input a name in the Name text field and leave the anonymous access level as private then click create button below.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738750023646/0297446d-8a51-46ef-bcfb-dda8a2d7fa04.png?auto=compress,format&format=webp align="left")

3. View the newly created container named **chijide** and double-click on it
    

4. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739593583653/1d8974ed-859a-42d0-a2ab-d27626e6d7b2.png align="center")
    
    Click on the **Upload** and a new page named upload blob will be opened on the right, drag and drop your file there and click the **Upload** button which will turn blue afterwards
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738751021212/991c7046-d225-4ac9-ab51-fe111540bb70.png?auto=compress,format&format=webp align="left")

5. The file uploaded to the blob is shown in the jpeg format, on the file right, click the 3 dots, a drop down menu is shown and then click on **Generate SAS**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738751360501/c8aa1ab4-8d6e-402e-81a0-4046e19e67af.png?auto=compress,format&format=webp align="left")

6. Click on **Overview** page, and then copy the URL in the URL text field.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738752068593/4f6f6300-44cb-47d2-be26-218f549e8ce8.png?auto=compress,format&format=webp align="left")

7. Paste the URL in a new browser and press enter. You should get the following errors below
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738752228253/2721168b-ca26-4225-bde1-703758f9ffc4.png?auto=compress,format&format=webp align="left")

8. Click on **Generate SAS** from the menu bar, then go to the **Start and expiry date/time**. Adjust the start time and expiry time to conform with how long you would like to grant access. Then click on the **Generate SAS token and URL button**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738752877607/c024ae08-1312-4ce0-81df-abff3249032c.png?auto=compress,format&format=webp align="left")

9. Copy the Blob SAS URL and paste it in a new browser
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738753089416/09ea79b4-83b9-43a3-a528-b85587c80a25.png?auto=compress,format&format=webp align="left")

10. You will be able to view the Jpeg file from the URL as shown below
    

* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739593224336/849ebebf-a8ea-4c2e-95d1-a628c7a2b244.png align="center")
    

11. Finally wait, for the expiry time to pass and refresh the URL, after which the following error should be displayed.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738753615913/27d1bea0-05d6-48c6-acdf-88951eebd3f9.png?auto=compress,format&format=webp align="left")