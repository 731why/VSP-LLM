# VSP-LLM (Visual Speech Processing incorporated with LLMs)

This is the PyTorch code for [Where Visual Speech Meets Language: VSP-LLM Framework for Efficient and Context-Aware Visual Speech Processing](https://arxiv.org/abs/2402.15151). This code is developed on the code of [AV-HuBERT](https://github.com/facebookresearch/av_hubert).

- [ ] add colab demo

## Introduction

We propose a novel framework, namely Visual Speech Processing incorporated with LLMs (VSP-LLM), to maximize the context modeling ability by bringing the overwhelming power of LLMs. Specifically, VSP-LLM is designed to perform multi-tasks of visual speech recognition and translation, where the given instructions control the type of task. The input video is mapped to the input latent space of a LLM by employing a self-supervised visual speech model. Focused on the fact that there is redundant information in input frames, we propose a novel deduplication method that reduces the embedded visual features by employing visual speech units. Through the proposed deduplication and Low Rank Adaptors (LoRA), VSP-LLM can be trained in a computationally efficient manner.

![vsr-vst](docs/demo.gif)

## Model checkpoint

You can find checkpoint of our model in [here](https://drive.google.com/file/d/1fQarnmP5MEMzYCodphisr4QuViqVwuI5/view?usp=sharing)

## Demo

Try our VSP-LLM demo using colab

## Preparation

```
conda create -n vsp-llm python=3.9 -y
conda activate vsp-llm
git clone https://github.com/Sally-SH/VSP-LLM.git
cd VSP-LLM
pip install -r requirements.txt
```

- Download AV-HuBERT pre-trained model `AV-HuBERT Large (LSR3 + VoxCeleb2)` from [here](http://facebookresearch.github.io/av_hubert).
- Download LLaMA2-7B from [here](https://huggingface.co/meta-llama/Llama-2-7b-chat-hf).

## Dataset
We use [LRS3](https://mmai.io/datasets/lip_reading/) for VSR task and [MuAViC](https://github.com/facebookresearch/muavic) for VST task.

## Data preprocessing

Follow [Auto-AVSR preperation](https://github.com/mpc001/auto_avsr/tree/main/preparation) to preprocess the LRS3 dataset. This will create two directories: `lrs3_video_seg24s` and `lrs3_text_seg24s`.\
The LRS3 dataset manifest is provided in [here](https://drive.google.com/file/d/1tzUaO25zkAYD9IjKxzf9MIP543ZFE8en/view?usp=sharing). Unzip this zip file in [dataset](src/dataset/). Then, you need to replace the path of the LRS3 in the manifest file with your preprocessed LRS3 dataset path using the following command:

```bash
cd src/dataset
python replace_path.py --lrs3 /path/to/lrs3 --outdir /path/to/save
```

### Generate visual speech unit and cluster counts file

If you want to create a "cluster_counts" file for deduplication on your own, follow the steps in [`clustering`](src/clustering/).

### Dataset layout

    .
    ├── lrs3
    │     ├── lrs3_video_seg24s               # preprocessed video and audio data
    │     └── lrs3_text_seg24s                # preprocessed text data
    └── lrs3_dataset
          ├── train.tsv                       # List of audio and video path for training
          ├── train.wrd                       # List of target label for training
          ├── train.cluster_counts            # List of clusters to deduplicate speech units in training
          ├── test.tsv                        # List of audio and video path for testing
          ├── test.wrd                        # List of target label for testing
          └── test.cluster_counts             # List of clusters to deduplicate speech units in testing

## Training

Open the training script ([`scripts/train.sh`](https://github.com/Sally-SH/VSP-LLM/blob/main/scripts/train.sh)) and replace these variables:

```bash
# path to downloaded pre-trained avhubert
PRETRAINED_MODEL_PATH=???

# path to train dataset dir
DATA_PATH=???

# path to llama checkpoint
LLM_PATH=???

# path where output trained models will be located
OUT_PATH=???
```

Run the training script:

```bash
$ bash scripts/train.sh
```

## Decoding

Open the decoding script ([`scripts/decode.sh`](https://github.com/Sally-SH/VSP-LLM/blob/main/scripts/decode.sh)) and replace these variables:

```bash
# language direction (e.g "en" or "en-fr")
LANG=???

# path to the trained model
MODEL_PATH=???

# path to test dataset dir
DATA_PATH=???

# path to llama checkpoint
LLM_PATH=???

# path where decoding results and scores will be located
OUT_PATH=???
```

Run the decoding script:

```bash
$ bash scripts/decode.sh
```
