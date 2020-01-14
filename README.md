# Dirty-Gancing
This repository contains the code and models for the Dirty GANcing Visium workshop at AMLD 2020.

## Workshop setup

Follow these steps if you wish to reproduce the exercises of the AMLD2020 Workshop

### Build the Docker image 

1) Install Docker:
    * Windows 10, Mac OS, Linux: find your OS on <https://docs.docker.com/install/#supported-platforms>
    * Windows <10: follow the instructions on <https://docs.docker.com/toolbox/toolbox_install_windows/> to install Docker Toolbox  
2) Download this repository

3) Open a terminal and move in the repository
    * Windows 10, Mac OS, Linux: use your favorite terminal application
    * Windows <10: use the Docker Quickstart Terminal installed with Docker Toolbox
4) Build the Docker image: type `docker build -t visium_amld_gancing .` (this might take a while)

    Note: If your computer has GPU capabilities and you need to install nvidia-docker. We also provide a separate Dockerfile that has GPU capabilities. For this workshop, running on the cpu will be fine though. 

### Run the image
5. Still from the repository, run the Docker image:
    * Windows Command Line: type `docker run -it -p 8888:8888 -v "%cd%":/app visium_amld_gancing`
    * Windows PowerShell / Mac OS / Linux: type `docker run -it -p 8888:8888 -v ${PWD}:/app visium_amld_gancing`
6. The container will automatically start a Jupyter notebook server
    * Mac OS, Linux: go to <http://localhost:8888>
    * Windows: go to <http://192.168.99.100:8888>


## Requirements
If you wish to run the code without using Docker, we advise to at least create a python virtual environment to install the dependencies.

Create a virtual environnement and install the dependencies:
```bash
python -m venv env
source env/bin/activate
pip install -r requirements_1st.txt
pip install -r requirements_2nd.txt
```

## Basic usage

1)  Put your source video (an `.mp4` file) in `./data/sources/{source_folder_name}/` 
and then run:
```bash
python src/data_preparation/prepare_source.py -s data/sources/{source_folder_name}
```
2) Put your target video (an `.mp4` file) in `./data/targets/{target_folder_name}/` 
and then run: 
```bash
python src/data_preparation/prepare_target.py -s data/targets/{target_folder_name}
```

3) Train the pose2vid model by running:
```bash
python src/GANcing/train_pose2vid.py -t data/targets/{target_folder_name} -r {run_name}
```
The training can be monitored:
* Using Tensorboard by running `tensorboard --logdir ./checkpoints`
* In a basic html webpage by running `python -m http.server` in `./checkpoints/{run_name}/web`

4) If the training is stopped for any reason, you can restart it from the last
checkpoint by using the same command as in step 3.

5) Once the training is finished or you stopped it, you can perform the Pose Normalization with:
```bash
python src/data_postprocessing/normalization.py -s data/sources/{source_folder_name} -t data/targets/{target_folder_name}
```

6) Finally, you can perform the pose transfer with:
```bash
python src/data_postprocessing/transfer.py -s data/sources/{source_folder_name} -r {run_name}
```

7) You can visualize the results as a gif by running:
```bash
python src/data_postprocessing/make_gif.py -s data/sources/{source_folder_name} -r results/{run_name}
```

## Extended notice

This extended instructions are for users willing to add Face Enhancement to their model

8) Start by creating the necessary files with:
```bash
python src/face_enhancer/prepare_face_enhancer_data.py -t data/targets/{target_folder_name} -r {run_name}
```

9) Train the Face Enhancer with:
```bash
python src/face_enhancer/train_face_enhancer -t data/targets/{target_folder_name} -r {run_name}
```

10) Once you are satisfied with the training results, you can stop it and perform the face_enhancement with:
```bash
python src/face_enhancer/run_face_enhancer.py -s data/sources/{source_folder_name} -t data/targets/{target_folder_name} -r {run_name}
```
This will create a new results folder in `./results/{run_name}_enhanced`, which you can use as a substitue in `make_gif.py`

## Citations

This repo is an adaptation of the paper [Everybody Dance Now](https://arxiv.org/pdf/1808.07371.pdf). If you intend to use it for anything, please consider citing the original authors in your paper:

```
@article{chan2018everybody,
  title={Everybody dance now},
  author={Chan, Caroline and Ginosar, Shiry and Zhou, Tinghui and Efros, Alexei A},
  journal={arXiv preprint arXiv:1808.07371},
  year={2018}
}
```

The code in this repo is an adapted and corrected for the AMLD workshop, and is orginally based on [CUHKSZ-TQL's repo](https://github.com/CUHKSZ-TQL/EverybodyDanceNow_reproduce_pytorch). 

It also borrows heavily from :
* [pix2pixHD](https://github.com/NVIDIA/pix2pixHD) 
* [pytorch_Realtime_Multi-Person_Pose_Estimation](https://github.com/last-one/Pytorch_Realtime_Multi-Person_Pose_Estimation)