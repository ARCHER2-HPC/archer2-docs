# Machine Learning

Two Machine Learning (ML) frameworks are supported on ARCHER2, [PyTorch](https://pytorch.org) and [TensorFlow](https://www.tensorflow.org).

For each framework, we'll show how to run a particular [MLCommons HPC benchmark](https://github.com/mlcommons/hpc).
We start with PyTorch.


## PyTorch

On ARCHER2, PyTorch is supported for use on both the CPU and GPU nodes.

We'll demonstrate the use of PyTorch with [DeepCam](https://github.com/mlcommons/hpc/tree/main/deepcam), a deep learning climate segmentation benchmark.
It involves training a neural network to recognise large-scale weather phenomena (e.g., tropical cyclones, atmospheric rivers) in the output generated
by ensembles of weather simulations, see link below for more details.

[Exascale Deep Learning for Climate Analytics](https://dl.acm.org/doi/10.5555/3291656.3291724)

There are two DeepCam training datasets available on ARCHER2. A **62 GB** mini dataset (`/work/z19/shared/mlperf-hpc/deepcam/mini`), and a much larger
**8.9 TB** dataset (`/work/z19/shared/mlperf-hpc/deepcam/full`).


### DeepCam on GPU

A binary install of PyTorch 1.13.1 suitable for ROCm 5.2.3 has been installed according to the instructions linked below.

[https://github.com/hpc-uk/build-instructions/blob/main/pyenvs/pytorch/build_pytorch_1.13.1_archer2_gpu.md](https://github.com/hpc-uk/build-instructions/blob/main/pyenvs/pytorch/build_pytorch_1.13.1_archer2_gpu.md)

This install can be accessed by loading the `pytorch/1.13.1-gpu` module.

As DeepCam is an [MLPerf](https://ieeexplore.ieee.org/document/9238612) benchmark, you may wish to base a local python environment on `pytorch/1.13.1-gpu`
so that you have the opportunity to install additional python packages that support MLPerf logging, as well as extra features pertinent to DeepCam (e.g., dynamic learning rates).

The following instructions show how to create such an environment.

```bash
#!/bin/bash

module -q load pytorch/1.13.1-gpu

PYTHON_TAG=python`echo ${CRAY_PYTHON_LEVEL} | cut -d. -f1-2`

PRFX=${HOME/home/work}/pyenvs
PYVENV_ROOT=${PRFX}/mlperf-pt-gpu
PYVENV_SITEPKGS=${PYVENV_ROOT}/lib/${PYTHON_TAG}/site-packages

mkdir -p ${PYVENV_ROOT}
cd ${PYVENV_ROOT}


python -m venv --system-site-packages ${PYVENV_ROOT}

extend-venv-activate ${PYVENV_ROOT}

source ${PYVENV_ROOT}/bin/activate


mkdir -p ${PYVENV_ROOT}/repos
cd ${PYVENV_ROOT}/repos

git clone -b hpc-1.0-branch https://github.com/mlcommons/logging mlperf-logging
python -m pip install -e mlperf-logging

rm ${PYVENV_SITEPKGS}/mlperf-logging.egg-link
mv ./mlperf-logging/mlperf_logging ${PYVENV_SITEPKGS}/
mv ./mlperf-logging/mlperf_logging.egg-info ${PYVENV_SITEPKGS}/

python -m pip install git+https://github.com/ildoonet/pytorch-gradual-warmup-lr.git

deactivate
```

In order to run a DeepCam training job, you must first clone the [MLCommons HPC github repo](https://github.com/mlcommons/hpc/tree/main).

```
mkdir ${HOME/home/work}/tests
cd ${HOME/home/work}/tests

git clone https://github.com/mlcommons/hpc.git mlperf-hpc

cd ./mlperf-hpc/deepcam/src/deepCam
```

You are now ready to run the following DeepCam submission script via the `sbatch` command.

```
#!/bin/bash

#SBATCH --job-name=deepcam
#SBATCH --account=[budget code]
#SBATCH --partition=gpu
#SBATCH --qos=gpu-exc
#SBATCH --nodes=2
#SBATCH --gpus=8
#SBATCH --time=01:00:00
#SBATCH --exclusive


JOB_OUTPUT_PATH=./results/${SLURM_JOB_ID}
mkdir -p ${JOB_OUTPUT_PATH}/logs

source ${HOME/home/work}/pyenvs/mlperf-pt-gpu/bin/activate

export OMP_NUM_THREADS=1
export HOME=${HOME/home/work}

srun --ntasks=8 --tasks-per-node=4 \
     --cpu-bind=verbose,map_cpu:0,8,16,24 --hint=nomultithread \
     python train.py \
         --run_tag test \
         --data_dir_prefix /work/z19/shared/mlperf-hpc/deepcam/mini \
         --output_dir ${JOB_OUTPUT_PATH} \
	 --wireup_method nccl-slurm \
	 --max_epochs 64 \
	 --local_batch_size 1

mv slurm-${SLURM_JOB_ID}.out ${JOB_OUTPUT_PATH}/slurm.out
```

The job submission script activates the python environment that was setup earlier, but that particular command (`source ${HOME/home/work}/pyenvs/mlperf-pt-gpu/bin/activate`)
could be replaced by `module -q load pytorch/1.13.1-gpu` if you are not running DeepCam and have no need for additional Python packages such as `mlperf-logging` and `warmup-scheduler`.

In the script above, we specify four tasks per node, one for each GPU. These tasks are evenly spaced across the node so as to maximise the communications
bandwidth between the host and the GPU devices. Note, PyTorch is not using Cray MPICH for inter-task communications, which is instead being handled by the 
ROCm Collective Communications Library (RCCL), hence the `--wireup_method nccl-slurm` option (`nccl-slurm` works as an alias for `rccl-slurm` in this context).

The above job should achieve convergence &mdash; an Intersection over Union (IoU) of 0.82 &mdash; after 35 epochs or so. Runtime should be around 20-30 minutes.


We can also modify the DeepCam `train.py` script so that the accuracy and loss are logged using [TensorBoard](https://pytorch.org/tutorials/recipes/recipes/tensorboard_with_pytorch.html).

The following lines must be added to the DeepCam `train.py` script.

```python
import os
...

from torch.utils.tensorboard import SummaryWriter

...

def main(pargs):

    #init distributed training
    comm_local_group = comm.init(pargs.wireup_method, pargs.batchnorm_group_size)
    comm_rank = comm.get_rank()
    ...

    #set up logging
    pargs.logging_frequency = max([pargs.logging_frequency, 0])
    log_file = os.path.normpath(os.path.join(pargs.output_dir, "logs", pargs.run_tag + ".log"))
    ...

    writer = SummaryWriter()

    #set seed
    ...

    ...

    #training loop
    while True:
        ...

        #training
        step = train_epoch(pargs, comm_rank, comm_size,
                           ...
                           logger, writer)

        ...
```

The `train_epoch` function is defined in `./driver/trainer.py` and so that file must be amended like so.

```python
...

def train_epoch(pargs, comm_rank, comm_size,
                ...,
                logger, writer):

    ...

    writer.add_scalar("Accuracy/train", iou_avg_train, epoch+1)
    writer.add_scalar("Loss/train", loss_avg_train, epoch+1)

    return step
```


### DeepCam on CPU

PyTorch can also be run on the ARCHER2 CPU nodes. However, since the DeepCam uses the `torch.distributed` module, we cannot use
[Horovod](https://horovod.readthedocs.io/en/stable/index.html) to handle (via MPI) inter-task communications. We must instead build PyTorch from source so that we can link `torch.distributed`
to the correct Cray MPICH libraries.

The instructions for doing such a build can be found here,
[https://github.com/hpc-uk/build-instructions/blob/main/pyenvs/pytorch/build_pytorch_1.13.0a0_from_source_archer2_cpu.md](https://github.com/hpc-uk/build-instructions/blob/main/pyenvs/pytorch/build_pytorch_1.13.0a0_from_source_archer2_cpu.md).

This install can be accessed by loading the `pytorch/1.13.0a0` module.
Please note, PyTorch source version `1.13.0a0` corresponds to PyTorch package version `1.13.1`.

Once again, as we are running the DeepCam benchmark, we'll need to setup a local Python environment for installing the MLPerf logging package.
This time the local environment is based on the `pytorch/1.13.0a0` module.

```bash
#!/bin/bash

module -q load pytorch/1.13.0a0

PYTHON_TAG=python`echo ${CRAY_PYTHON_LEVEL} | cut -d. -f1-2`

PRFX=${HOME/home/work}/pyenvs
PYVENV_ROOT=${PRFX}/mlperf-pt
PYVENV_SITEPKGS=${PYVENV_ROOT}/lib/${PYTHON_TAG}/site-packages

mkdir -p ${PYVENV_ROOT}
cd ${PYVENV_ROOT}


python -m venv --system-site-packages ${PYVENV_ROOT}

extend-venv-activate ${PYVENV_ROOT}

source ${PYVENV_ROOT}/bin/activate


mkdir -p ${PYVENV_ROOT}/repos
cd ${PYVENV_ROOT}/repos

git clone -b hpc-1.0-branch https://github.com/mlcommons/logging mlperf-logging
python -m pip install -e mlperf-logging

rm ${PYVENV_SITEPKGS}/mlperf-logging.egg-link
mv ./mlperf-logging/mlperf_logging ${PYVENV_SITEPKGS}/
mv ./mlperf-logging/mlperf_logging.egg-info ${PYVENV_SITEPKGS}/

python -m pip install git+https://github.com/ildoonet/pytorch-gradual-warmup-lr.git

deactivate
```

In order to run a DeepCam training job, you must first clone the [MLCommons HPC github repo](https://github.com/mlcommons/hpc/tree/main).

```
mkdir ${HOME/home/work}/tests
cd ${HOME/home/work}/tests

git clone https://github.com/mlcommons/hpc.git mlperf-hpc

cd ./mlperf-hpc/deepcam/src/deepCam
```


Next, we need to edit some parts of the DeepCam Python source such that DeepCam is properly integrated with Cray MPICH.

The `init` function defined in `./utils/comm.py` contains an `if` statement that initialises the DeepCam job according
to the selected communications method. You will need to edit the `mpi` branch of this `if` statement as shown below.

```python
...

def init(method, batchnorm_group_size=1):

    if method == "nccl-openmpi":

    ...

    elif method == "mpi":
        rank = int(os.getenv("SLURM_PROCID"))
        world_size = int(os.getenv("SLURM_NTASKS"))
        dist.init_process_group(backend = "mpi",
                                rank = rank,
                                world_size = world_size)
    
    else:
        raise NotImplementedError()

    ...    
```

Second, as we're not running on a GPU platform, we'll need to comment out a statement that calls a GPU-based
synchronisation method, see the `synchronize` method within `./utils/bnstats.py`.

```python
...

def synchronize(self:

    if dist.is_initialized():
        # sync the device before
        #torch.cuda.synchronize()
    
    with torch.no_grad():
        ...
```


DeepCam can now be run on the CPU nodes using a submission script like the one below.

```bash
#!/bin/bash

#SBATCH --job-name=deepcam
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard
#SBATCH --nodes=32
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=128
#SBATCH --time=10:00:00
#SBATCH --exclusive


JOB_OUTPUT_PATH=./results/${SLURM_JOB_ID}
mkdir -p ${JOB_OUTPUT_PATH}/logs

source ${HOME/home/work}/pyenvs/mlperf-pt/bin/activate

export SRUN_CPUS_PER_TASK=${SLURM_CPUS_PER_TASK}
export OMP_NUM_THREADS=${SLURM_CPUS_PER_TASK}

srun --hint=nomultithread \
     python train.py \
         --run_tag test \
         --data_dir_prefix /work/z19/shared/mlperf-hpc/deepcam/mini \
         --output_dir ${JOB_OUTPUT_PATH} \
         --wireup_method mpi \
         --max_inter_threads ${SLURM_CPUS_PER_TASK} \
         --max_epochs 64 \
         --local_batch_size 1

mv slurm-${SLURM_JOB_ID}.out ${JOB_OUTPUT_PATH}/slurm.out
```

The script above activates the local Python environment so that the `mlperf-logging` package is available; this is needed
by the `logger` object declared in the DeepCam `train.py` script. Notice also that the `--wireup-method` parameter is
now set to `mpi` and that a new parameter has been added, `--max_inter_threads`, for specifying the maximum number
of concurrent readers.

DeepCam performance on the CPU nodes is much slower than GPU. Running on 32 CPU nodes, as shown above, will take around 6 hours
to complete 35 epochs. This assumes you're using the default hyperparameter settings for DeepCam.


## TensorFlow 

On ARCHER2, TensorFlow is supported for use on the CPU nodes only.

We'll demonstrate the use of TensorFlow with the [CosmoFlow](https://github.com/mlcommons/hpc/tree/main/cosmoflow) benchmark.
It involves training a neural network to recognise cosmological parameter values from the output generated by 3D dark matter simulations, see link below for more details.

[CosmoFlow: using deep learning to learn the universe at scale](https://dl.acm.org/doi/10.1109/SC.2018.00068)

There are two CosmoFlow training datasets available on ARCHER2. A **5.6 GB** mini dataset (`/work/z19/shared/mlperf-hpc/cosmoflow/mini`), and a much larger
**1.7 TB** dataset (`/work/z19/shared/mlperf-hpc/cosmoflow/full`).


### CosmoFlow on CPU

In order to run a CosmoFlow training job, you must first clone the [MLCommons HPC github repo](https://github.com/mlcommons/hpc/tree/main).

```
mkdir ${HOME/home/work}/tests
cd ${HOME/home/work}/tests

git clone https://github.com/mlcommons/hpc.git mlperf-hpc

cd ./mlperf-hpc/cosmoflow
```

You are now ready to run the following CosmoFlow submission script via the `sbatch` command.

```
#!/bin/bash

#SBATCH --job-name=cosmoflow
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard
#SBATCH --nodes=32
#SBATCH --ntasks-per-node=8
#SBATCH --cpus-per-task=16
#SBATCH --time=01:00:00
#SBATCH --exclusive

module -q load tensorflow/2.13.0

export UCX_MEMTYPE_CACHE=n
export SRUN_CPUS_PER_TASK=${SLURM_CPUS_PER_TASK}
export MPICH_DPM_DIR=${SLURM_SUBMIT_DIR}/dpmdir

export OMP_NUM_THREADS=16
export TF_ENABLE_ONEDNN_OPTS=1

srun  --hint=nomultithread --distribution=block:block --cpu-freq=2250000 \
    python train.py \
        --distributed --omp-num-threads ${OMP_NUM_THREADS} \
        --inter-threads 0 --intra-threads 0 \
        --n-epochs 2048 --n-train 1024 --n-valid 1024 \
        --data-dir /work/z19/shared/mlperf-hpc/cosmoflow/mini/cosmoUniverse_2019_05_4parE_tf_v2_mini
```

The CosmoFlow job runs eight MPI tasks per node (one per NUMA region) with sixteen threads per task,
and so, each node is fully populated. The `TF_ENABLE_ONEDNN_OPTS` variable refers to Intel's oneAPI
Deep Neural Network library. Within the TensorFlow source there are `#ifdef` guards that are activated
when oneDNN is enabled. It turns out that having `TF_ENABLE_ONEDNN_OPTS=1` also improves performance
(by a factor of 12) on AMD processors.

The inter/intra thread training parameters allow one to exploit any parallelism implied by the
TensorFlow (TF) DNN graph. For example, if a node in the TF graph can be parallelised, the number of
threads assigned will be the value of `--intra-threads`; and, if there are separate nodes in the TF
graph that can be run concurrently, the available thread count for such an activity is the value of
`--inter-threads`. Of course, the optimum values for these parameters will depend on the DNN graph.
The job script above tells TensorFlow to choose the values by setting both parameters to zero.

You will note that only a few hyperparameters are specified for the CosmoFlow training job
(e.g., `--n-epochs`, `--n-train` and `--n-valid`). Those settings in fact override the values
assigned to those same parameters within the `./configs/cosmo.yaml` file. However, that file
contains settings for many other hyperparameters that are not overwritten.

The CosmoFlow job specified above should take around 140 minutes to complete 2048 epochs,
which should be sufficient to achieve a mean average error of 0.23.
