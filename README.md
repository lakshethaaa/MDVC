<img src="https://github.com/v-iashin/v-iashin.github.io/raw/master/images/mdvc/MDVC.svg" alt="MDVC" width="900">

# Multi-modal Dense Video Captioning

[Project Page](https://v-iashin.github.io/mdvc) | [Proceedings](http://openaccess.thecvf.com/content_CVPRW_2020/html/w56/Iashin_Multi-Modal_Dense_Video_Captioning_CVPRW_2020_paper.html) | [ArXiv](https://arxiv.org/abs/2003.07758) | [Presentation](https://www.youtube.com/watch?v=0Vmx_gzP1bM) ([Mirror](https://a3s.fi/swift/v1/AUTH_a235c0f452d648828f745589cde1219a/mdvc/14-oral.mp4))

This is a PyTorch implementation of our paper Multi-modal Dense Video Captioning (CVPR Workshops 2020).

The publication will appear in the conference proceedings of CVPR Workshops. Please, use this bibtex citation
```
@InProceedings{MDVC_Iashin_2020,
  author = {Iashin, Vladimir and Rahtu, Esa},
  title = {Multi-Modal Dense Video Captioning},
  booktitle = {The IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR) Workshops},
  pages={958--959},
  year = {2020}
}
```

If you found this work interesting, [check out](https://v-iashin.github.io/bmt) our latest paper, where we propose a novel architecture for the dense video captioning task called **Bi-modal Transformer with Proposal Generator**.

## Usage
_The code is tested on `Ubuntu 16.04/18.04` with one `NVIDIA GPU 1080Ti/2080Ti`. If you are planning to use it with other software/hardware, you might need to adapt `conda` environment files or even the code._

Clone this repository. Mind the `--recursive` flag to make sure `submodules` are also cloned (evaluation scripts for Python 3).
```bash
git clone --recursive https://github.com/v-iashin/MDVC.git
```

Download features [I3D (17GB)](https://a3s.fi/swift/v1/AUTH_a235c0f452d648828f745589cde1219a/mdvc/sub_activitynet_v1-3.i3d_25fps_stack24step24_2stream.hdf5), [VGGish (1GB)](https://a3s.fi/swift/v1/AUTH_a235c0f452d648828f745589cde1219a/mdvc/sub_activitynet_v1-3.vggish.hdf5) and put in `./data/` folder (speech segments are already there). You may use `curl -O <link>` to download the features.

```
# MD5 Hash
a661cfe3535c0d832ec35dd35a4fdc42  sub_activitynet_v1-3.i3d_25fps_stack24step24_2stream.hdf5
54398be59d45b27397a60f186ec25624  sub_activitynet_v1-3.vggish.hdf5
```

Setup `conda` environment. Requirements are in file `conda_env.yml`
```bash
# it will create new conda environment called 'mdvc' on your machine
conda env create -f conda_env.yml
conda activate mdvc
# install spacy language model. Make sure you activated the conda environment
python -m spacy download en
```

## Train and Predict
Run the training and prediction script. It will, first, train the captioning model and, then, evaluate the predictions of the best model in the learned proposal setting. It will take ~24 hours (50 epochs) to run on a 2080Ti GPU. Please, note that the performance is expected to reach its peak after ~30 epochs.
```bash
# make sure to activate environment: conda activate mdvc
# the cuda:1 device will be used for the run
python main.py --device_ids 1
```
The script keeps the log files, including `tensorboard` log, under `./log` directory by default. You may specify other path using `--log_dir` argument. Also, if you stored the downloaded data (`.hdf5`) files in another directory other than `./data`, make sure to specify it using `–-video_features_path` and `--audio_features_path` arguments.

You may also download the pre-trained model [here (~2 GB)](https://a3s.fi/swift/v1/AUTH_a235c0f452d648828f745589cde1219a/mdvc/best_model.pt).
```
# MD5 Hash
55cda5bac1cf2b7a803da24fca60898b  best_model.pt
```

## Evaluation Scrips and Results
If you want to skip the training procedure, you may replicate the main results of the paper using the prediction files in `./results` and the [official evaluation script](https://github.com/ranjaykrishna/densevid_eval/tree/9d4045aced3d827834a5d2da3c9f0692e3f33c1c).

1. To evaluate the performance in the learned proposal set up, run the official evaluation script on `./results/results_val_learned_proposals_e30.json`. Our final result is 6.8009
2. To evaluate the performance on ground truth segments, run the script on each validation part (`./results/results_val_*_e30.json`) against the corresponding ground truth files (use `-r` argument in the script to specify each of them). When both values are obtained, average them to verify the final result. We got 9.9407 and 10.2478 on `val_1` and `val_2` parts, respectively, thus, the average is 10.094.

As we mentioned in the paper, we didn't have access to the full dataset as [ActivityNet Captions](https://cs.stanford.edu/people/ranjaykrishna/densevid/) is distributed as the list of links to YouTube video. Consequently, many videos (~8.8 %) were no longer available at the time when we were downloading the dataset. In addition, some videos didn't have any speech. We filtered out such videos from the validation files and reported the results as `no missings` in the paper. We provide these filtered ground truth files in `./data`.

## Raw Data & Details on Feature Extraction
If you are feeling brave, you may want extract features on your own. Check out our script for extraction of the I3D and VGGish features from a set of videos: [video_features on GitHub](https://github.com/v-iashin/video_features/tree/4fa02bd5c5b8c34081dcfb609e2bcd5a973eaab2) (make sure to check out to `4fa02bd5c5b8c34081dcfb609e2bcd5a973eaab2` commit). Also see [#7](https://github.com/v-iashin/MDVC/issues/7) for more details on configuration. We also provide the script used to process the timestamps `./utils/parse_subs.py`.

## Misc.
We additionally provide
- the file with subtitles with original timestamps in `./data/asr_en.csv`
- the file with video categories in `./data/vid2cat.json`

## Acknowledgments
Funding for this research was provided by the Academy of Finland projects 327910 & 324346. The authors acknowledge CSC — IT Center for Science, Finland, for computational resources for our experimentation.

## Media Coverage
- (in Russian) [Рубрика «Читаем статьи за вас». Июнь 2020 года (habr.com)](https://habr.com/ru/company/ods/blog/515688/)
