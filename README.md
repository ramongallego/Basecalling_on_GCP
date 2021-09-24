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
  * I had to choose us-east4 and us-east4-b in the region/zone options so I could get access to the Tesla P4 GPU
  * Then Click on the CPU platform and GPU section and add the Tesla P4


 ![image](https://user-images.githubusercontent.com/15640943/134716176-0f222b40-f3e8-483f-80fa-68f95490c6ff.png)
 
 Now the Boot Disk - Which flavour of Linux will be easier for the installation of Nanopore? In theory anything Ubuntu should work?
 I tried before Ubuntu16 - let's try now Ubuntu18
 
 ![image](https://user-images.githubusercontent.com/15640943/134556126-f14bd2b5-72fc-4108-846e-afd8d9fd2651.png)

Click on `Change`

And now I ended with

![image](https://user-images.githubusercontent.com/15640943/134716490-5ab8296e-dbe1-4bbd-9257-44d02193292b.png)


Now it comes the section of allowing or not HTTP/HTTPS traffic. For now I'll leave it as it is. Click on `Create`

It takes ~ 2 min to set up the VM. Once it is done, you can go on

## Accessing the shell 

Click on the shell icon next to the search box in the top of your page

### Can we just install guppy?

Try with wget and the Ubuntu release. Get the link from the downloads section of Nanopore's website (propietary). I think I need the Linux-64bit GPU

![image](https://user-images.githubusercontent.com/15640943/134558607-d4c6c0a0-ddd6-480f-ba9b-638507d35ded.png)

I right click on the download Icon and `Copy link address`

Then I use wget on the shell terminal in the GCP window

![image](https://user-images.githubusercontent.com/15640943/134558944-44d31433-77f0-41b4-8fc4-103ead0958ed.png)


And follow by a 

`tar -xzvf ont-guppy-whatever.tar.gz`

Allow the shell to find it by adding `ont-guppy/bin` to the `$PATH`

## Now it finds it but it does not work

![image](https://user-images.githubusercontent.com/15640943/134560094-9052ca5a-409b-4c62-813a-8e47b07dd3be.png)

## Maybe install CUDA?

I found [this](https://towardsdatascience.com/installing-cuda-on-google-cloud-platform-in-10-minutes-9525d874c8c1)

I managed to install CUDA by following their instructions

```
curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

sudo dpkg -i cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub

sudo apt-get update

sudo apt-get install cuda-10-0 cuda-drivers -y
```

Half way through it it complains and shows youthis

![image](https://user-images.githubusercontent.com/15640943/134733576-1aae340b-720f-4edc-9020-3499bb44a14a.png)


And update both `$PATH` and `$LD_LIBRARY_PATH`

```
nano ~/.bashrc
export PATH=/usr/local/cuda-10.0/bin${PATH:+:${PATH}}$ 
export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
source ~/.bashrc
sudo ldconfig
```

That protocol follows with installing `CuDNN` - I couldn't fetch the file with `wget`, so I downloaded the file to my computer and then uploaded to the VM using the `Upload file` option in the VM's console

![image](https://user-images.githubusercontent.com/15640943/134709535-f77feede-1887-4084-989f-84095c636545.png)

After copying the file, proceed withthe CuDNN installation

```
sudo tar -xzvf cudnn-10.0-linux-x64-v7.6.5.32.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```
Now Check if CUDA worked

`nvcc -V`

That should print some verison of CUDA

### Install Tensorflow

```
sudo apt-get -y install python3-pip
sudo pip3 install --upgrade pip
sudo apt-get -y install ipython

#install numpy matplotlob
sudo pip3 install numpy scipy matplotlib ipython jupyter pandas sympy nose

#install tensorflow
sudo pip install tensorflow-gpu
```









