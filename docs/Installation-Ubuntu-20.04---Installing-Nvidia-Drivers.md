# Installation Ubuntu 20.04 - Installing Nvidia Drivers

## Install latest Nvidia drivers
```
sudo apt-get update

# The commands below will allow you to identify your Nvidia card model:
sudo lshw -C display

# more info here:
# https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-ubuntu-20-04-focal-fossa-linux
sudo apt install ubuntu-drivers-common

# Install Nvidia Driver, use an LTS version number
# To find which version to use, visit https://www.nvidia.com/download/index.aspx?lang=en-us
sudo apt install nvidia-driver-440
sudo reboot
```

**DO NOT FORGET TO REBOOT**

**DO NOT FORGET TO REBOOT**

**DO NOT FORGET TO REBOOT**

## Install CUDA drivers
```
# 1 - install VGA drivers 1st, see code bloc above.
# https://linuxconfig.org/how-to-install-cuda-on-ubuntu-20-04-focal-fossa-linux

# 2 - Setup Nvida CUDA repository
sudo wget -O /etc/apt/preferences.d/cuda-repository-pin-600 https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"

# 3 - At this stage all should be ready to install CUDA. Execute the following apt command:
sudo apt update
sudo apt install cuda

# 4 - Once ready, set your path to point to CUDA binaries:
echo 'export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}' >> ~/.bashrc

# 5 - Check CUDA version to confirm the installation:
```

## Fixing possible Cuda install issues

### Issue: Errors were encountered while processing
> Errors were encountered while processing:
/tmp/apt-dpkg-install-nqlSwX/015-libnvidia-compute-450_450.36.06-0ubuntu1_amd64.deb
E: Sub-process /usr/bin/dpkg returned an error code (1)
```
# https://askubuntu.com/questions/1044817/failed-installation-of-package-breaks-apt-get
sudo apt-get -o Dpkg::Options::="--force-overwrite" install --fix-broken
```

### Issue: nvidia-cuda-dev (= 10.1.243-3) but it is not going to be installed
Fix found on https://forums.developer.nvidia.com/t/cuda-install-unmet-dependencies-cuda-depends-cuda-10-0-10-0-130-but-it-is-not-going-to-be-installed/66488/7
> It may mean that one of the packages in the dependency chain cannot be installed.
>
> One of the dependent packages is freeglut3-dev
>
> This is from the ubuntu universe repo.
>
> If you have not added this repo, it will not be installable, and you will get this message about “cuda : Depends: cuda-10-0 (>= 10.0.130) but it is not going to be installed”
> 
> To solve this particular example (freeglut3-dev), add the ubuntu universe repository (google that), and install freeglut3-dev:

```
# https://forums.developer.nvidia.com/t/cuda-install-unmet-dependencies-cuda-depends-cuda-10-0-10-0-130-but-it-is-not-going-to-be-installed/66488/7
sudo add-apt-repository universe
sudo apt-get update
sudo apt-get install freeglut3 freeglut3-dev libxi-dev libxmu-dev
```
> To debug the issue more generally (if it is not due to freeglut3-dev), you have to chase the dependency chain. Each time it says a package is not going to be installed, add that package to your install line, eg.
> 
> sudo apt-get install cuda-10-0 cuda-toolkit-10-0
> 
> and keep this process up until you get to a package that can’t be installed.
`sudo apt-get install cuda-10-0 cuda-toolkit-10-0`

## Confirm the installation 

Confirm the installation by compiling an example CUDA C code. 
Save the following code into a file named eg. hello.cu

```
cd ~
nano hello.cu
```

Paste the following content in hello.cu:
```
#include <stdio.h>

__global__
void saxpy(int n, float a, float *x, float *y)
{
  int i = blockIdx.x*blockDim.x + threadIdx.x;
  if (i < n) y[i] = a*x[i] + y[i];
}

int main(void)
{
  int N = 1<<20;
  float *x, *y, *d_x, *d_y;
  x = (float*)malloc(N*sizeof(float));
  y = (float*)malloc(N*sizeof(float));

  cudaMalloc(&d_x, N*sizeof(float)); 
  cudaMalloc(&d_y, N*sizeof(float));

  for (int i = 0; i < N; i++) {
    x[i] = 1.0f;
    y[i] = 2.0f;
  }

  cudaMemcpy(d_x, x, N*sizeof(float), cudaMemcpyHostToDevice);
  cudaMemcpy(d_y, y, N*sizeof(float), cudaMemcpyHostToDevice);

  // Perform SAXPY on 1M elements
  saxpy<<<(N+255)/256, 256>>>(N, 2.0f, d_x, d_y);

  cudaMemcpy(y, d_y, N*sizeof(float), cudaMemcpyDeviceToHost);

  float maxError = 0.0f;
  for (int i = 0; i < N; i++)
    maxError = max(maxError, abs(y[i]-4.0f));
  printf("Max error: %f\n", maxError);

  cudaFree(d_x);
  cudaFree(d_y);
  free(x);
  free(y);
}
```

Test the Nvidia CUDA compiler
```
exec $SHELL
nvcc -o hello hello.cu 
./hello 
```
Expected result:
`Max error: 0.000000`

## Install Cudnn
for more info: https://developer.nvidia.com/cudnn 

To download cuDNN Library for Linux, you need to log in. 

After downloading the unzipped file, copy the cuda/include/cudnn.h file to the usr/local/cuda/include folder.

### [2.3. Installing cuDNN on Linux](https://docs.nvidia.com/deeplearning/sdk/cudnn-archived/cudnn_741/cudnn-install/index.html#installlinux)
The following steps describe how to build a cuDNN dependent program. Choose the installation method that meets your environment needs. For example, the tar file installation applies to all Linux platforms. The debian installation package applies to Ubuntu 14.04 and 16.04. **(and 20.04)**

In the following sections:
* your CUDA directory path is referred to as /usr/local/cuda/
* your cuDNN download path is referred to as <cudnnpath>

### [2.3.1. Installing from a Tar File](https://docs.nvidia.com/deeplearning/sdk/cudnn-archived/cudnn_741/cudnn-install/index.html#installlinux-tar)
1. Navigate to your <cudnnpath> directory containing the cuDNN Tar file.
2. Unzip the cuDNN package.
```
$ tar -xzvf cudnn-9.0-linux-x64-v7.tgz
```
3. Copy the following files into the CUDA Toolkit directory, and change the file permissions.
```
# below is my network share. Naviguate to the location where you unpacked the tar file
cd /mnt/nfs/ml_persona_share/nvidia/cudnn-10.0 
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```
