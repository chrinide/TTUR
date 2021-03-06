# Two time-scale update rule for training GANs

This repository contains code accompanying the paper [GANs Trained by a Two Time-Scale Update Rule
Converge to a Nash Equilibrium](https://arxiv.org/abs/1706.08500).

## Frechet Inception Distance (FID)
The FID is the performance measure used to evaluate the experiments in the paper. There, a detailed description can be found
in the experiment section as well as in the the appendix in section A1.

In short:
The Frechet distance between two multivariate Gaussians X_1 ~ N(mu_1, C_1) and X_2 ~ N(mu_2, C_2) is

                       d^2 = ||mu_1 - mu_2||^2 + Tr(C_1 + C_2 - 2*sqrt(C_1*C_2)).

The FID is calculated by assuming that X_1 and X_2 are the activations of the pool_3 layer of the inception model (see below)
for generated samples and real world samples respectivly.

### Batched and unbatched implementation
In this repository we provide two implementations to calculate the FID, a "unbatched" and a "batched" version. Here "unbatched"
and "batched" refer to the way the data is fed into the inception net. The used pretrained model (see below for the link) takes
individual images in JPEG format as input. The "unbatched" version uses this original input layer whereas the "batched" version
skips this layer. This results in a different FID for the two versions, since the conversion into and from JPEG slightly
changes the RGB values. Note, that while the two versions behave consistently on their own, they are not directly compareable.

The experiments in the paper are done with the "unbatched" version, except for the reported consistency tests.
The downside of the "unbatched" version is, that it is very slow (but since we started with this version we had to stick
with it).  Therefore, if a direct comparison with the results in the paper is not necessary, it might be better to use the
batched version.

## Provided Code

Requirements: TF 1.1, Python 3.x

#### fid.py
This file contains the implementation of all necessary functions to calculate the FID, to calculate statistics over real world
samples and to save/load them.

#### calculate_fid.py
Short script to calculate the FID for a given dataset. You will need to provide the precalculated statistics
for the given dataset (see below). An example call might look like this:

    python3 calculate_fid.py ./generated_images/ -s stat_lsun.pkl.gz


#### fid_example_batched.py
Example code to show the usage of the batched version of the FID implementation on the CelebA dataset.
Four distances are calculated for images that are disturbed by randomly implanted, black rectangles,
where the intensity is decreasing.

#### fid_example_unbatched.py
Example code to show the usage of the unbatched version of the FID implementaion on the CelebA dataset.
Four distances are calculated for images that are disturbed by randomly implanted, black rectangles,
where the intensity is decreasing.

#### precalc_stats_example.py
Example code to show how to calculate, save and load real world statistics.

#### data_container.py
Containes a helper class for data handling.

#### WGAN_GP
Improved WGAN (WGAN-GP) implementation forked from https://github.com/igul222/improved_wgan_training
with added FID evaluation for the image model and switchable TTUR/orig settings. Lanuage model with
JSD Tensorboard logging and switchable TTUR/orig settings.

## Additional info
Precalculated unbatched statistics for datasets
- cropped CelebA (calculated on 100000 randomly choosen training samples)
- LSUN bedroom (calculated on 100000 randomly choosen training samples)
- Imagenet (calculated on the validation set)

are provided at: http://bioinf.jku.at/research/ttur/ttur.html

For FID evaluation download the Inception modelf from http://download.tensorflow.org/models/image/imagenet/inception-2015-12-05.tgz

The cropped CelebA dataset can be downloaded here http://mmlab.ie.cuhk.edu.hk/projects/CelebA.html

To download the LSUN bedroom dataset go to: http://www.yf.io/p/lsun

The 64x64 downsampled ImageNet training and validation datasets can be found here http://image-net.org/small/download.php
