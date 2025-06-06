# Meta-ASr
[IJCV 2024] Towards Task Sampler Learning for Meta-Learning


## Create Environment

For easier use and to avoid any conflicts with existing Python setup, it is recommended to use [`virtualenv`](https://docs.python-guide.org/dev/virtualenvs/) to work in a virtual environment. Now, let's start:

**Step 1:** Install [`virtualenv`](https://docs.python-guide.org/dev/virtualenvs/)

```bash
pip install --upgrade virtualenv
```

**Step 2:** Create a virtual environment, activate it:

```bash
virtualenv venv
source venv/bin/activate
```

**Step 3:** Install the requirements in [`requirements.txt`](requirements.txt).

```bash
pip install -r requirements.txt
```

## Data Availability

All data sets used in this work are open source. The download and deployment ways are as follows:
​
* miniImageNet, Omniglot, and tieredImageNet will be downloaded automatically upon runnning the scripts, with the help of [pytorch-meta](https://github.com/tristandeleu/pytorch-meta).

* For ['meta-dataset'](https://github.com/google-research/meta-dataset/blob/e95c50658e4260b2ede08ede1129827b08477f1a/prepare_all_datasets.sh), follow the following steps: Download ILSVRC2012 (by creating an account [here](https://image-net.org/challenges/LSVRC/2012/index.php) and downloading `ILSVRC2012.tar`) and Cu_birds2012 (downloading from `http://www.vision.caltech.edu/visipedia-data/CUB-200-2011/CUB_200_2011.tgz`) separately. Then, Run `sbatch scripts/download_meta_dataset/install_meta_dataset_parallel.sh` to download and prune all datasets in a parallel fashion. All the ten datasets should be copied in a single directory.

* For the few-shot-regression setting, Sinusoid, Sinusoid & Line, and Harmonic dataset are toy examples and require no downloads. Just follow the implementation in the paper.

Now, you have completed all the settings, just directly train and test as you want :)


## Train

We offer two ways to run our code (Take [`MAML`](scripts/MAML) with [`meta-dataset`](scripts/MAML/Train/train_maml_metadataset_all_samplers.sh) as an example):

**Way 1:** Train all samplers and models in a parallel fashion using the carefully organized [`scripts`](scripts), which is as follows:

```bash
sbatch scripts/MAML/Train/train_maml_<dataset>_all_samplers.sh
```

**Way 2:** Directly write:

```bash
python -m src.main --exp_name maml_meta_dataset --train --runs 1 --folder $SLURM_TMPDIR/records --task_sampler $SLURM_ARRAY_TASK_ID --dataset meta_dataset --num-ways 5 --num-shots 1 --use-cuda --num-steps 5 --step-size 0.4 --meta-lr 0.001 --batch-size 16 --num-workers 0 --num-epochs 150 --num-adaptation-steps 5 --output-folder ./config/maml_meta_dataset_try_3/$SLURM_ARRAY_TASK_ID/
```

## Test

Similarly, all the models can be tested on a fixed set of tasks in a parallel fashion as follows:

```bash
sbatch scripts/MAML/Test/test_maml_<dataset>_all_samplers.sh
```

or

```bash
python -m src.main --exp_name test_maml_meta_dataset --log-test-tasks --runs 1 --folder $SLURM_TMPDIR/records --task_sampler $SLURM_ARRAY_TASK_ID --dataset meta_dataset --num-ways 5 --num-shots 1 --use-cuda --num-steps 5 --step-size 0.4 --meta-lr 0.001 --batch-size 1 --num-workers 0 --num-epochs 150 --output-folder ./config/maml_meta_dataset_try_2/$SLURM_ARRAY_TASK_ID/
```

## View Results and Analysis

To collect statistics and view results, the corresponding code are also provide, run:

```bash
python -m src.analysis.py <path_to_task_json> -O <path_to_output_json>
```

or 

Uncomment the `print` in the code.

In addition, if you are drawing pictures, such as line charts, bar charts, or scatter charts, you can refer to [draw-Figures](https://github.com/WangJingyao07/draw-Figures) =)


## Citation
```
@article{wang2024towards,
  title={Towards Task Sampler Learning for Meta-Learning},
  author={Wang, Jingyao and Qiang, Wenwen and Su, Xingzhe and Zheng, Changwen and Sun, Fuchun and Xiong, Hui},
  journal={International Journal of Computer Vision},
  pages={1--31},
  year={2024},
  publisher={Springer}
}
```
