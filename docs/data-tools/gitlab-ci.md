# Gitlab CI/CD

Gitlab is a DevOps platform that combines a git hosting service as well as a powerful CI/CD (continuous integration/continuous development) framework. 

This page explains how to run CI/CD jobs on ARCHER2 while keeping the integration with gitlab-ci. Your gitlab repository will communicate with ARCHER2 via the ``gitlab-runner``, an executable constantly running on ARCHER2. It is responsible for authenticating to the gitlab repository, retrieving the source code, running jobs -either locally or using slurm-, and finally sending artefacts and results back to the gitlab user interface.

To achieve this, the ``gitlab-runner`` needs to be always running (idling most of the time) with an access to the internet and be able to submit jobs. On ARCHER2 this will be done by running it on the serial queue.

!!! note 
    You should make sure that you trust everyone with write access to your gitlab repository, setting up this CI/CD means that they will be able to run ARCHER2 jobs with your account/budget.

## Useful links

  - [Gitlab-ci documentation](https://docs.gitlab.com/ee/ci/)
  - [Example gitlab repository with CI/CD](https://git.ecdf.ed.ac.uk/slemaire/cse-ci-tests)

## Initial setup

All the tools associated to gitlab-ci are accessible in the module ``gitlab-ci`` that should be loaded with:

```sh
module load gitlab-ci
```

### Runner registration
This step authenticate your gitlab repository with the ``gitlab-runner`` on ARCHER2. For it you will need the *url* of your gitlab instance as well as your repository *registration token*, both of which can be retrieved by going in:
**Settings -> CI/CD**, then **expand** the **runners** item. And you will find both in the **Specific runners** box.

Once you have them at hand, run
```sh
gitlab-runner register
```

and reply to the prompts:

- gitlab instance url: `https://....`
- registration token: `xxx-xxxxxxxxxxxx`
- description: `ARCHER2`
- tag: `hpc,slurm`
- note: ` `
- executor: `custom`

If successful it should now read: **Runner registered successfully.** 
Since you likely want to be able run `untagged` jobs you have to enable it on gitlab's interface. In **Settings -> CI/CD**, expand **runners** and edit the newly created **ARCHER2** line and tick the box named **Run untagged jobs**.

### Configuration

Once the runner is registered, a configuration file is placed in `~/.gitlab-runner/config.toml`. You will need to edit it and add the `config_exec` and `run_exec` lines like in the example below. Also make sure you edit/add the `concurrent` lines to allow multiple job to be run simultaneously.

```toml
concurrent = 10
check_interval = 0
shutdown_timeout = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "NAME"
  url = "YOUR URL"
  id = ID
  token = TOKEN
  token_obtained_at = 2023-05-19T15:05:07Z
  token_expires_at = 0001-01-01T00:00:00Z
  executor = "custom"
  [runners.custom]
    concurrent = 10
    config_exec = "gitlab-ci-config.sh"
    run_exec = "gitlab-ci-run.py"
```



## Launch gitlab-runner

CI/CD jobs need the `gitlab-runner` to be continuously running on ARCHER2 which is done using the `serial` queue. However, because the `serial` queue is limited to 24h long jobs, the jobfile present in the gitlab-ci module will resubmit itself to be continuously running. The path of the jobfile is written in the environment variable `$GITLAB_CI_JOBFILE` so the `gitlab-runner` can be submutted and run with:

```sh
sbatch $GITLAB_CI_JOBFILE
```

!!! note
    For initial setup and debugging, the login node can be used for running the gitlab-runner. This can be done using `gitlab-runner run`.
    

## Repository configuration

Because some gitlab CI jobs will be run by slurm, additional parameters and variables are needed in your repository `.gitlab-ci.yml`.

#### With slurm
Here is an example configuration for running a job with slurm. Make sure you adapt the `account` field with your project code:
```yaml
test-slurm:
  variables:
    ON_COMPUTE: "TRUE"
    SLURM_job_name: gitlab-ci-job
    SLURM_account: z19
    SLURM_partition: standard
    SLURM_qos: short
    SLURM_time: "00:03:00"
    SLURM_nodes: 1
    SLURM_tasks_per_node: 2
  script:
    - module load xthi
    - srun xthi_mpi
```

#### Without slurm (login node/serial queue)
If a job doesn't need to be submitted to the compute node but can be run directly on the login node/serial queue, one just need to set `ON_COMPUTE` to `FALSE`.

```yaml
test-local:
  variables:
    ON_COMPUTE: "FALSE"
  script:
    - echo "I am running on $HOSTNAME"
```

#### Complete example
Finally, like any variable in the `.gitlab-ci.yml` they can be defined globally instead of locally per test, or even outside the `gitlab-ci.yml` file. Here is a more realistic complete example: 

```yaml
variables:
  SLURM_job_name: gitlab-ci-job
  SLURM_account: z19
  SLURM_partition: standard
  SLURM_qos: short

test-slurm:
  variables:
    ON_COMPUTE: "TRUE"
    SLURM_time: "00:03:00"
    SLURM_nodes: 1
    SLURM_tasks_per_node: 2
  script:
    - module load xthi
    - srun xthi_mpi

test-local:
  variables:
    ON_COMPUTE: "FALSE"
  script:
    - echo "I am running on $HOSTNAME"
```

The supported list of slurm parameter is:

| SBATCH parameter    | gitlab-ci variable    |
|---------------------|-----------------------|
| `--job-name`        | `SLURM_job_name`      |
| `--time`            | `SLURM_time`          |
| `--nodes`           | `SLURM_nodes`         |
| `--tasks-per-node`  | `SLURM_tasks_per_node`|
| `--cpus-per-task`   | `SLURM_cpus_per_task` |
| `--account`         | `SLURM_account`       |
| `--partition`       | `SLURM_partition`     |
| `--qos`             | `SLURM_qos`           |
| `--exclusive`       | `SLURM_exclusive`     |

!!! note 
    Only strings can be passed as a variable reliably, so make sure to use double quotes (`"`) around `TRUE`/`FALSE`, and `00:02:00` for example or they won't be interpreted properly.


#### Example repository
The repository at https://git.ecdf.ed.ac.uk/slemaire/cse-ci-tests can be used as an example for building an application and running it on the compute node.


