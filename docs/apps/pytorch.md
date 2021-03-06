# PyTorch

Machine learning framework for Python.

!!! Important

    **Starting from PyTorch version 1.7 our modules will be based on Singularity by default**. This is mainly due to the performance issues of conda-based environments on shared file systems, causing long  start-up delays for Python scripts on CSC's supercomputers. Wrapper scripts have been provided so that common commands such as `python`, `python3`, `pip` and `pip3` should work as normal. For more information, see our [machine learning guide](../support/tutorials/gpu-ml.md).


## Available

Available on Puhti only.  Currently supported PyTorch versions and corresponding modules to load:

- 1.7.1 with [Horovod](../support/tutorials/gpu-ml.md#multi-gpu-and-multi-node-jobs) support using [Singularity](../support/tutorials/gpu-ml.md#singularity): `pytorch/1.7`
- 1.6.0: `pytorch/1.6`
- 1.4.0: `pytorch/1.4`
- 1.3.1: `pytorch/1.3.1`
- 1.3.1 with [Horovod](../support/tutorials/gpu-ml.md#multi-gpu-and-multi-node-jobs) support using hpcx MPI: `pytorch/1.3.1-hvd`
- 1.3.1 with [Horovod](../support/tutorials/gpu-ml.md#multi-gpu-and-multi-node-jobs) support using mpich MPI: `pytorch/1.3.1-hvd-mpich`
- 1.3.0: `pytorch/1.3.0`
- 1.2.0: `pytorch/1.2.0`
- 1.1.0: `pytorch/1.1.0`
- 1.0.1: `pytorch/1.0.1`
- 0.4.1: `pytorch/0.4.1`

All modules include [PyTorch](https://pytorch.org/) and related libraries with GPU support via CUDA.

If you find that some package is missing, you can often install it yourself with `pip install --user`. If you think that some important PyTorch-related package should be included in the module provided by CSC, you can send an email to <servicedesk@csc.fi>.

Some modules are Singularity-based. Wrapper scripts have been provided so that common commands such as `python`, `python3`, `pip` and `pip3` should work as normal. For more information, see our [machine learning guide](../support/tutorials/gpu-ml.md).
 

### NVIDIA's containers

For convenience, we also offer modules based on NVIDIA's optimized
container images from
[NGC](https://ngc.nvidia.com/catalog/containers/nvidia:pytorch) with
some CSC specific additions. For these, the included PyTorch versions
are typically not the release versions, but the newest git commit at
the time of creation. See [NVIDIA's PyTorch container release
notes](https://docs.nvidia.com/deeplearning/frameworks/pytorch-release-notes/index.html)
for more information on provided software versions.

- 1.8.0a0 using [Singularity](../support/tutorials/gpu-ml.md#singularity): `pytorch/nvidia-20.11-py3`
- 1.7.0a0 with *experimental* [Horovod](../support/tutorials/gpu-ml.md#multi-gpu-and-multi-node-jobs) support using [Singularity](../support/tutorials/gpu-ml.md#singularity): `pytorch/nvidia-20.08-py3`
- 1.6.0a0 using [Singularity](../support/tutorials/gpu-ml.md#singularity): `pytorch/nvidia-20.07-py3`
- 1.5.0a0 using [Singularity](../support/tutorials/gpu-ml.md#singularity): `pytorch/nvidia-20.03-py3`
- 1.5.0a0 using [Singularity](../support/tutorials/gpu-ml.md#singularity): `pytorch/nvidia-20.02-py3`
- 1.4.0a0 using [Singularity](../support/tutorials/gpu-ml.md#singularity): `pytorch/nvidia-19.11-py3`


## License

PyTorch is BSD-style licensed, as found in the [LICENSE file](https://github.com/pytorch/pytorch/blob/master/LICENSE).

## Usage

To use this software on Puhti, initialize it with:

```text
module load pytorch
```

to access the default version, or if you wish to have a specific version (see [above for available versions](#available)):

```text
module load pytorch/1.4
```

Please note that the module already includes CUDA and cuDNN libraries, so **there is no need to load cuda and cudnn modules separately!**

This command will also show all available versions:

```text
module avail pytorch
```

To check the exact packages and versions included in the loaded module you can run:

```text
list-packages
```


!!! note 

    Note that Puhti login nodes are not intended for heavy computing, please use slurm batch jobs instead. See our [instructions on how to use the batch job system](../computing/running/getting-started.md).

### Example batch script

Example batch script for reserving one GPU and 10 CPUs in a single node:

```bash
#!/bin/bash
#SBATCH --account=<project>
#SBATCH --partition=gpu
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=10
#SBATCH --mem=64G
#SBATCH --time=1:00:00
#SBATCH --gres=gpu:v100:1

module load pytorch/1.4
srun python3 myprog.py <options>
```

!!! note

    Please **do not read a huge number of files from the shared file system**, use fast local disk or package your data into larger files instead!  See the [GPU-accelerated machine learning guide](../support/tutorials/gpu-ml.md#data-storage) for more details.

### Big datasets, multi-GPU and multi-node jobs

Please see our tutorial for [GPU-accelerated machine learning](../support/tutorials/gpu-ml.md), which covers more advanced topics, including how to work with big data sets, multi-GPU and multi-node jobs.


## More information

- CSC's guide for [GPU-accelerated machine learning](../support/tutorials/gpu-ml.md)
- [PyTorch documentation](https://pytorch.org/docs/stable/index.html)
- [Horovod with PyTorch example](https://github.com/horovod/horovod/blob/master/docs/pytorch.rst)
