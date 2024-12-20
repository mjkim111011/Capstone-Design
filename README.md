# Performance Evaluation of 4D Gaussian Splatting Algorithm in Various Scenarios

## 0. Project Summary
In this project, the performance of the 4D Gaussian Splitting algorithm, a technology for dynamic scene rendering, was evaluated in various scenarios. Qualitative and quantitative evaluations were conducted for various scenarios of the dataset, and the performance and practical application potential of 4D Gaussian Splitting were analyzed through this process.

## 1. Up Front Scenario Demos
|Coarse Training|Dense Training|Result|
|---|---|---|
|![Coarse_Training](/assets/up_fw_before_Training.gif)|![Dense_Training](/assets/up_fw_after_coarse_training.gif)|![Result](/assets/up_fw_real.gif)|


### Result Analysis
|SSIM|PSNR|LIPIPS-vgg|
|---|---|---|
|![output1](/assets/output1.png)|![output2](/assets/output2.png)|![output3](/assets/output3.png)|

|LIPIPS-alex|MS-SSIM|D-SSIM|
|---|---|---|
|![output4](/assets/output4.png)|![output5](/assets/output5.png)|![output6](/assets/output6.png)|

## 2. Goal 
What is the best scenario applying gaussian splatting.


## 3. Conclusion and Future Works
1. The best results came from an environment taken in rear with a wider FOV (Feed of View).
2. Reconstruction was carried out based on the pure rotation under the guise of the plane, but a scenario taken to show the side part of the robot will also be needed.
3. Limitations exist because it is a Handheld camera. For a more accurate comparison, further experiments in control or simulation through a robot are required.

## 4. Environmental Setups

Please follow the [3D-GS](https://github.com/graphdeco-inria/gaussian-splatting) to install the relative packages.

```bash
git clone https://github.com/hustvl/4DGaussians
cd 4DGaussians
git submodule update --init --recursive
conda create -n Gaussians4D python=3.7 
conda activate Gaussians4D

pip install -r requirements.txt
pip install -e submodules/depth-diff-gaussian-rasterization
pip install -e submodules/simple-knn
```

Our test setup was:
- Ubuntu 22.04: `pytorch: 1.13.1+cu117`

## 5. Data Preparation

**Custom Datasets:**
Orginize your dataset as follows:

```
├── data
|   | multipleview
│     | (your dataset name) 
│   	  | cam01
|     		  ├── frame_00001.jpg
│     		  ├── frame_00002.jpg
│     		  ├── ...
│   	  | cam02
│     		  ├── frame_00001.jpg
│     		  ├── frame_00002.jpg
│     		  ├── ...
│   	  | ...
│   	  | sparse_
│     		  ├── cameras.bin
│     		  ├── images.bin
│     		  ├── ...
│   	  | points3D_multipleview.ply
│   	  | poses_bounds_multipleview.npy
```


## 6. Training

For your custom datasets, install nerfstudio and follow their [COLMAP](https://colmap.github.io/) pipeline. You should install COLMAP at first, then:

```python
pip install nerfstudio
# computing camera poses by colmap pipeline
ns-process-data images --data data/your-data --output-dir data/your-ns-data
cp -r data/your-ns-data/images data/your-ns-data/colmap/images
python train.py -s data/your-ns-data/colmap --port 6017 --expname "custom" --configs arguments/hypernerf/default.py 
```
You can customize your training config through the config files.

## 7. Checkpoint

Also, you can train your model with checkpoint.

```python
python train.py -s data/dnerf/bouncingballs --port 6017 --expname "dnerf/bouncingballs" --configs arguments/dnerf/bouncingballs.py --checkpoint_iterations 200 # change it.
```

Then load checkpoint with:

```python
python train.py -s data/dnerf/bouncingballs --port 6017 --expname "dnerf/bouncingballs" --configs arguments/dnerf/bouncingballs.py --start_checkpoint "output/dnerf/bouncingballs/chkpnt_coarse_200.pth"
# finestage: --start_checkpoint "output/dnerf/bouncingballs/chkpnt_fine_200.pth"
```

## 8. Rendering

Run the following script to render the images.

```
python render.py --model_path "output/dnerf/bouncingballs/"  --skip_train --configs arguments/dnerf/bouncingballs.py 
```

## 9. Evaluation

You can just run the following script to evaluate the model.

```
python metrics.py --model_path "output/dnerf/bouncingballs/" 
```


## 10. Viewer



## 11. Citation

```
@InProceedings{Wu_2024_CVPR,
    author    = {Wu, Guanjun and Yi, Taoran and Fang, Jiemin and Xie, Lingxi and Zhang, Xiaopeng and Wei, Wei and Liu, Wenyu and Tian, Qi and Wang, Xinggang},
    title     = {4D Gaussian Splatting for Real-Time Dynamic Scene Rendering},
    booktitle = {Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    month     = {June},
    year      = {2024},
    pages     = {20310-20320}
}

@inproceedings{TiNeuVox,
  author = {Fang, Jiemin and Yi, Taoran and Wang, Xinggang and Xie, Lingxi and Zhang, Xiaopeng and Liu, Wenyu and Nie\ss{}ner, Matthias and Tian, Qi},
  title = {Fast Dynamic Radiance Fields with Time-Aware Neural Voxels},
  year = {2022},
  booktitle = {SIGGRAPH Asia 2022 Conference Papers}
}
```
