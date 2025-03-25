---
title: "Pushing Application code to GitHub using Git-bash, Deploying the Application to Azure App-service using Azure CLI."
datePublished: Tue Jan 14 2025 23:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm8n2wus1000y0ajr6re972qw
slug: pushing-application-code-to-github-using-git-bash-deploying-the-application-to-azure-app-service-using-azure-cli
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1742814710906/463a5c78-dc58-4dce-8ade-ba0fa631ed9e.png

---

To actualize this task, we need to run it in 3 phases.

### Phase 1 is Version Control with GitHub:

To do the Version Control, we clone the starter project (or create a new one). Initialize a Git repository using Git Bash. Stage, commit, and push the code to a new GitHub repository.

1. Sign in to GitHub or sign up, if you have not registered.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742302484926/dd469b06-50f8-4d46-affe-54d6dc4507fa.jpeg align="center")

2. Input your username and password.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742303763172/cee8dcf6-c60d-4de4-b73a-d599d714caa7.jpeg align="center")

3. Click on **NEW (green button)** to create a new repository or the plus sign (+) button, then click on **New repository** in the drop-down.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742304065981/f727ccac-33a5-4448-8079-ddf4c9c90a14.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742304394874/a4622e5a-990f-42c1-916e-0c00fa175b6e.jpeg align="center")

4. Input the name of the repository, then add a description to explain what you want to do, then for this, make sure public is selected, then click on **Add a README file**. Click on **Create repository**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742314538196/09263b36-b6fb-4a0f-b99b-170d1524f2aa.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742314556221/197808b7-bfe4-4f1c-b83f-fb52bd320f49.jpeg align="center")

5. It would take you to the repository page.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742315224405/77343f79-b8a8-4c84-96c3-2a58fbaf9344.jpeg align="center")

6. Go to your file explorer and search for Git bash, to open the Git terminal.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742315346994/3bb8afcf-4618-4d9a-95f1-0745ebb446ec.jpeg align="center")

7. Then type in the following command:
    
    a. “**git config - -global user.name (name of the GitHub username)**“, then hit **Enter**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742327137485/758d1e52-e9a6-46c3-89cf-451fe495143d.jpeg align="center")

a. Type: “**git config - -global user.email (email of the GitHub account)**“, then press **Enter.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742327495230/9bd3a791-73f5-4e9d-9299-aba019afb8c3.jpeg align="center")

b. Create a directory/folder, by inputting this command; “**mkdir bika-ride**”, hit **Enter**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742327593951/57d9bb72-8551-4e1f-bb23-41e98fc4be82.jpeg align="center")

c. Run this command to enter into the Folder/directory; “**cd bika-ride**“, press **Enter**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742359533563/0721c468-933a-4a25-907c-472e5addd652.jpeg align="center")

d. Then we initialized by typing the following command; “**git init**“, press Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742359946738/305f1272-f4ba-4f6f-89ac-c719859a63f5.jpeg align="center")

e. Create the index.html file, by typing the following codes; “**touch index.html**“, and hit Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742360290710/dc9d18a9-54d2-4881-ae35-c59ba6fa96fa.jpeg align="center")

f. Open the index.html file by tying these codes; “**cd index.html**“, and click Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742360428337/8a119ecd-68fc-4376-9bb1-9e1008a4ba84.jpeg align="center")

g. When you open the index.html file, it takes you to the file.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742360629848/575b4496-ac24-466e-a865-2426802f4d6f.jpeg align="center")

h. Then we input the codes for the app by pasting the code.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742362352601/1cdd1894-6027-4d9f-a23a-c6c6b0e4f6c9.jpeg align="center")

i. Once the code is inputted, move out of the file by pressing the “**Esc**”, then press the colon“:“ and type “**wq**“ to exit the file.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742364460626/364195ea-c9ba-45fb-b109-09b34ce41ef7.jpeg align="center")

j. Input the following command, ‘**git remote add origin** (**git repository URL, you get it by clicking on code, in the drop-down, copy the URL and then paste it in the git terminal**)’, and hit Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742820838442/a464f19e-b6d4-428c-88a0-610ca30d8720.jpeg align="center")

k. We add the code by typing ‘**git add index.html**’, and Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742365089216/e4e355ab-147a-4235-a101-b903ac6882d1.jpeg align="center")

l. Type the command, ‘**git status**’, and Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742367683724/e5757b66-2d6f-4dfa-a7b8-51950841e220.jpeg align="center")

m. Type the following command to commit, ‘**git commit -m “first commit“** ‘. press Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742381150781/bbcd1dbc-cf2c-452d-a1f7-ef3d400806a3.jpeg align="center")

n. Type in ‘**git status**’, and hit Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742381353934/741bb6e4-e63e-46d7-ab5d-6fdbe2d61d3f.jpeg align="center")

o. We then push the origin to the master, ‘**git push origin master**’, and hit Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742381813093/bd4d7207-1e0e-4e1e-8292-b1b98d470ab2.jpeg align="center")

8. We then go to the GitHub site to check if it’s uploaded the file, then click on ‘**compare & pull request**‘.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742382423341/9459cb0d-8d6f-4c20-a8d4-bfdd84a53ad7.jpeg align="center")

a. You can now have access to the code uploaded.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742384645988/e29d65aa-64ff-48b8-8bb9-97404854a668.jpeg align="center")

### Phase 2 is Azure App Service Setup.

We do that by using Azure CLI to create a Resource Group and App Service Plan. Create an Azure Web App using Azure CLI.

1. Open up Power Shell on your device, type in ‘**az login**’, and press the Enter key.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742795926080/45517220-1520-4dad-a341-5b6358ab6975.jpeg align="center")

2. Once you hit Enter, an account screen will pop up, then you will sign in to the Azure account with an active subscription.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742705419082/ef5836f9-598f-4129-ac48-6cf49a4a0542.jpeg align="center")

3. It will prompt you to select a subscription tenant from your account or your just hit Enter key to continue.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742705501180/f9dbcb36-915f-4166-9d6b-50faed0ce802.jpeg align="center")

4. With the below image, you have successfully logged into your Azure account from Azure CLI.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742705634868/18446f44-a6b3-4df0-9711-f168af50c4d0.jpeg align="center")

5. We then move to create a resource group by typing ‘**az group create - -name (select any name) - -location (choose any location)**‘and then hit Enter.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742706203282/e9af00d9-b688-4125-ba5e-179fe806489a.jpeg align="center")

6. The following command is to create the application-service, so we type ‘**az appservice plan create - -name (select a name) - -resource-group (name of initial resource-group)**‘ and hit Enter.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742795291361/682765a7-4d5e-4eab-9a0c-5a32b08d7c08.jpeg align="center")

7. Then we create a web-app by typing the following command, ‘**az webapp create - -name (put in a preferred name) - -resource-group (name of initial resource-group) - -plan (name of initial app service)**‘, and hit Enter.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742801202094/7707c069-b5f5-4044-9ded-89ac295c319d.jpeg align="center")
    

### Phase 3 is Deployment.

We deploy the web application from GitHub to Azure using Azure CLI, and test the deployment by accessing the application URL.

1. We then deploy the application by entering the following command, ‘**az webapp deployment source config - -name (name of initial webapp) - -resource-group (name of initial resource-group) - -repo-url (copy the url from the GitHub, see image 1a below) - -branch master - -manual-integration**‘ and hit Enter.
    
    ![1a](https://cdn.hashnode.com/res/hashnode/image/upload/v1742803262785/2befa7f4-21fe-4a22-9893-32d7fd0042a7.jpeg align="center")
    
    1a
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742802272834/fa71fd5d-6713-4ae2-bbcb-45b72ac413b4.jpeg align="center")
    
    2. Once the application is deployed, go to the Azure portal and go to the App service created, click on the web app, and in the overview click on the Default domain to take you to the Application site.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742815414896/4626863b-c713-4225-a131-216ef8be34a4.jpeg align="center")
    
    3. When you click on the URL, it takes you to the Application site.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742820037290/1410cd57-75c3-49ce-b911-0f346ac52f67.jpeg align="center")