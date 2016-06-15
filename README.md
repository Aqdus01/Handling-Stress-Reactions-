# LSTM-on-CNN
Extracts features from a pre-trained CNN and trains a LSTM.

## Installation and dependences
1. Install CUDA and CUDNN if possible.
2. Install and compile caffe https://github.com/BVLC/caffe
2. `pip install h5py numpy`
3. Install torch http://torch.ch/
4. Install `torch-hdf5` (https://github.com/deepmind/torch-hdf5/blob/master/doc/usage.md)
5. `luarocks install nn cunn rnn optim gnuplot`
6. `git clone this repository`
7. Set your caffe installation path in `config.py`

## Automatic Script
For automatic generation of the features and training the LSTM run:

```bash
./scripts/train.sh
```
### Data Format
* Images in a OpenCV compatible format (jpg, png, etc.)
* A file with the ordered list of *train* frames. `./path/to/frame_x.jpg label sequence_number\n...`
* A file with the ordered list of *validation* frames. `./path/to/frame_x.jpg label sequence_number\n...`

All the frames from the same video sequence must have the same sequence number.

### Configuration
The script can be configured from inside:

```bash
# DATASET PATH
DEPLOY='smth.prototxt'
CAFFEMODEL='smth.caffemodel'
DATAROOT='root/to/images/'
TRAIN_LIST='train/list.txt' # ex: ./class/0345435.jpg\n... label seq_num or ./0445342.jpg\n... label seq_num, etc.
VAL_LIST='val/list.txt' # ex: ./class/0345435.jpg\n... label seq_num or ./0445342.jpg\n... label seq_num, etc.

# CONVNET PARAMS
EXTRACT_FROM="fc7" # example from vgg16

# DATASET MEAN
# (IMAGENET mean, change as convenient)
R=123.68 
G=116.779
B=123.68

# LSTM hyperparameters
RECURRENT_DEPTH=1
HIDDEN_LAYER_SIZE=256
RHO=5 # max sequence length. n when doing n-to-1.
BATCHSIZE=32 # should be as big as possible
EPOCHS=100000
DROPOUT_PROB=0

# Other
SNAPSHOT_EVERY=10 #number of epochs to save current model. Set 0 for never.
PLOT=1 #plots the regression outputs and targets in real time during learning. Set n to plot every n epochs.
LOG='' #for specific log file. default is ./logs/current_datetime.log. Usage LOG='--logPath <path>'

# FLAGS
# Can use any in {--sort, --cpuonly, --standarize, --verbose} with spaces inbetween
FLAGS='--standarize' # use '--sort' in case the image lists do not have ordered frames
```

## Manual usage
* `gen_outputs.py` receives a caffemodel, the images and the listfiles and creates an hdf5 file with the outputs.
* `LSTM.lua` trains a LSTM with a training and validation hdf5 datasets with the following fields:
  - `outputs`: `NxCxHxW` tensor of N images with `C` channels and spatial dims `HxW`.
  - `labels`: `NxC` tensor of `N` labels of `C` dimensionality.
  - `seq_numbers`: tensor of `N` numbers correponding to the video sequence from which the frame was extracted.

Use the `--testonly` and `--saveOutputs` options of `LSTM.lua` in order to extract a HDF5 with the output of the network, and the `--load` option to reload a saved model.

## Troubleshooting
Please contact me if any problem `pau.rodriguez at uab.cat`


