# Randomize the order of GPUs for SLURM job allocation

SLURM always allocate jobs to uses available GPUs of lowest IDs, thus causes GPU 0 to wear down faster than GPU 9.

This program randomizes the order of GPUs at the start of slurmd so that jobs are not first allocated to physical GPU 0.

## Requirements
The system must install z shell. 

The GPU devices must be named `/dev/nvidia[0-9]#`. More than 10 GPUs are supported in this program. 

## Install

1. Run the following
```bash
git clone --depth=1 https://github.com/gqqnbig/randomize-gpu/
cat <<SERVICE > /etc/systemd/system/slurmd.service.d/randomize-gpu.conf
[Service]
ExecStartPre=$PWD/randomize-gpu
SERVICE
```

2. Revise gres.conf to read `/dev/rNvidia[0-9]` instead of `/dev/nvidia[0-9]`. Change 9 to the actual number of your GPUs.

3. Restart slurmd.

## Verify
After installing (and you have restarted slurmd), you shall see rNvidia files in /dev. 

Submit a SLURM requesting multiple GPUs, you should see the job is not using GPUs of lowest IDs.

## Uninstall
1. Delete the local copy of this repository.

2. Delete `/etc/systemd/system/slurmd.service.d/randomize-gpu`.

3. Revise gres.conf to read `/dev/nvidia[0-9]`. Change 9 to the actual number of your GPUs.
