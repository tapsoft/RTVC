This is a step-by-step guide for reproducing the training. For explanations about the framework and how it works, see [TODO].

## Datasets
There's a lot of data involved in fully training the three models. You'd want at least 500 gb of free space, and that's if you delete datasets after you used them. With 1 tb you're fine. 

Ideally, you want to keep all your datasets under a same directory. All prepreprocessing scripts will, by default, output the clean data to a new directory  `SV2TTS` created in your datasets root directory. Inside this directory will be created a directory for each model: the encoder, synthesizer and vocoder.

You will need the following datasets:

For the encoder:
- **[LibriSpeech](http://www.openslr.org/12/):** train-other-500 (extract as `LibriSpeech/train-other-500`)
- **[VoxCeleb1](http://www.robots.ox.ac.uk/~vgg/data/voxceleb/vox1.html):** Dev A - D as well as the metadata file (extract as `VoxCeleb1/wav` and `VoxCeleb1/vox1_meta.csv`)
- **[VoxCeleb2](http://www.robots.ox.ac.uk/~vgg/data/voxceleb/vox2.html):** Dev A - H (extract as `VoxCeleb2/dev`)

For the synthesizer and the vocoder: 
- **[LibriSpeech](http://www.openslr.org/12/):** train-clean-100, train-clean-360 (extract as `LibriSpeech/train-clean-100` and `LibriSpeech/train-clean-360`)
- **[LibriSpeech alignments](https://github.com/CorentinJ/librispeech-alignments#download-links):** take the first link and merge the directory structure with the LibriSpeech datasets you have downloaded (do not take the alignments from the datasets you haven't downloaded else the scripts will think you have them)


Feel free to adapt the code to your needs. Other interesting datasets that you could use include **[LibriTTS](http://www.openslr.org/60/)** (paper [here](https://arxiv.org/abs/1904.02882)) which I already tried to implement but couldn't finish in time, **[VCTK](https://homepages.inf.ed.ac.uk/jyamagis/page3/page58/page58.html)** (used in the SV2TTS paper) or **[M-AILABS](https://www.caito.de/2019/01/the-m-ailabs-speech-dataset/)**.


## Preprocessing and training
Here's the great thing about this repo: you're expected to run all python scripts in their alphabetical order. You likely started with the demo scripts, now you can run the remaining ones (pass `-h` to get argument infos for any script): 

`python encoder_preprocess.py <datasets_root>`

For training, the encoder uses visdom. You can disable it with `--no_visdom`, but it's nice to have. Run "visdom" in a separate CLI/process to start your visdom server. Then run:

`python encoder_train.py my_run <datasets_root>/SV2TTS/encoder`

Here's what the visdom environment looks like:

![Visdom](https://i.imgur.com/rB1xk0b.png)

Then you have two separate scripts to generate the data of the synthesizer. This is convenient in case you want to retrain the encoder, you will then have to regenerate embeddings for the synthesizer.

Begin with the audios and the mel spectrograms:

`python synthesizer_preprocess_audio.py <datasets_root>`

Then the embeddings:
 
`python synthesizer_preprocess_embeds.py <datasets_root>/SV2TTS/synthesizer`

You can then train the synthesizer:

`python synthesizer_train.py my_run <datasets_root>/SV2TTS/synthesizer`

The synthesizer will output generated audios and spectrograms to its model directory when training. Refer to https://github.com/Rayhane-mamah/Tacotron-2 if you need help.

Use the synthesizer to generate training data for the vocoder:

`python vocoder_preprocess.py <datasets_root>`

And finally, train the vocoder:

`python vocoder_train.py my_run <datasets_root>`

The vocoder also outputs ground truth/generated audios to its model directory.
 