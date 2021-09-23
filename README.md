# Basecalling_on_GCP
Keeping a record of the choices and processes that led to having a VM that can do guppy basecalling


## Setting up an GCP account

I am getting mine through NOAA - hopefully this would be transferable to my next institution

## Getting a sandbox approved. 

THe sandbox is great to begin with because it will allow me to test that every component that I need is working, and that I can control the
usage of computing $$s, and succesfully upload and download data.

The downside is that they are located in a particular Region/node and thus the GPUs available is limited

Let's say that you have succesfully done those two things and now you have a GCP acount and a sandbox approved.

## Creating a VM

Navigate to the New VM instance / Create new instance. 

  * I will start with a very modest CPU (bc in theory all the heavy lifting is done by the GPU). 
  * I had to choose us-east4 and us-east4-b in the region/zone options so I could get access to the Tesla T4 GPU
  * Then Click on the CPU platform and GPU section and add the Tesla T4


 ![image](https://user-images.githubusercontent.com/15640943/134555383-bb79068f-3866-4cbd-b588-713b6c3d6cdb.png)
 
 Now the Boot Disk - Which flavour of Linux will be easier for the installation of Nanopore? In theory anything Ubuntu should work?
 
 ![image](https://user-images.githubusercontent.com/15640943/134556126-f14bd2b5-72fc-4108-846e-afd8d9fd2651.png)

Click on `Change`

I have also changed the disk type for SSD in case this affects the speed of writing/reading

![image](https://user-images.githubusercontent.com/15640943/134556483-e3efce7a-21f0-4205-8a40-2ce2dd51f082.png)

Now it comes the section of allowing or not HTTP/HTTPS traffic. For now I'll leave it as it is. Click on `Create`

