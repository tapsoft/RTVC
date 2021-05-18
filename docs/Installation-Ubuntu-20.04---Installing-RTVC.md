# Installation Ubuntu 20.04 - Installing RTVC

## Git the code
```
# ensure you are under the right user
su USER
exec $SHELL

# create key folders
cd ~
mkdir github

cd github

git clone https://github.com/CorentinJ/Real-Time-Voice-Cloning.git

# move into the repo folder before continuing.
cd Real-Time-Voice-Cloning
```


## Setup a Conda workspace
Use a unique name for your workspace. 

If you installed miniconda3 for all users, in /opt/, it is even more important.
Ie: 
> rtvc_py373

or

> USER_rtvc_py373

Warning, python=3.7 will result in installing the latest of 3.7
be explicit if required.

```
conda deactivate

conda create --name USER_rtvc_py373 python=3.7.3

conda activate USER_rtvc_py373
```

## Install requirements
Manually install some of the libraries.
```
conda install -c conda-forge pytorch=1.2.0 torchvision=0.4.0
conda install ffmpeg
```

Edit requirements.txt and save as requirements_conda_anaconda.txt
File content:
```
tensorflow==1.15
umap-learn
visdom
librosa>=0.5.1
matplotlib>=2.0.2
numpy>=1.14.0
scipy>=1.0.0
tqdm
python-sounddevice
pySoundFile
Unidecode
inflect
# PyQt5
multiprocess
numba==0.48
```

Install the new requirements file
```
conda install -c conda-forge --file requirements_conda_anaconda.txt
```

## Installing tensorflow-gpu
```
# requirements_gpu.txt will not install
# conda install --file requirements_gpu.txt

# manually install webrtcvad with pip
pip install webrtcvad

conda install tensorflow-gpu=1.15
```
