# Polite Dialogue Generation Without Parallel Data (TACL 2018)

Authors' implementation of "[Polite Dialogue Generation Without Parallel Data](https://arxiv.org/abs/1805.03162)" (TACL 2018) in TensorFlow (the code was built upon TF 1.3, but any version later than that should also work).

Includes code for the politeness classifier and the three proposed polite dialogue generation models.

Authors: Tong Niu, Mohit Bansal ([bib citation entry](https://transacl.org/ojs/index.php/tacl/rt/captureCite/1424/310/BibtexCitationPlugin))

## Politeness Classifier

(1) Obtain the [Stanford Politeness Corpus](http://www.cs.cornell.edu/~cristian/Politeness_files/Stanford_politeness_corpus.zip), unzip it, and put the files under data/

(2) Download the [jar file of the Stanford Postagger](https://nlp.stanford.edu/software/tagger.shtml) (required for tokenization)

(3) Download the [pretrained word2vec embeddings binary file](https://drive.google.com/uc?export=download&confirm=wa0J&id=0B7XkCwpI5KDYNlNUTTlSS21pQmM)

To preprocess the politeness data, please run
```
python3 src/basic/read_csv.py
python3 src/basic/process_requests.py --tagger_path [path to Stanford Postagger jar file] --word2vec [path to pretrained word2vec bin file]
```

To train the politeness classifier from scratch, please run
```
python3 src/model/LSTM-CNN-multi-GPU-new_vocab.py
```

To test the politeness classifier, please run
```
python3 src/model/LSTM-CNN-multi-GPU-new_vocab.py --test --ckpt [name of the checkpoint]
```
The model should get around 85.0% and 70.2% accuracies on the WIKI and SE domains, respectively (for comparison to results from previous works, please refer to [the paper](https://arxiv.org/abs/1805.03162)). 

You can optionally use our trained model [checkpoint](https://drive.google.com/open?id=1593PqiZFk8O1p7095D-8E6KDvxx6j1qQ) by putting it under ckpt/)

## Polite Dialogue Generation
After training the politeness classifier, please put the SubTle corpus (for pretraining) and the MovieTriples dataset under data/.

The SubTle corpus can be obtained by contacting the authors of the [original paper](http://www.inesc-id.pt/publications/10062/pdf), 
and the MovieTriples dataset can be obtained from the [owner of the dataset](https://github.com/julianser) upon request.

To preprocess the SubTle corpus and the MovieTriples dataset, please run
```
python3 process_movie_triples_classifier_seq2seq.py --word2vec [path to pretrained word2vec bin file]
```

(0) To pretrain on the SubTle corpus, please run
```
python3 src/model/seq2seq-RL.py --pretrain
```

(1) To train the Fusion model, please run
```
python3 src/model/fusion_seq2seq_LM.py --ckpt_classifier [name of the checkpoint]
```
(2) To train the Label-Fine-Tuning (LFT) model, please run
```
python3 src/model/seq2seq-LFT.py --ckpt_classifier [name of the checkpoint]
```
(3) To train the Polite-RL model, please run
```
python3 src/model/seq2seq-RL.py --ckpt_classifier [name of the checkpoint]
```

To test the above three models, please add "--test" to each command, the generated responses will be under output/ (you can optionally use our trained model [checkpoints](https://drive.google.com/open?id=1593PqiZFk8O1p7095D-8E6KDvxx6j1qQ) by putting them under ckpt/).
```
python3 src/model/fusion_seq2seq_LM.py --test --ckpt_generator [name of the checkpoint]
python3 src/model/seq2seq-LFT.py --test --ckpt_generator [name of the checkpoint]
python3 src/model/seq2seq_RL.py --test --ckpt_generator [name of the checkpoint]
```

## Citations

If you happen to use our work, please consider [citing our paper](https://transacl.org/ojs/index.php/tacl/rt/captureCite/1424/310/BibtexCitationPlugin).
```
@article{TACL1424,
	author = {Niu, Tong and Bansal, Mohit},
	title = {Polite Dialogue Generation Without Parallel Data},
	journal = {Transactions of the Association for Computational Linguistics},
	volume = {6},
	year = {2018},
	keywords = {},
	abstract = {Stylistic dialogue response generation, with valuable applications in personality-based conversational agents, is a challenging task because the response needs to be fluent, contextually-relevant, as well as paralinguistically accurate. Moreover, parallel datasets for regular-to-stylistic pairs are usually unavailable. We present three weakly-supervised models that can generate diverse, polite (or rude) dialogue responses without parallel data. Our late fusion model (Fusion) merges the decoder of an encoder-attention-decoder dialogue model with a language model trained on stand-alone polite utterances. Our label-fine-tuning (LFT) model prepends to each source sequence a politeness-score scaled label (predicted by our state-of-the-art politeness classifier) during training, and at test time is able to generate polite, neutral, and rude responses by simply scaling the label embedding by the corresponding score. Our reinforcement learning model (Polite-RL) encourages politeness generation by assigning rewards proportional to the politeness classifier score of the sampled response. We also present two retrieval-based, polite dialogue model baselines. Human evaluation validates that while the Fusion and the retrieval-based models achieve politeness with poorer context-relevance, the LFT and Polite-RL models can produce significantly more polite responses without sacrificing dialogue quality.},
	issn = {2307-387X},
	url = {https://transacl.org/ojs/index.php/tacl/article/view/1424},
	pages = {373--389}
}
```
