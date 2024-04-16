## YATO: Yet Another deep learning based Text analysis Open toolkit

# Introduction

**YATO**, an open-source Python library for text analysis. In particular, **YATO** focuses on sequence labeling and sequence classification tasks, including extensive fundamental NLP tasks such as part-of-speech tagging, chunking, NER, CCG supertagging, sentiment analysis, and sentence classification. **YATO** can design both specific RNN-based and Transformer-based through user-friendly configuration and integrating the SOTA pre-trained language models, such as BERT.

**YATO** is a PyTorch-based framework with flexible choices of input features and output structures. The design of neural sequence models with **YATO** is fully configurable through a configuration file, which does not require any code work.

Its previous version called ****NCRF++**** has been accepted as a demo paper by ACL 2018. The in-depth experimental report based on ****NCRF++**** was accepted as the best paper by COLING 2018.

Compared with ****NCRF++****, the highlight of **YATO** is the support for Pre-trained Language Model and sentence classification tasks.

Welcome to star this repository!

![alt text](./readme/framework.jpg "YATO")


## Getting Started

We provide an easy way to use the toolkit **YATO** from PyPI

```bash
pip install ylab-yato
```

Or directly install it from the source  code

```
git clone https://github.com/jiesutd/YATO.git
```

The code to train a Model

```python
from yato import YATO
model = YATO(configuration file)
model.train()
```

The code to decode prediction files:

```python
from yato import YATO
decode_model = YATO(configuration file)
result_dict = decode_model.decode()
```

return dictionary contents following value:

- speed: decoding speed
- accuracy: If the decoded file contains annotation results, accuracy means verifying the accuracy
- precision:  If the decoded file contains annotation results, precision means verifying the precision
- recall:  If the decoded file contains annotation results, recall means verifying the recall
- predict_result: predicted result
- nbest_predict_score: nbest scores of decoded prediction
- label: Mapping between labels and indexes

## Data Format

* Refer [sample_data](sample_data) for the detailed data format.
* **YATO** supports both BIO and BIOES(BMES) tag schemes.
* Notice that IOB format (***different*** from BIO) is currently not supported, because this tag scheme is old and works worse than other schemes [Reimers and Gurevych, 2017](https://arxiv.org/pdf/1707.06799.pdf).
* The differences among these three tag schemes are explained in this [paper](https://arxiv.org/pdf/1707.06799.pdf).
* We provided a [script](utils/tagSchemeConverter.py) which supports convertation of tag scheme among IOB/BIO/BIOES. Welcome to have a try.

## Configuration Preparation

You can specify the model, optimizer, and decoding through the configuration file:

### Training Configuration

#### Dataloader

train_dir=the path of the train file   
dev_dir=the path of the validation file    
test_dir=the path of the test file    
model_dir=the path to save model weights       
dset_dir=the path of configuration encode file         

#### Model

use_crf=True/False        
use_char=True/False            
char_seq_feature=GRU/LSTM/CNN/False          
use_word_seq=True/False          
use_word_emb=True/False          
word_emb_dir=The path of word embedding file          
word_seq_feature=GRU/LSTM/CNN/FeedFowrd/False          
low_level_transformer=pretrain language model from huggingface          
low_level_transformer_finetune=True/False          
high_level_transformer=pretrain language model from huggingface          
high_level_transformer_finetune=True/False          
cnn_layer=layer number          
char_hidden_dim=dimension number          
hidden_dim=dimension number          
lstm_layer=layer number          
bilstm=True/False          

### Hyperparameters

sentence_classification=True/False          
status=train/decode          
dropout=Dropout Rate          
optimizer=SGD/Adagrad/adadelta/rmsprop/adam/adamw          
iteration=epoch number          
batch_size=batch size          
learning_rate=learning rate          
gpu=True/False          
device=cuda:0          
scheduler=get_linear_schedule_with_warmup/get_cosine_schedule_with_warmup          
warmup_step_rate=warmup steo rate          

### Decode Configuration

status=decode          
raw_dir=The path of decode file          
nbest=0 (NER)/1 (sentence classification)          
decode_dir=The path of decode result file          
load_model_dir=The path of model weights          
sentence_classification=True/False          

## Performance

For multiple sequence labeling and sequence classification tasks, **YATO** has reproduced or outperformed the reported SOTA results on majority datasets.

By default, the `LSTM` is a bidirectional LSTM. The `BERT-base` is huggingface's bert-base-uncased. The `RoBERTa-base` is huggingface's roberta-base. The `ELECTRA-base` is huggingface's google/electra-base-discriminator.

#### Results for sequence labeling tasks.

| ID | Model          | CoNLL2003 | OntoNotes 5.0 | 
| -- | -------------- | --------- | ------------- | 
| 1  | BERT-base      | 89.70     | 84.05         |


#### Results for sequence classification tasks.

| ID | Model        | SST2  | 
| -- | ------------ | ----- | 
| 1  | BERT-base    | 92.70 | 


For more details, you can refer to our papers mentioned below.

The results based on Pretrain Language Model were recorded in [YATO: Yet Another deep learning based Text analysis Open toolkit]()


## N best Decoding

The traditional CRF structure decodes only one label sequence with the largest probabilities (i.e. 1-best output). In contrast, **YATO** can decode `n` label sequences with the top `n` probabilities (i.e. n-best output). The nbest decoding has been supported by several popular **statistical** CRF frameworks.


## Text Attention Heatmap Visualization

**YATO** takes the list of words and the corresponding weights as input to generate Latex code for visualizing the attention-based result.

The Latex code will generate a separate .pdf visualization file.

![alt text](./readme/attention.png "Visualization of attention map in Transformer-based model")

For example,

```python
from yato import YATO
from utils import text_attention
model = YATO(decode configuration file)

sample = ["a fairly by-the-books blend of action and romance with sprinklings of intentional and unintentional comedy . ||| 1"]
probsutils, weights_ls = model.attention(input_text=sample)
sentece = "a fairly by-the-books blend of action and romance with sprinklings of intentional and unintentional comedy . "
atten = weights_ls[0].tolist()

text_attention.visualization(sentece, atten[0], tex = 'sample.tex', color='red')
```

## Reproduce Paper Results and Hyperparameter Tuning

To reproduce the results of CoNLL2003 and SST2 in our paper, you only need to use the configuration file `bert_base_conll2003.config`, `bert_base_gelu_sst2.config` and then configure your file directory.

The default configuration file adopts the `Pure PLM ` model, and you can develop your model by modifying the configuration accordingly. 

If you want to use this framework in new tasks or datasets, here are some tuning [tips](readme/hyperparameter_tuning.md) by @Victor0118.

## Report Issue or Problem

If you want to report an issue or ask a problem, please attach the following materials if necessary. With these information, we can provide a fast and accurate discussion and the corrsponding suggestions.

* `log file`
* `config file`
* `sample data`

## Cite

If you use **YATO++** in your paper, please cite our [paper](https://arxiv.org/abs/2209.13877):

    @inproceedings{yang2022yato,
    title={YATO: Yet Another deep learning based Text analysis Open toolkit},
    author={Wang, Zeqiang and Wang, Yile and Wu, Jiageng and Teng, Zhiyang and Yang, Jie},
    year={2022}
    }


## Future Plan

* Support API usage
* Release trained model on various sequence labeling and classification tasks

## Updates

* 2022-May-14  **YATO**, init version
* 2020-Mar-06, dev version, sentence classification, framework change, model saved in one file.
* 2018-Dec-17, **NCRF++** v0.2, support PyTorch 1.0
* 2018-Mar-30, **NCRF++** v0.1, initial version
* 2018-Jan-06, add result comparison.
* 2018-Jan-02, support character feature selection.
* 2017-Dec-06, init version
