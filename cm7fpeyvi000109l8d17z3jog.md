---
title: "Deploying EC2 Instances with Shared EFS Storage"
datePublished: Sat Feb 22 2025 04:32:47 GMT+0000 (Coordinated Universal Time)
cuid: cm7fpeyvi000109l8d17z3jog
slug: deploying-ec2-instances-with-shared-efs-storage
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740132450684/d907915f-e388-4fd7-920b-bcbea0fd9470.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1740198714829/a5d66adb-dc26-40c8-8b90-ab224bb6f187.png
tags: step-by-step-how-to-create-and-mount-aws-efs-file-system-to-ec2-instances, deploying-ec2-instances-with-shared-efs-storage

---

I am going to explain how to launch EC2 instances, create EFS, and mount EFS on 2 EC2 instances, and also share files between the 2 instances.

## Creating of 2 EC2 instances:

1. Sign in to the AWS console with your IAM user account
    

![Input account ID, IAM username, and password.](https://cdn.hashnode.com/res/hashnode/image/upload/v1740167486705/98fa8cc0-ee2b-421d-9731-78cb30fcf9ee.png align="center")

2. On the console page, click on **EC2** or search for **EC2** in the search bar above.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740168333229/af9e5657-e9c6-4fca-8d24-d46035dea5d7.png align="center")

3. Click on the **Launch instance.**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740178638826/9798e5e6-3995-49d0-babf-831d44ac126c.png align="center")

4. insert a name for the first instance “ **CloudNative Server1** ”.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740178745751/533163da-cf0d-406e-bac4-fe08f39597dc.jpeg align="center")

5. Make sure the **Amazon Machine image** is in **Amazon Linux 2023 AMI.**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740179643898/95749fba-84f8-4290-801b-7610e6933230.jpeg align="center")

6. Make sure the **Instance type** is in **t3.micro.**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740179625489/f0d09c05-dc1a-4c55-b387-38421eb88f3c.jpeg align="center")

7. For the **Key pair**, select **Proceed without a Key pair**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740180060711/faf535da-cdd9-4ca5-93b9-35bc1151265f.jpeg align="center")

8. On the **Network setting** Tab, click on **Edit**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740180286536/c3810ae8-f51c-43d9-94b1-c3b7def0d28b.jpeg align="center")

9. Change the **Security group name** to a preferred name.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740180444881/f59b4530-1727-46b2-bc9f-93f61403617d.jpeg align="center")

10. After the above, you can go straight to click on **Launch instance**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740180640087/2896a46c-d7a0-4ff1-8f09-0bfafdd85c56.jpeg align="center")

11. Click on a Launch instance to create another instance as we need two instances to carry out our experiment.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740181241965/bd6a82b2-1621-4566-aca9-2e280f487c58.jpeg align="center")

## Creating EFS:

1. Go to the search bar, type **EFS,** and select it.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740181741640/d734bfff-aca6-4721-a3f0-e1d44130b007.jpeg align="center")

2. Click on **Create file system.**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740182061489/54843626-eae5-4f39-8764-3a8ec544d727.jpeg align="center")

3. Click on Customize.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740182415019/c639c2d5-76b9-48b2-a218-2c4325052ee3.jpeg align="center")

4. Input a preferred name.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740183105091/9a91cc84-2682-42e4-a675-d3cdb6ba5261.jpeg align="center")

5. Go straight to click on **Next**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740183112913/618abb6d-ee43-48fd-8ced-749eb733eb13.jpeg align="center")

6. Continue to click on **Next** till you get to the **Review and create** tab, then click on **Create**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740183361182/68dc8f82-4575-400a-a836-a3e526882a42.jpeg align="center")

7. Here is your EFS setup.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740183818428/7ee4e732-97cb-4bfd-aa27-4918ef306dcf.jpeg align="center")

## Setting up **Security Group:**

1. Search for **EC2** on the search bar, and go to the **Security Group** on the left under the **Network and Security** tab.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740184651296/101e4593-c06b-4f2e-b51b-28bed68e88c3.jpeg align="center")

2. Click on the **default**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740185091030/82057636-a1f3-4cc8-b8a1-6e9b0f9c334a.jpeg align="center")

3. In the d**efault security** page click on **Edit Inbound rules**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740185682516/4dd86bf5-be93-4a8d-b02d-c71d3fe11d8c.jpeg align="center")

4. In the **Inbound rules**, click on **Add rule**, then in the 2 on the diagram, search for the security group name of the first EC2, **EFS SG1**, in 3, add description to specify. Click on Add rule again and repeat the process but change the security group to **EFS SG-2** for the second EC2.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740186914946/175d0ccf-f048-4c4a-8fd4-eb07a3f26256.jpeg align="center")

5. Once you’re done, click **Save**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740187689616/a110ff95-c695-4976-85a7-de98f5eab6d5.jpeg align="center")

## Connecting the 2 EC2 Instances:

1. Go back to **EC2**, click on the **Dashboard**, then click on the first instance, and click on **Connect**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740188584645/a72dabde-85d5-4404-8d6e-2ae16a0ff405.jpeg align="center")

2. Click on **Connect**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740188973869/2ee6fe85-47d4-4ae4-9073-05bf4a139f7f.jpeg align="center")

3. Click on **Connect** again.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740189236463/f65884e1-ce11-40e1-a691-6b8fe16e1307.jpeg align="center")

4. It will take you to a new tab that will open a **VM( or server)** for the first instance. Repeat the same process for the second instance.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740189456953/fe4a246e-1f7f-41ea-8cee-38c27548acbd.jpeg align="center")

5. Here is the VM of the second instance.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740189718081/1b04a000-f9ef-491f-bcd5-01428e6aacab.jpeg align="center")

6. On both servers, we switch to the root user by typing this command “sudo su”.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740190972791/045793f1-bc95-4c6f-9f2b-deb16357df2b.jpeg align="center")

7. Install NFS on both servers by typing this command “dnf install nfs-util\*”.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740191048315/70fcbace-6242-4f7d-bed9-907f2f14b6cb.jpeg align="center")

8. Next is to download Amazon EFS utility package on both servers by typing this command “sudo yum install amazon-efs-utils”. then “Y” and hit **Enter.**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740192335257/7af62383-bec6-4687-a463-a91f79ec970f.jpeg align="center")

9. Create a folder by typing “mkdir ED” on server 1.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740193190702/dfc9d0c6-8a97-4339-9206-fbf3a60dd296.jpeg align="center")

10. Go to EFS on another tab, click on the file system and click on **Attach**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740193380963/3ad76ad8-0362-4346-ba82-ab57a686f7ea.jpeg align="center")

11. copy the **EFS mount helper**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740193497468/733498f6-fbc5-4139-bc01-0fc2199351e0.jpeg align="center")

12. Paste it on the server 1.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740193963606/305ba5a3-c1dc-462a-bc93-0c32ba9507d8.jpeg align="center")

13. Change the directory on the link to the folder name you created in server 1. Hit enter once you change the directory or folder name.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740194196436/b10c27b6-730f-47f7-b5d7-84a1d4766cc0.jpeg align="center")

14. Repeat the above process for server 2.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740194615299/f2dae547-e0ce-48a7-9efa-575731413517.jpeg align="center")

15. In server 2, open the folder “**cd ED**”, and create a file “**touch myfile.txt**”. Hit enter.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740195041753/0c26874c-189e-46f0-8e4c-25cffcff96b7.jpeg align="center")

16. Open the “myfile.txt” file with this command “vi myyfile” from server 2.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740196339281/3e209db3-32e3-4f0e-b158-f9b845a69b94.jpeg align="center")

17. First type “i” to allow you to type in the file, then type in a content on the file.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740196627933/550c148f-476d-438c-a153-702ba525d49d.jpeg align="center")

18. Once you are done typing, press the “ESC” key, then click on the “:” key, and type “wq” to exit from the file.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740197162740/621dd51b-ab35-4f10-beea-dda6259240c2.jpeg align="center")

19. Go to server 1 to check the folder you created in server 2.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740197133062/7c123b24-d007-47ca-81ec-bc3e9ca6ad85.jpeg align="center")

20. In server 1, type “ls” then “Enter”, it will show you the folder and file of server 2.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740197414545/c99e5722-1b42-421f-9f42-416d57898994.jpeg align="center")

21. Open the file with this command “vi myfile”, hit “Enter”.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740197597523/04a11ee4-dce9-494f-87d5-b7f0be68a546.jpeg align="center")

22. You can see that we can access the content of server 2 file from server 1.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740197717307/5931c8ef-ceac-407f-8c02-25b9cbac7585.jpeg align="center")

23. Exit from the file as we did in server 2 (press the “ESC” key, then click on the “:” key, and type “wq” to exit from the file).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740197959531/e41f09f5-5c6c-4021-9ae9-0edaaa3b889a.jpeg align="center")

24. We are out of the file.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740198014663/3cd411fb-43b5-4eb8-9308-3620408fc583.jpeg align="center")

25. Create a folder in server 1, by typing the command “mkdir vm-folder”, and hit “Enter”
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740198154711/162a18c7-4978-4024-8a2f-b9a82ceb6d2a.jpeg align="center")

26. Go to server 2 type in “ls” to see the folder that was created in server 1.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740198386511/e3219834-1884-46fb-aec5-312bd1c8e032.jpeg align="center")