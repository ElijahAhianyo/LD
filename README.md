# Localization Distillation for Object Detection 

### This repo is based on MMDetection.

This is the code for our papers:
 - [Localization Distillation for Object Detection ](https://arxiv.org/abs/1911.08287)[LD](https://arxiv.org/pdf/2011.12885.pdf)

LD is a kind of kownledge distillation strategy which utilizes the learned bounding box distributions to transfer the localization knowledge from teacher to student.

LD stably improves over GFocalV1 about ~0.8 AP and ~1 AR100 without adding any computational cost! 

## Introduction

Knowledge distillation (KD) has witnessed its powerful ability in learning compact and efficient models in several deep learning fields, but it is still limited in distilling localization information for object detection. Existing KD methods for object detection mainly focus on mimicking deep features between teacher model and student model, which not only is restricted by specific model architectures, but also cannot distill localization ambiguity. KD for object detection two issues: (i) how to distill localization information for arbitrary detector architecture, (ii) how to choose effective teacher model for distillation. In this paper, we first propose localization distillation (LD) for object detection. In particular, our LD can be formulated as standard KD by adopting the general localization representation of bounding box. Our LD is very simple and flexible, and is applicable to distill localization ambiguity for arbitrary architecture of teacher model and student model. Moreover, it is interesting to find that Self-LD, i.e., distilling teacher model itself, can further boost state-of-the-art performance. Second, we suggest a teacher assistant (TA) strategy to fill the possible gap between teacher model and student model, by which the distillation effectiveness can be guaranteed even the selected teacher model is not optimal. On benchmark datasets PASCAL VOC and MS COCO, our LD can consistently improve the performance for student models, and also boosts state-of-the-art detectors notably.

<img src="https://github.com/implus/GFocalV2/blob/master/LD.png" width="541" height="365" align="middle"/>


## Installation

Please refer to [INSTALL.md](docs/INSTALL.md) for installation and dataset preparation.

## Get Started

Please see [GETTING_STARTED.md](docs/GETTING_STARTED.md) for the basic usage of MMDetection.

## Train

```python
# assume that you are under the root directory of this project,
# and you have activated your virtual environment if needed.
# and with COCO dataset in 'data/coco/'

./tools/dist_train.sh configs/ld/ld_r101_r50_fpn_ms2x.py 8
```

## Inference

```python
./tools/dist_test.sh configs/ld/ld_r101_r50_fpn_ms2x.py work_dirs/ld_r101_r50_fpn_ms2x/epoch_24.pth 8 --eval bbox
```

## Speed Test (FPS)

```python
CUDA_VISIBLE_DEVICES=0 python3 ./tools/benchmark.py configs/ld/ld_r101_r50_fpn_ms2x.py work_dirs/ld_r101_r50_fpn_ms2x/epoch_24.pth
```

# COCO Evaluation

### GFocalV1 with LD
| Teacher | Student | Training schedule | box AP (val)| box AP75 (val)| box AP (test-dev)| box AP75 (test-dev)| box AR100 (test-dev)|
|:----:|:-------:|:-------:|:----:|:----:|:----:|:----:|:----:|
|      | R-18 | 1x |  35.8  |  38.2  |  36.0  |  38.7  |  55.3  |
| R-101 | R-18 | 1x |  36.5  |  39.3  |  36.8  |  39.9  |  56.6  |
|      | R-34 | 1x |  38.9  |  42.2  |  39.2  |  42.3  |  58.0  |
| R-101 | R-34 | 1x |  39.8  |  43.1  |  40.0  |  43.5  |  59.3  |
|      | R-50 | 1x |  40.1  |  43.1  |  40.5  |  43.9  |  59.0  |
| R-101 | R-50 | 1x |  41.1  |  44.9  |  41.2  |  44.7  |  59.8  |
|      | R-101 | 2x |  44.6  |  48.4  |  45.0  |  48.9  |  62.3  |
| R-101-DCN | R-101 | 2x |  45.4  |  49.5  |  45.6  |  49.8  |  63.3  |

### GFocalV1 with Self-LD

| Teacher | Student | Training schedule | box AP (val)| box AP75 (val)|
|:----:|:-------:|:-------:|:----:|:----:|:----:|:----:|:----:|
|      | R-18 | 1x |  35.8  |  38.2  |
| R-18  | R-18 | 1x |  36.1  |  38.5  |
|      | R-50 | 1x |  40.1  |  43.1  |
| R-50  | R-50 | 1x |  40.6  |  43.8  |
|      | X-101-32x4d-DCN | 2x |  46.9  |  51.1  |
| X-101-32x4d-DCN | X-101-32x4d-DCN | 2x |  47.5 |  51.8  |

### GFocalV2 with LD

| Teacher | Student | Training schedule | box AP (test-dev)| box AP75 (test-dev)| box AR100 (test-dev)|
|:----:|:-------:|:-------:|:----:|:----:|:----:|:----:|:----:|
|      | R-50 | 2x |  44.4  |  48.5  |  62.4  |
| R-101 | R-50 | 2x |  44.8  |  49.0  |  63.1  |
|      | R-101 | 2x |  46.0  |  50.2  |  63.5  |
| R-101-DCN| R-101 | 2x |  46.8  |  51.1  |  64.3  |
|      | R-101-DCN | 2x |  48.2  |  52.6  |  64.4  |
| R2-101-DCN| R-101-DCN | 2x |  49.1  |  53.7  |  65.6  |
|      | X-101-32x4d-DCN | 2x |  49.0  |  53.4  |  64.7  |
| R2-101-DCN| X-101-32x4d-DCN | 2x |  50.2  |  54.9  |  66.3  |
|      | R2-101-DCN | 2x |  50.5  |  55.1  |  66.2  |
| R2-101-DCN| R2-101-DCN | 2x |  51.0  |  55.9  |  66.8  |

[0] *The reported numbers here are from new experimental trials (in the cleaned repo), which may be slightly different from the original paper.* \
[1] *Note that the 1x performance may be slightly unstable due to insufficient training. In practice, the 2x results are considerably stable between multiple runs.* \
[2] *All results are obtained with a single model and without any test time data augmentation such as multi-scale, flipping and etc..* \
[3] *`dcnv2` denotes deformable convolutional networks v2. Note that for ResNe(X)t based models, we apply deformable convolutions from stage c3 to c5 in backbones.* \
[4] *Refer to more details in config files in `config/gfocal/`.* \
[5] *FPS is tested with a single GeForce RTX 2080Ti GPU, using a batch size of 1.* 

## Citation

If you find GFocal useful in your research, please consider citing:

```
@inproceedings{li2020generalized,
    title={Generalized Focal Loss: Learning Qualified and Distributed Bounding Boxes for Dense Object Detection},
    author={Li, Xiang and Wang, Wenhai and Wu, Lijun and Chen, Shuo and Hu, Xiaolin and Li, Jun and Tang, Jinhui and Yang, Jian},
    booktitle={NeurIPS},
    year={2020}
}
```