# Overview
Tensorflow can be a bit of a bear getting setup on Windows. This guide is intended as a one-stop-shop for those wanting to get started ASAP. We will also cover a couple of "gotchas" that I have found getting some of the basic GPU-enabled capabilities running successfully.

# Building a Tensorflow virtual environment

## Install 64 bit Python >=3.5
Tensorflow distributions from Google currently requires Python >=3.5 in the 64 bit distribution. Use https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64-webinstall.exe and install into a local directory (e.g. `C:\Python35-64`) or a location you will remember. We will be using `virtualenv` to isolate the Tensorflow environement away from any other existing Python installations you may have running in your environment.

## Install [`virtualenv`](https://pypi.python.org/pypi/virtualenv)
`virtualenv` is a great utility for maintaining different versions of Python to prevent conflicting versions running in to each other. It also will install setup tools and package managers.
```
pip install virtualenv
```

## Create `tensorflow-env`
Create a virtual environement for installing Tensorflow inside of. Give it a name you'll remember, such as "tensorflow" or "tensorflow-env".
```
virtualenv -p C:\Python35-64\python.exe C:\path\to\tensorflow-env
```

## Activate the tensorflow virtual environment
Virtual environments must be activated before using the distribution or using the environment's package manager.
```
tensorflow-env\scripts\activate
```
You will notice that you will see the `tensorflow-env` tag behind your prompt, signifying that you are now using the virtual environements path settings configured for this installation of python.
```
(tensorflow-env) C:\>
```
I will be omitting the prompt (e.g. `(tensorflow-env) C:\>`) so that you can copy-paste the `pip install` commands below in your environment.

# Installing Tensorflow and dependencies

## Install Numpy
On Windows, you will need to use the Numpy+MKL wheel, which contains Intel's [Math Kernal Library](https://software.intel.com/en-us/intel-mkl) from the [unofficial python wheels](http://www.lfd.uci.edu/~gohlke/pythonlibs/#numpy). Once you have downloaded the wheel, you can use `pip` to install it locally.
```
pip install C:\Users\<your_user>\Downloads\numpy-1.11.2+mkl-cp35-cp35m-win_amd64.whl
```

## Install Tensorflow
Tensorflow can be used independent of GPUs, and is a great way for testing smaller networks. In larger settings, you will definitely want to take advantage of the GPU. The [basic_cnn](basic_cnn.py) example alone gains a ~9x increase in performance using an NVIDIA GTX 960m, which is a common card on Dell laptops from 2015.

* Check the [Tensorflow documentation](https://www.tensorflow.org/get_started/os_setup#pip_installation_on_windows) for the latest pip install commands. It will look something like the following `pip install` commands below.

### CPU-only support
CPU-only only requires the `pip` installation from the Google cloud storage.
```
pip install --upgrade https://storage.googleapis.com/tensorflow/windows/cpu/tensorflow-0.12.0rc1-cp35-cp35m-win_amd64.whl
```
You now have a working installation of Tensorflow - woohoo!

### GPU support
GPU support requires a different `pip` installation from the Google cloud storage. The Tensorflow team is very good about releasing `cpu` and `gpu` builds concurrently, so you can edit the line from the CPU-only swapping `gpu` for `cpu`. Or, you can copy-paste the line below.
```
pip install --upgrade https://storage.googleapis.com/tensorflow/windows/gpu/tensorflow_gpu-0.12.0rc1-cp35-cp35m-win_amd64.whl
```

Tensorflow requires additional dependencies from NVIDIA's GPU programming library, CUDA, to utilize the GPU. Without these dependencies, Tensorflow will not work properly whatsoever.

## CUDA Toolkit
GPU support requires the [NVIDIA CUDA Toolkit](https://developer.nvidia.com/cuda-downloads) and an [CUDA-enabled GPU](https://developer.nvidia.com/cuda-gpus) (see the extensive list of compatible GPUs). Most NVIDIA cards within the last 3 years will be CUDA-enabled. Additionally, the CUDA toolkit will install lots of additional drivers, including update video driver updates, that Tensorflow is dependent on.

## cuDNN
NVIDIA's [CUDA Deep Neural Network](https://developer.nvidia.com/cudnn) (cuDNN) is utilized by Tensorflow for performing much of the heavy lifting for running Depp Neural Networks, and will be necessary for any utilizing of Tensorflow on the GPU.

You can [download the library here](https://developer.nvidia.com/cudnn) (you'll need to register for their Accelerated Computing Membership Program), and then unzip the folder on your machine. The structure will look something like the following:
```
└───cuda
    ├───bin
    ├───include
    └───lib
        └───x64
```

Now we need to let Tensorflow know where cuDNN is located. I recommend copying the `cuda` directory to
```
C:\Program Files (x86)\NVIDIA Corporation\cudnn
```

From here, we can update the system `PATH` environment variable with the path below. However you wish to do this without destroying the existing `PATH` variable is up to you.
```
C:\Program Files (x86)\NVIDIA Corporation\cudnn\cuda\bin
```

Tensorflow will now be able to load cuDNN without a fuss!

## CUPTI
The [CUDA Profiling Tools Interface](http://docs.nvidia.com/cuda/cupti/) (CUPTI) is utilized by Tensorflow in a profiling sense for inspecting how well the GPU is handling the loads. Unfortunately, these are placed inside of `extras` directory inside of the installation directory for CUDA. We will need to make these available.

The simplest way of doing this is by copying the CUPTI header (`.h`) and object library (`.lib`) into the main CUDA `bin` and `lib\x64` directories.

To do this, assuming you installed CUDA into the default directory on Windows:

First copy `cupti64_80.dll` from the `\extras\CUPTI\libx64\` directory into the `\bin\` directory:
```
# Copy cupti64_80.dll...
C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0\extras\CUPTI\libx64\cupti64_80.dll
# into...
C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0\bin\cupti64_80.dll
```

Now copy `cupti.lib` from the `\extras\CUPTI\libx64\` directory into the `\lib\x64\` directory:
```
Copy cupti.lib
C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0\extras\CUPTI\libx64\cupti.lib
# into...
C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0\lib\x64\cupti.lib
```

# Success!

You should now have a working installation of Tensorflow on Windows!

Please send suggestions, typos, or any other feedback to taylor.mutch@consbio.org. Or better yet, submit a pull request!
