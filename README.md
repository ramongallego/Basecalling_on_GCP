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

Click on the SSH button on the main page


https://user-images.githubusercontent.com/15640943/134752910-3c0dd667-7913-4d4d-adac-2a41424034f3.mov




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

If you chose Ubuntu 16.04

```
curl  http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_10.0.130-1_amd64.deb -o cuda.deb
```
If you chose Ubuntu 18.04

```
curl https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.0.130-1_amd64.deb -o cuda.deb

```
Now continue with installation

```

sudo dpkg -i cuda.deb

sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub

sudo apt-get update

sudo apt-get install cuda-10-0 cuda-drivers -y
```

Half way through it it complains and shows youthis

![image](https://user-images.githubusercontent.com/15640943/134733576-1aae340b-720f-4edc-9020-3499bb44a14a.png)

But press enter, add some password to confirm that it is you doing this and move on. It will finish and then the next thing is to update both `$PATH` and `$LD_LIBRARY_PATH`

```
nano ~/.bashrc
export PATH=/usr/local/cuda-10.0/bin${PATH:+:${PATH}}$ 
export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
source ~/.bashrc
sudo ldconfig
```

That protocol follows with installing `CuDNN` - I couldn't fetch the file with `wget`, so go to [NVIDIA DEVELOPER](https://developer.nvidia.com/user), and create a free account, and then download the file that matches the OS&architecture in your VM


so I downloaded the file to my computer and then uploaded to the VM using the `Upload file` option in the VM's console



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

Check if cuDNN is installed

`cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2`

That should also return the part of the file qwith the major version


### Install Tensorflow

**UPDATE 20210927**
Turns out one of my error messages was about pip3 not installing, and some other dependencies not being met.

```
sudo apt --fix-broken install

```

Should do the trick. Now continue with the installation

```
sudo apt-get -y install python3-pip
sudo pip3 install --upgrade pip
sudo apt-get -y install ipython

#install numpy matplotlob
sudo pip3 install numpy scipy matplotlib ipython jupyter pandas sympy nose

#install tensorflow
sudo pip install tensorflow-gpu
```

This sounds good and all but when I try to run guppy with GPU I get

![image](https://user-images.githubusercontent.com/15640943/134752785-75e26663-1d1d-4991-b7b1-9f24ae7e3ecc.png)








