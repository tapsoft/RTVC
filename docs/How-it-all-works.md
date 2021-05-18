This page explains the principle behind SV2TTS and how you can interface the repo in your own projects.

## Interface
At the root of the repo, you have a directory (= a python module) for each of the three models of the SV2TTS framework: the **encoder**, the **synthesizer** and the **vocoder**. You also have **preprocessing** and **training** python scripts, we won't need them as they're used for [training](https://github.com/CorentinJ/Real-Time-Voice-Cloning/wiki/Training) the models.

Each of the three modules contains code to preprocess data, visualize data, load data, train the model, define hyperparameters and finally to **use the model for inference**. This last inference interface is exposed as a `<model_name>/inference.py` for each model. These are the only files you will need to import to use the models.

## Models
The subsections below explain how each model works and what functions are available for each. For each model, the source papers are linked so that you can read up more on them, there's also a summary for each in [my thesis](https://puu.sh/DHgBg.pdf).

### Encoder
TODO

### Synthesizer
TODO

### Vocoder
TODO
