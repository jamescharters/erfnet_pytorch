# Training ERFNet in Pytorch

PyTorch code for training ERFNet model on Cityscapes. The code was based initially on the code from [bodokaiser/piwise](https://github.com/bodokaiser/piwise), adapted with several custom added modifications and tweaks. Some of them are:
- Load cityscapes dataset
- ERFNet model definition
- Show IoU on each epoch during training (adapted using cityscapes scripts)
- Save snapshots and best model during training
- Save additional output files useful for checking results (see below "Output files...")

## Options
For all options and defaults please see the bottom of the "main.py" file. Required ones are --savedir (name for creating a new folder with all the outputs of the training) and --datadir (path to cityscapes directory).

## Example commands
Train encoder with 150 epochs and batch=6 and then train decoder (loads encoder trained in the same folder):
```
python3.6 main.py --savedir erfnet_training1 --datadir /home/datasets/cityscapes/ --num-epochs 150 --batch-size 6 
python3.6 main.py --savedir erfnet_training1 --datadir /home/datasets/cityscapes/ --num-epochs 150 --batch-size 6 --decoder
```

Train decoder using encoder's pretrained weights with ImageNet:
```
python3.6 main.py --savedir erfnet_training1 --datadir /home/datasets/cityscapes/ --num-epochs 150 --batch-size 6 --decoder --pretrainedEncoder "../trained_models/erfnet_encoder_pretrained.pth.tar"
```

## Output files generated for each training:
Each training will create a new folder in the "erfnet_pytorch/save/" directory named with the parameter --savedir and the following files:
* **automated_log.txt**: Plain text file that contains in columns the following info of each epoch {Epoch, Train-loss,Test-loss,Train-IoU,Test-IoU, learningRate}. Can be used to plot using Gnuplot or Excel.
* **best.txt**: Plain text file containing a line with the best IoU achieved during training and its epoch.
* **checkpoint.pth.tar**: bundle file that contains the checkpoint of the last trained epoch, contains the following elements: 'epoch' (epoch number as int), 'arch' (net definition as a string), 'state_dict' (saved weights dictionary loadable by pytorch), 'best_acc' (best achieved accuracy as float), 'optimizer' (saved optimizer parameters).
* **{model}.py**: copy of the model file used (default erfnet.py). 
* **model.txt**: Plain text that displays the model's layers
* **model_best.pth**: saved weights of the epoch that achieved best val accuracy.
* **model_best.pth.tar**: Same parameters as "checkpoint.pth.tar" but for the epoch with best val accuracy.
* **opts.txt**: Plain text file containing the options used for this training

NOTE: Encoder trainings have an added "_encoder" tag to each file's name.

## IoU display during training

The IoU calculation during training is performed with code adapted from cityscapes scripts which require compiled c code (with cython) to be fast. A compiled version for Linux 64bits is already provided with the code but if you need to compile for another machine do the following command in "train/cityscapes/" folder:
``` 
python setup.py build_ext --inplace
```

By default, only Validation IoU is calculated for faster training (can be changed in options)

## Visualization
If you want to visualize the outputs during training add the "--visualize" flag and open an extra tab with:
```
python3.6 -m visdom.server -port 8697
```
The plots will be available using the browser in http://localhost.com:8097

## Multi-GPU
If you wish to specify which GPUs to use, use the CUDA_VISIBLE_DEVICES command:
```
CUDA_VISIBLE_DEVICES=0 python3.6 main.py ...
CUDA_VISIBLE_DEVICES=0,1 python3.6 main.py ...
```


