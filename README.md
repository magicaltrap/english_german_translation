# About
In fastai's [Code-First Introduction to Natural Language Processing](https://www.fast.ai/2019/07/08/fastai-nlp/) course in chapter 7, various Machine Translation architectures were covered. In the mentioned lessons, around 40000 French questions were used to translate into English. I copied the notebooks and wanted to reproduce the results on a bigger English-German dataset. Specifically, I wanted to compare different Machine Translation architectures regarding accuracy (e.g. BLEU scores) and efficiency (training time). Check the fastai NLP notebooks for more [details](https://github.com/fastai/course-nlp), they are a great read! 

# Dataset
As training set, I decided to take a English-German parallel corpus from the latest WMT 2019 Conference ([ACL 2019: Fourth Conference on Machine Translation](http://www.statmt.org/wmt19/)). I chose the `News Commentary v14` corpus which has **338285** English/German parallel sentences. For validation and test sets, we have two options. Either 1) take a small subset from our training set and label them as validation and/or test set or 2) we download a separate validation and/or test set from different sources. We can do both just in case. For option 2) let's download the `newstest2013` and  `newstest2014` corpora as validation and test sets respectively. Those two corpora are popular in Machine Translation research to validate MT models. The original Transformer paper (["Attention Is All You Need"](https://arxiv.org/abs/1706.03762)) used those two corpora as well for example. You can download them [here](https://google.github.io/seq2seq/data/). 

For my experiments, I decided to take 10% from the `News Commentary v14` corpus as validation set. Moreover, I just keep the sentences that have less than 25 tokens per sentence. This will make training a bit faster and likely more accurate (the longer a sentence the lower the BLEU score tend to be). With this setup, we have **137400** language pairs for training and **15335** language pairs for validation. This is not a lot for Machine Translation models (a few million language pairs for training is not uncommon) but it should be enough for experimenting. All the preprocessing (+ creating embeddings for our sentences) are done in the `preprocessing_embedding.ipynb` file.

A few (lower-cased) examples from our `News Commentary v14` corpus: 

| ID          |   English  |  German   | 
|-------------|-------------|----------|
| 0 | in china, people call it fazhan , or “development,” but in much of rest of the world, it is more commonly described simply as the “china boom,” or the “china miracle.” | in china sagt man dazu fazhan , also „entwicklung”,  aber in den meisten teilen der welt wird dieses phänomen einfach als „china-boom“ oder „chinesisches wunder“ bezeichnet. |  
| 1 | such pessimism is warranted. | ein derartiger pessimismus ist gerechtfertigt. |  
| 2 | that is also what, if you take her literally, merkel is doing now. | dasselbe tut derzeit auch merkel, wenn man sie beim wort nimmt. |
| 3 | 	it proved to be the tipping point, triggering waves of protests by lawyers and other groups in pakistan’s main cities. | der vorfall erwies sich als wendepunkt, der wellen des protests von anwälten und anderen gruppen in pakistans größten städten auslöste. |


# Different models
1. seq2seq model with GRUs (`seq2seq-translation.ipynb`)
2. seq2seq model with GRUs + Teacher's Forcing (`seq2seq-translation.ipynb`)
3. seq2seq model with GRUs + Teacher's Forcing + Attention (`seq2seq-translation-attention.ipynb`)
4. Transformer model (`translation-transformer.ipynb`)

# Results

Due to time and GPU restrictions (I used Google Colaboratoy's free GPUs), I only trained each model for around 1-2h and a few epochs (4-8 epochs). The results would definitely be better with more training but it's sufficient for experimenting. For each model, I take the results from the last trained epoch:

| model                   | train_loss | valid_loss | seq2seq_acc | bleu    | avg train time per epoch |
|-------------------------|------------|------------|-------------|---------|--------------------------|
| **seq2seq**             | 4.598033 | 5.671896 | 0.259412 | 0.175585 | 21:26 min |
| **+ Teacher's Forcing** | 4.939337 | 6.043734 | 0.240516 | 0.165269 |	23:22 min | 
| **+ Attention**         | 3.952050 | 5.452516 | 0.313406 | 0.211178 | 27:27 min |  
| **Transformer**         | 1.210745 | 1.714322 | 0.716311 | 0.484944	| 11:20 min |
 
 
 # Example outputs (4x)
 
Here some German sentences that our MT model outputted:  
 
|  Source                     |  Output
|-----------------------------|------------------------------------------|
| **English source sentence** | trump ’s simplistic , xenophobic rhetoric will also find a sympathetic audience among poles and hungarians who fear large - scale immigration . |
| **German target sentence**  | trumps vereinfachende , xxunk rhetorik wird auch bei jenen polen und ungarn anklang finden , die sich vor umfangreicher zuwanderung fürchten . |
| **seq2seq**                 | trumps xxunk xxunk xxunk , , , , , , , , und und und und xxunk xxunk . . |
| **+ Teacher's Forcing**     | trumps xxunk xxunk xxunk , , , xxunk xxunk und , , , , und und xxunk xxunk . |
| **+ Attention**             | die xxunk xxunk , die xxunk , die xxunk , die xxunk und xxunk und xxunk und xxunk . |
| **Transformer**             | trumps allzu , fremdenfeindliche rhetorik wird auch unter polen polen und ungarn mit finden , die die vor großen einwanderung fürchten . |


|  Source                     |  Output
|-----------------------------|------------------------------------------|
| **English source sentence** | my portal to the world of artificial intelligence is a narrow one : the more than xxunk - old game of chess . |
| **German target sentence**  | mein eigener zugang zur welt der künstlichen intelligenz ist ein eng xxunk : das über 500 jahre alte xxunk schach . |
| **seq2seq**                 | ich der der der der der der der der xxunk xxunk xxunk xxunk xxunk xxunk xxunk xxunk xxunk |
| **+ Teacher's Forcing**     | meine xxunk der der der der ist ist ein , , , , xxunk xxunk xxunk xxunk . |
| **+ Attention**             | die der der der der der der der xxunk , die xxunk , die xxunk xxunk xxunk . |
| **Transformer**             | meine xxunk xxunk zur welt der künstlichen intelligenz ist ein eng xxunk : je mehr xxunk - alten spiel . . |


|  Source                     |  Output
|-----------------------------|------------------------------------------|
| **English source sentence** | the transition from autocracy to democracy in the middle east is likely to be bumpy and unstable , at best .|
| **German target sentence**  |  der übergang von der autokratie zur demokratie i m nahen osten wird wahrscheinlich holprig und instabil verlaufen – bestenfalls . |
| **seq2seq**                 | der der der nahen osten osten osten osten osten osten osten osten und und und osten . . |
| **+ Teacher's Forcing**     | der kampf der der in in in osten in osten ist und und und und und . |
| **+ Attention**             | die der der der der der der in der der in der der in der xxunk , , , die zu zu . |
| **Transformer**             | der übergang von autokratie autokratie zur demokratie i m nahen osten dürfte bestenfalls ein und instabil sein . bestenfalls . |


|  Source                |  Output
|-------------------------|------------------------------------------|
| **English source sentence** | but it is time to go further and begin to lobby aggressively for faster financial liberalization in the developing world . |
| **German target sentence**  | es ist allerdings an der zeit , sich darüber hinaus mit mehr nachdruck für eine raschere finanzliberalisierung in den entwicklungsländern einzusetzen . |
| **seq2seq**                 | aber es es zeit zeit , , , , die die die die in in zu zu zu . |
| **+ Teacher's Forcing**     | es ist es zeit zeit , , , und , die die die die die die der zu |
| **+ Attention**             | aber ist es , , die zu und und die zu und zu zu zu zu zu zu zu . |
| **Transformer**             | doch ist jedoch an der zeit , sich weiter zu weiter einer zu zu eine schnellere liberalisierung in den entwicklungsländern zu . |




# Summary
I tried to translate English sentences into German with different Machine Translation architectures. By using **137400** language pairs for training and **15335** language pairs for validation, we can see that the Transformer outperforms the other architectures by far. Looking at the example outputs, the Transformer model is the only model that doesn't output repetitive words and can get the gist of the English source. I'm sure that with a bigger training set and more training time (I only trained for an hour) that the outputs will be better. Looking at the BLEU score for the Transformer of **0.484944** , there's a dramatic jump from the other models, more than double. The Transformer's training time per epoch is also less than half compared to the other models. 
