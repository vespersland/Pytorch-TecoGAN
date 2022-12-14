# TecoGAN
## This repository contains my pytorch implementation of the TecoGan project for video super resolution.
The code uses version 1.7.1 of pytorch. The originial code and paper can be found here:

Github: <https://github.com/thunil/TecoGAN>

Video: <https://www.youtube.com/watch?v=pZXFXtfd-Ak>

Preprint: <https://arxiv.org/pdf/1811.09393.pdf>

Supplemental results: <https://ge.in.tum.de/wp-content/uploads/2020/05/ClickMe.html>

![TecoGAN teaser image](Resources/teaser.jpg)

## Kaggle Dataset
I have an example dataset for this tecogan model [here](https://www.kaggle.com/gtownfoster/ucf101-images-for-tecogan-pytorch/activity). It contains about 441 scenes and is a good starter dataset. 

## Edits I have made to improve the model
First thing I did was I added a few resblocks to the discriminator and increased the size of the fnet model. I also removed the addition of the bilinear upsampled image to the geneator which let it upsample the image by itself and will hopefully increase the final detail. I removed the fnet model completely now. This led to much higher quality in the images with little to no loss in video continuity. I trained the new models for 2000 epochs and was finally able to break the full detail barrier. Now the gan gif and real gif are much more similar. The gan has finally learned the smaller details like the deep green of the grass and the lines in the netting. Also to train I am using the UCF101 dataset by converting it to photos using the convert2images script to train the model. 

It took my 2080 and i7 9700k about 22 hours to run 2100 epochs with 408 scenes. Now with fp16 support my model trains much faster. On my 2080 with the tensors cores it will train 2000 epochs in about 17 hours which is a huge difference. Also with half precision my gpu memory has lowered from 7gb to 4gb but it is now 5gb with the new discriminator model. 

## Colab 
To use this notebook in colab open the colab folder and follow the instructions in the readme

### Additional Generated Outputs

Our method generates fine details that 
persist over the course of long generated video sequences. E.g., the mesh structures of the armor,
the scale patterns of the lizard, and the dots on the back of the spider highlight the capabilities of our method.
Our spatio-temporal discriminator plays a key role to guide the generator network towards producing coherent detail.

<img src="Resources/tecoGAN-lizard.gif" alt="Lizard" width="900"/><br>

<img src="Resources/tecoGAN-armour.gif" alt="Armor" width="900"/><br>

<img src="Resources/tecoGAN-spider.gif" alt="Spider" width="600" hspace="150"/><br>

### Running the TecoGAN Model

Below you can find a quick start guide for running a trained TecoGAN model.

```bash
# Install PyTorch 1.7.1+,
pip3 install torch torchvision
CUDA
Python3
## This code was tested on Ubuntu but it should work on Windows
```

### Train the TecoGAN Model

#### 1. Prepare the Training Data

The training and validation dataset can be downloaded with the following commands into a chosen directory `TrainingDataPath`.  Note: online video downloading requires youtube-dl.  

```bash
# Install youtube-dl for online video downloading
pip install --user --upgrade youtube-dl

# take a look of the parameters first:
python3 dataprepare.py --help

# To be on the safe side, if you just want to see what will happen, the following line won't download anything,
# and will only save information into log file.
# TrainingDataPath is still important, it the directory where logs are saved: TrainingDataPath/log/logfile_mmddHHMM.txt
python3 dataprepare.py --start_id 2000 --duration 120 --disk_path TrainingDataPath --TEST

# This will create 308 subfolders under TrainingDataPath, each with 120 frames, from 28 online videos.
# It takes a long time.
python3 dataprepare.py --start_id 2000 --duration 120 --REMOVE --disk_path TrainingDataPath


```


#### 2. Train the Model  

```bash

# Train the TecoGAN model, based on our FRVSR model
# Please check and update the following parameters: 
# - VGGPath, it uses ./model/ by default. The VGG model is ca. 500MB
# - TrainingDataPath (see above)
# - in main.py you can also adjust the output directory of the  testWhileTrain() function if you like (it will write into a train/ sub directory by default)
python3 main.py --input_video_dir TrainingDataPath --max_epochs 10



```


#### 3. Inference 

```bash
#for dataset
python3 main.py --mode inference --input_dir_LR ../inference --inferencetype dataset --g_checkpoint generator.pt
#or for video
python3 main.py --mode inference --input_dir_LR ../inferece.mp4 --inferencetype video --g_checkpoint generator.pt

```

### Acknowledgements for the originial TecoGan project
```
@article{tecogan2020,
  title={Learning temporal coherence via self-supervision for GAN-based video generation},
  author={Chu, Mengyu and Xie, You and Mayer, Jonas and Leal-Taix{\'e}, Laura and Thuerey, Nils},
  journal={ACM Transactions on Graphics (TOG)},
  volume={39},
  number={4},
  pages={75--1},
  year={2020},
  publisher={ACM New York, NY, USA}
}
```
### Reference
[1] [The Unreasonable Effectiveness of Deep Features as a Perceptual Metric (LPIPS)](https://github.com/richzhang/PerceptualSimilarity)  
[2] [Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network](https://github.com/brade31919/SRGAN-tensorflow.git)  
[3] [gif_summary](https://colab.research.google.com/drive/1vgD2HML7Cea_z5c3kPBcsHUIxaEVDiIc)

TUM I15 <https://ge.in.tum.de/> , TUM <https://www.tum.de/>

Thank you to the researchers behind the TecoGan project for sharing your code and work.
If you have any questions feel free to email me ```dwightf404@gmail.com```
