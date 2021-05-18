Pretrained models come as an archive that contains **all three models** (speaker encoder, synthesizer, vocoder). The archive comes with the same directory structure as the repo, and you're expected to merge its contents with the root of the repository.

### [[Download pretrained.zip]](https://github.com/blue-fish/Real-Time-Voice-Cloning/releases/download/v1.0/pretrained.zip)

Please ensure the files are extracted to these locations within your local copy of the repository:
```
encoder\saved_models\pretrained.pt
synthesizer\saved_models\pretrained\pretrained.pt
vocoder\saved_models\pretrained\pretrained.pt
```

Details about model training and audio samples can be found here: https://blue-fish.github.io/experiments/RTVC-7.html

If you're using an older version of the repo code which has the Tensorflow synthesizer, you'll need a different set of [pretrained models](https://github.com/CorentinJ/Real-Time-Voice-Cloning/wiki/Pretrained-models/2cd3887f379d4921b193214973b463043efa5c23).