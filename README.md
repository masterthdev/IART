# Enhancing Video Super-Resolution via Implicit Resampling-based Alignment 

[![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/an-implicit-alignment-for-video-super/video-super-resolution-on-reds4-4x-upscaling)](https://paperswithcode.com/sota/video-super-resolution-on-reds4-4x-upscaling?p=an-implicit-alignment-for-video-super)
[![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/an-implicit-alignment-for-video-super/video-super-resolution-on-vid4-4x-upscaling)](https://paperswithcode.com/sota/video-super-resolution-on-vid4-4x-upscaling?p=an-implicit-alignment-for-video-super)

**Updates:**

- January 18, 2024: We have enhanced our [updated paper](https://github.com/kai422/IART/blob/main/arxiv.pdf) with improved illustrations, along with speed comparisons and additional qualitative analyses!



https://github.com/kai422/IART/assets/31679416/b32abb4a-9c27-43ce-bef6-1bde073ada18


This is an offical PyTorch implementation of 


>**An Implicit Alignment for Video Super-Resolution.**  
[[Paper]](https://github.com/kai422/IART/blob/main/arxiv.pdf)       
[Kai Xu](https://kai422.github.io/), Ziwei Yu, Xin Wang, Michael Bi Mi, [Angela Yao](https://www.comp.nus.edu.sg/~ayao/)    
Computer Vision and Machine Learning group, NUS.   

Video super-resolution commonly uses a frame-wise alignment to support the propagation of information over time. The role of alignment is well-studied for low-level enhancement in video, but existing works have overlooked one critical step -- re-sampling.
Most works, regardless of how they compensate for motion between frames, be it flow-based warping or deformable convolution/attention, use the default choice of bilinear interpolation for re-sampling. However, bilinear interpolation acts effectively as a low-pass filter and thus hinders the aim of recovering high-frequency content for super-resolution.

This paper studies the impact of re-sampling on alignment for video super-resolution. Extensive experiments reveal that for alignment to be effective, the re-sampling should preserve the original sharpness of the features and prevent distortions. From these observations, we propose an implicit alignment method that re-samples through a window-based cross-attention with sampling positions encoded by sinusoidal positional encoding. The re-sampling is implicitly computed by learned network weights. Experiments show that the proposed implicit alignment enhances the performance of state-of-the-art frameworks on both synthetic and real-world datasets.

<p align="center">
  <img width="800" src="method.png">
</p>

*A comparison diagram between bilinear interpolation and our implicit alignment. Bilinear interpolation fixes aggregation weight W_bi. Implicit alignment learns affinity through the cross-attention module to calculate the final result. Red grids denote the source frame, purple grids denote the target frame, and blue grids denote the aligned frame.*

## Results 
| REDS4 | Frames| PSNR    | SSIM  | Download |
|:-----|:-----:|:------:|:------:|:------:|
| PSRT-recurrent  |   6  | 31.88 | 0.8964 |
| **IART (ours)** |   **6**  | **32.15** | **0.9010** |[model](https://drive.google.com/file/d/1L8TsTzINe2sx5UXISjGQmSEpI7GDDgT2/view?usp=share_link) \| [results](https://drive.google.com/file/d/1Eesfph5QHvjKb3YiwdiiHGhjOzbZuLP3/view?usp=share_link)    | 


| REDS4 | Frames| PSNR    | SSIM  |Download |
|:-----|:-----:|:------:|:------:|:------:|
| BasicVSR++  |   30  | 32.39 | 0.9069 |
| VRT  |   16  | 32.19 | 0.9006 |
| RVRT  |   30  | 32.75 | 0.9113 |
| PSRT-recurrent  |   16  | 32.72 | 0.9106 |
| **IART (ours)**  |   **16**  | **32.90** | **0.9138** | [model](https://drive.google.com/file/d/14Pn3uCJ5IvkLJD7HPPqXbV1Si43FV2k2/view?usp=share_link) \| [results](https://drive.google.com/file/d/1GcIGxFdLjkhy0USh3bk0O4RbMUSbnHPD/view?usp=share_link)    |

| Vimeo90k-T | Frames| PSNR    | SSIM  |Download |
|:-----|:-----:|:------:|:------:|:------:|
| BasicVSR++  |   14  | 37.79 | 0.9500 |
| VRT  |   7  | 38.20 | 0.9530 |
| RVRT  |   14  | 38.15 | 0.9527 |
| PSRT-recurrent  |   14  | 38.27 | 0.9536 |
| **IART (ours)**  |   7  | 38.14 | 0.9528 | [model](https://drive.google.com/file/d/1RG-R1zr_2Hl8CFi1kpw4tgCsyHfL8oeR/view?usp=sharing) |

| Vid4 | Frames| PSNR    | SSIM  |Download |
|:-----|:-----:|:------:|:------:|:------:|
| BasicVSR++  |   14  | 27.79 | 0.8400 |
| VRT  |   7  | 27.93 | .8425 |
| RVRT  |   14  | 27.99 | 0.8462 |
| PSRT-recurrent  |   14  | 28.07 | 0.8485 |
| **IART (ours)**  |   **7**  | **28.26** | **0.8517** | [model](https://drive.google.com/file/d/1RG-R1zr_2Hl8CFi1kpw4tgCsyHfL8oeR/view?usp=sharing) |

## Installation

*We test the code for python-3.9, torch-1.13.1 and cuda-11.7. Similar versions will also work.*

```bash
conda create -n IART python==3.9
conda activate IART

pip install torch==1.13.1+cu117 torchvision==0.14.1+cu117 --extra-index-url https://download.pytorch.org/whl/cu117
pip install -r requirements.txt
```
## Prepare Data:

To prepare the dataset, follow [BasicSR](https://github.com/XPixelGroup/BasicSR/blob/master/docs/DatasetPreparation.md#Video-Super-Resolution). After completing the preparation, the directory structure should be as follows: 

```
datasets/
├──REDS/
│   └──val_REDS4_sharp
│   └──val_REDS4_sharp_bicubic
```

## Testing

Download models and put them under `experiments/pretrained_models/`

```bash
# VSR trained on REDS with 6 input frames, tested on REDS4
CUDA_VISIBLE_DEVICES=0 python test_IART_reds_N6.py

# VSR trained on REDS with 16 input frames, tested on REDS4
CUDA_VISIBLE_DEVICES=0 python test_IART_reds_N16.py

```

## Training

```bash
# VSR trained on REDS with 6 input frames, tested on REDS4
bash dist_train.sh 8 options/IART_REDS_N6_300K.yml

# VSR trained on REDS with 16 input frames, tested on REDS4
bash dist_train.sh 8 options/IART_REDS_N16_600K.yml

# video sr trained on Vimeo, validated on Vid4
bash dist_train.sh 8 options/IART_Vimeo_N14_300K.yml
```

Due to the incompatibility of pytorch checkpoint and distributed training, the training process will terminate after the first 5000 iterations. To resume the training, execute the training script again and the previously saved parameters will be automatically loaded. This will restore the normal training procedure.



## Acknowledgment
We acknowledge the following contributors whose code served as the basis for our work:

[RethinkVSRAlignment](https://github.com/XPixelGroup/RethinkVSRAlignment), [BasicSR](https://github.com/XPixelGroup/BasicSR) and [mmediting](https://github.com/open-mmlab/mmediting).
