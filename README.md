
### LAV-ACT: Language-Augmented Visual Action Chunking with Transformers for Bimanual Robotic Manipulation

Project page: https://dktpt44.github.io/LAV-ACT/

# Improving ACT with pre-trained represention

## What's Changed
### Voltron as a feature extractor
Use pertained [Voltron](https://github.com/siddk/voltron-robotics?tab=readme-ov-file) to produce dense representation vector from image observations and language. The variant of Voltron used is called V-dual, which takes two images from different timesteps and language as input. For the Aloha simulation environment, the tilmestep difference is taken to be 10 timesteps, which corresponds to 0.2 seconds, as the environment has fps = 50. 

### Combining Voltron feature vector and Resnet feature map
We found that using Voltron representation along is not sufficient for good imitation learning. Our hypothesis is that Voltron is pre-trained to learn high level features. However, for precise bimanual manipulation, fine-grained features for precise localization may be needed. For this reason, we keep the Resent backbone (as used in the ACT) together with Voltron. 

The output of Voltron is a dense vector, while the Resnet backbone produces a feature map with spatial dimension. To combine them before feeding to transformer, we choose to use a [FiLM layer](https://arxiv.org/pdf/1709.07871) to perform feature-wise affine transformation on Resnset feature map based on conditioning information (Voltron dense vector). 

## Other Notes 
### By ACT authors: [ACT tuning tips](https://docs.google.com/document/d/1FVIZfoALXg_ZkYKaYVh-qOlaXveq5CtvJHXkY25eYhs/edit?usp=sharing)
TL;DR: if your ACT policy is jerky or pauses in the middle of an episode, just train for longer! Success rate and smoothness can improve way after loss plateaus.

### Simulation dataset
The ACT authors have made all scripted/human demo for simulated environments public [here](https://drive.google.com/drive/folders/1gPR03v05S1xiInoVJn7G7VJ9pDCnxq9O?usp=share_link).

### Running the code
Refer to the original [ACT code base](https://github.com/tonyzhaozh/act) to set up the environment. In the same environment, install [Voltron](https://github.com/siddk/voltron-robotics) package via PyPI: `pip install voltron-robotics`. 

You can run both training and evaluation using the commands described in [ACT code base](https://github.com/tonyzhaozh/act).

### Implementation notes
* **Language conditioning:** Voltron takes language as input, and you can set the corresponding language prompt of the task by seting the `lang_pompt` variable in the main() function in `/imitate_episodes.py`. 
* **Dual images conditioning:** Two images are passed to Voltron, one of the current timestep and one of a previous timestep. Only the current image observation is passed to the Resnet backbone. `EpisodicDataset` in `/utils.py` is modified to include two images (present_frame, prev_frame) per datapoint. The function `eval_bc` in `/imitate_episodes.py` is also modified to pass two observation images to the model during test time.
* **Learning rate and finetuning:** Voltron's weight is unfreezed and finetuned with learning rate 5e-6, which is set by the variable `lr_backbone`. The weights other part of the ACT network and Resnet backbone are updated by a learning rate of 1e-5, as used in the original ACT.
 


## Acknowledgments
This project is the modification of the [project-page-template](https://github.com/lin-tianyu/project-page-template). Thanks!

## Website License
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
