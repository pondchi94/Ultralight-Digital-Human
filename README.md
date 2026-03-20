# Ultralight Digital Human

<p align="center">
    <a href="./LICENSE"><img src="https://img.shields.io/badge/license-Apache%202-dfd.svg"></a>
    <a href=""><img src="https://img.shields.io/badge/python-3.10-aff.svg"></a>
    <a href="https://github.com/anliyuan/Ultralight-Digital-Human/stargazers"><img src="https://img.shields.io/github/stars/anliyuan/Ultralight-Digital-Human?color=ccf"></a>
  <br>
    <br>
</p>

A Ultralight Digital Human model can run on mobile devices in real time!!!

Lets see the demo.⬇️⬇️⬇️

![DigitalHuman](https://github.com/user-attachments/assets/9d0b37ee-2076-4b4f-93ba-eb939a9fb427)

## Train

It's so easy to train your own digital human.I will show you step by step.

### install pytorch and other libs

``` bash
conda create -n udh python=3.12
conda activate udh
conda install pytorch torchvision torchaudio 

pip install -r requirement.txt
```

I only ran on pytorch==1.13.1, Other versions should also work.

Download wenet encoder.onnx from https://drive.google.com/file/d/1e4Z9zS053JEWl6Mj3W9Lbc9GDtzHIg6b/view?usp=drive_link 

and put it in data_utils/

### Data preprocessing

Prepare your video, 3~5min is good. Make sure that every frame of the video has the person's full face exposed and the sound is clear without any noise, put it in a new folder.I will provide a demo video.

First of all, we need to extract audio feature.I'm using 2 different extractor from wenet and hubert, thank them for their great work.

When you using wenet, you neet to ensure that your video frame rate is 20, and for hubert,your video frame rate should be 25.

In my experiments, hubert performs better, but wenet is faster and can run in real time on mobile devices.

And other steps are in data_utils/process.py, you just run it like this.

``` bash
cd data_utils
python process.py --video <xxx.mp4> --asr hubert
```

Then you wait.

### train

After the preprocessing step, you can start training the model.
Train a syncnet first for better results.


``` bash
cd ..
python syncnet.py --save_dir ./output/syncnet_ckpt/ --dataset_dir ./output/xxx --asr hubert
```

Then find a best one（low loss） to train digital human model.

``` bash
cd ..
python train.py --dataset_dir ./output/xxx --save_dir ./output/checkpoint/ --asr hubert --use_syncnet --syncnet_checkpoint ./output/syncnet_ckpt/<yy.pth>
```

## inference

Before run inference, you need to extract test audio feature(i will merge this step and inference step), run this


``` bash
python data_utils/hubert.py --wav your_test_audio.wav  # when using hubert

or

python data_utils/wenet_infer.py --wav your_test_audio.wav  # when using wenet
```

then you get your_test_audio_hu.npy or your_test_audio_wenet.npy

then run
``` bash
python inference.py --asr hubert --dataset ./your_data_dir/ --audio_feat your_test_audio_hu.npy --save_path xxx.mp4 --checkpoint your_trained_ckpt.pth
```

To merge the audio and the video, run

``` bash
ffmpeg -i xxx.mp4 -i your_audio.wav -c:v libx264 -c:a aac result_test.mp4
```

## Enjoy🎉🎉🎉

if you have some advice, open an issue or PR.
If you think this repo is useful to you, please give me a star.

<table>
  <tr>
    <td><img src="demo/wechat.jpeg" width="180"/></td>
  </tr>
</table>

