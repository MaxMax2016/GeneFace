# Prepare the Environment
[中文文档](./install_guide-zh.md)

This guide is about building a python environment for GeneFace.

The following installation process is verified in RTX3090, Ubuntu 18.04.

# 1. Install the python libraries

```
conda create -n geneface python=3.9 -y
conda activate geneface
# install pytorch with conda, older versions also work
conda install pytorch=1.12 torchvision cudatoolkit=11.3.1 -c pytorch -c nvidia -y
# install pytorch-3d
conda install -c fvcore -c iopath -c conda-forge fvcore iopath -y
conda install -c bottler nvidiacub -y
conda install pytorch3d -c pytorch3d -y # 0.7.2 recommended
# other dependencies, including tensorflow-gpu=2.x
sudo apt-get install libasound2-dev portaudio19-dev # dependency for pyaudio
pip install -r docs/prepare_env/requirements.txt 

# install torch-ngp cuda extensions
# NOTE: you need to manually install CUDA with the same version of pytorch (in this case, v11.3.1)
# make sure your cuda path (typically /usr/local/cuda) points to a installed `/usr/local/cuda-11.3`
# then run:
bash docs/prepare_env/install_ext.sh
```

If you find any error in python package compatility, you can refer to `docs/prepare_env/geneface_*.yaml` for my tested specific package versions for different GPUs.

# 2. Prepare the 3DMM model and other data

## 2.1 Download 3DMM model

Apply BFM2009 model in [this link](https://faces.dmi.unibas.ch/bfm/index.php?nav=1-2&id=downloads).

You can obtain a file named `BaselFaceModel.tgz`, extract a file named `01_MorphableModel.mat` from it and save it into the directory `./deep_3drecon/BFM/`

## 2.2 Download PCA Basis

Download at [this link](https://drive.google.com/drive/folders/1iTopSpZucEmjWiWZIErLYiMBlZYwzil2?usp=share_link)

Extract the `Exp_Pca.bin` and place it to the `./deep_3drecon/BFM` directory.

## 2.3 Download BFM Model Front

Download at [this link](https://drive.google.com/drive/folders/1YCxXKJFfo1w01PzayhnxWSZZK5k7spSH?usp=share_link)

Extract the `BFM_model_front.mat` and place it to the `./deep_3drecon/BFM` directory.


## 2.4 Download FaceRecon Model

Download at [this link](https://drive.google.com/drive/folders/18VRcygXYOKPYvJWsl9lrF0J9PoFPk77y?usp=sharing)

Extract the `epoch_20.pth` and place it to the `./deep_3drecon/checkpoints/facerecon` directory.


## 2.5 generate files for face_tracking (used by all NeRFs)
Then run the following commandlines:
```
cd data_util/face_tracking
conda activate geneface
python convert_BFM.py
```
This will generate `data_util/face_tracking/3DMM/3DMM_info.npy`.


# 3. Verification of the Installation

```
# run the examples of deep_3drecon 
cd <root_dir>
conda activate geneface
export PYTHONPATH=./
CUDA_VISIBLE_DEVICES=0 python deep_3drecon/test.py 

# validate the bindings with GeneFace
# generate config file of deep_3drecon locally
python deep_3drecon/generate_reconstructor_opt_for_geneface.py 
CUDA_VISIBLE_DEVICES=0 python
# below are run in python console
import deep_3drecon
face_reconstructor = deep_3drecon.Reconstructor()
```


