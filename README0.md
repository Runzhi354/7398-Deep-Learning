# Image Super-Resolution via Iterative Refinement
基于GAN和马赛克技术的超分辨率优化
###Zexuan Meng,Xiaoqi Guo,Runzhi Wang
## Usage
### Environment
```python
pip install -r requirement.txt
```

### For Pretrained Model

```python
# If you want use some otehr models,Edit [sr|sample]_[ddpm|sr3]_[resolution option].json about "resume_state":
"resume_state": [your pretrain model path]
```

### Data Prepare

#### New Start
You need to change the datasets config to your data path and image resolution: 
你需要将数据集配置更改为您的数据路径和图像分辨率：

```json
"datasets": {
    "train": {
        "dataroot": "dataset/ffhq_16_128", // [output root] in prepare.py script
        "l_resolution": 16, // low resolution need to super_resolution
        "r_resolution": 128, // high resolution
        "datatype": "lmdb", //lmdb or img, path of img files
    },
    "val": {
        "dataroot": "dataset/celebahq_16_128", // [output root] in prepare.py script
    }
},
```
You also can use your image data by following steps, and we have some examples in dataset folder.

您也可以按照以下步骤使用您的图像数据，我们在数据集文件夹中有一些示例。（选用马赛克和非马赛克混合）

At first, you should organize the images layout like this, this step can be finished by `data/prepare_data.py` automatically:

你可以直接使用预处理脚本，来将你选择的任意尺寸图像调整为你想要的尺寸，例如lr为16，hr为128
```shell
# set the high/low resolution images, bicubic interpolation images path # 设置高/低分辨率图片，双三次插值图片路径
dataset/celebahq_16_128/
├── hr_128 # it's same with sr_16_128 directory if you don't have ground-truth images.
├── lr_16 # vinilla low resolution images
└── sr_16_128 # images ready to super resolution
```

```python
# super resolution from 16 to 128
python data/prepare_data.py  --path [dataset root]  --out celebahq --size 16,128 
```

*Note: 请注意，你不需要保留中括号[ ]。*

then you need to change the dataset config to your data path and image resolution: 

同样的，你需要将数据集配置更改为您的数据路径和图像分辨率：
```json
"datasets": {
    "train|val": { // train and validation part
        "dataroot": "dataset/celebahq_16_128",
        "l_resolution": 16, // low resolution need to super_resolution
        "r_resolution": 128, // high resolution
        "datatype": "img", //lmdb or img, path of img files
    }
},
```

### Training

```python
# Use sr.py and sample.py to train the super resolution task and unconditional generation task, respectively.
# Edit json files to adjust network structure and hyperparameters
python sr.py -p train -c config/sr_sr3.json
```
### Test
*Note: 请注意，你需要先在 config 文件中更改验证数据集路径*
```python
# Edit json to add pretrain model path and run the evaluation 
python sr.py -p val -c config/sr_sr3.json

# Quantitative evaluation alone using SSIM/PSNR metrics on given result root
python eval.py -p [result root]
```

### Inference Alone

Set the  image path like steps in `Own Data`, then run the script:

```python
# run the script
python infer.py -c [config file]
```

## Acknowledge

Our work is based on the following theoretical works:

- [Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network](https://arxiv.org/abs/1609.04802)
- [Image Super-Resolution: A Comprehensive Review](https://blog.paperspace.com/image-super-resolution/)
- [Deep Learning for Image Super-Resolution](https://www.analyticsvidhya.com/blog/2021/05/deep-learning-for-image-super-resolution/)
- [Mosaic data enhancement method in YoloV4](https://zhuanlan.zhihu.com/p/174019699)
- [Denoising Diffusion Probabilistic Models](https://arxiv.org/pdf/2006.11239.pdf)
- [Image Super-Resolution via Iterative Refinement](https://arxiv.org/pdf/2104.07636.pdf)
- [WaveGrad: Estimating Gradients for Waveform Generation](https://arxiv.org/abs/2009.00713)
- [Large Scale GAN Training for High Fidelity Natural Image Synthesis](https://arxiv.org/abs/1809.11096)

and we are benefiting a lot from the following projects:

- https://github.com/bhushan23/BIG-GAN
- https://github.com/lmnt-com/wavegrad
- https://github.com/rosinality/denoising-diffusion-pytorch
- https://github.com/lucidrains/denoising-diffusion-pytorch
- https://github.com/hejingwenhejingwen/AdaFM
- https://github.com/idealo/image-super-resolution
- https://github.com/NVlabs/ffhq-dataset



