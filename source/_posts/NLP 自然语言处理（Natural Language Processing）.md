---
title: NLP 自然语言处理（Natural Language Processing）
date: 2021-09-08 16:57:06
categories: 学习日志
tags:
  - 神经网络
  - 自然语言处理
toc: true
---

## 前言

跟着师兄做知识图谱相关课题，果然知识图谱还是🉐和NLP结合，那就来把，先过一遍NLP。

<!--more-->

早期的自然语言处理是基于规则来建立词汇、句法语义分析。

后来的统计自然语言处理基于人工定义的特征建立机器学习系统。

现在基本上都是神经网络自然语言处理，之前的NLP方法简单做了些了解，接下来的学习就是从神经网络自然语言处理开始了。

## 神经网络自然语言处理的基本思想

### 理解词语

- 词向量
    把词映射到空间中，根据夹角、距离和长短等判断词的相似性。

- Continuous Bag of Words (CBOW)
    前后的词预测中间的词

- Skip-Gram
    中间的词预测前后的词

### 理解句子

- 句向量

    通过模型，得到的机器对于一句话的理解，对于这个理解，也就是向量，使用的方法不止一种。

- Encoding

    得到词向量之后，使用神经网络做复杂乘法运算得到句向量，也就是把多个词空间向量通过神经网络映射到另一个空间做句向量。
    Encoding过程类似压缩的过程，将大量复杂的信息，压缩成少量经典的信息，通过这个途径找到信息的精华部分。

- Decoding

    Encoding得到的句向量就是机器对句子的理解，那么可以用另一个神经网络对句向量做各种各样的解码操作。
    简而言之，Encoder负责理解上文，Decoder负责将思考怎么样在理解的句子的基础上做任务。

- Seq2Seq

    seq2seq属于encoder-decoder结构的一种，常见的encoder-decoder结构，基本思想就是利用两个RNN，一个RNN作为encoder，另一个RNN作为decoder。encoder负责将输入序列压缩成指定长度的向量，这个向量就可以看成是这个序列的语义，这个过程称为编码，而decoder则负责根据语义向量生成指定的序列，这个过程也称为解码。

## 循环神经网络

RNNs的目的是用来处理序列数据，不只是在NLP领域取得巨大成功，在其他领域也有一席之地，比如图像描述生成和路径判断等也有不错的效果。

- Simple RNN

    也就是基础的RNN网络，也被称为Vanilla RNN

- Bidirectional RNN

    双向的RNN，上下叠加在一起组成的，相当于前后的序列信息加在一起。

- Deep (Bidirectional) RNN

    顾名思义，更深层的结构，为了更强的表达能力

- Echo State Network

    回声状态网络，主要的特点就是它有一个“存储库”，使用大规模随机连接的循环网络取代经典神经网络中的中间层，一般称为reservoir，来存储相关的信息和保存时间特性。

- LSTM (Long Short-Term Memory)

    简单来说增加了一个传输状态，用于记录长期的信息，内部有忘记、选择记忆、和输出三个阶段。

- Gated Recurrent Unit

    GRU是对LSTM的一个简化，只使用了一个更新门。

- BiLSTM(Bi-directional LSTM)

    双向LSTM

## 注意力机制

Attention机制最早提出是在视觉领域，2014年Google Mind发表了《Recurrent Models of Visual Attention》，采用了RNN模型，并加入了Attention机制来进行图像的分类。

2015年，Bahdanau等人在论文《Neural Machine Translation by Jointly Learning to Align and Translate》中，将attention机制首次应用在nlp领域，其采用Seq2Seq+Attention模型来进行机器翻译。

2017年，Google 机器翻译团队发表的《Attention is All You Need》成了划时代的论文，完全抛弃了RNN和CNN等网络结构，而仅仅采用Attention机制来进行机器翻译任务，取得了很好的效果，注意力机制也成了研究热点。

- 抽象理解

    从注意力三个字就可以抽象理解到，输入信息有很多特征，学习的时候不仅是特征提出，也要做重要程度的标记，或者说特征权重，这就是注意力机制。

- Seq2Seq Attention

    decoder中的信息定义为Query，encoder中的信息定义为Key，Query和Key通过多种计算方式得到权重值，生成一个注意力的context，再和之前的context结合，最后decoder输出结果。

    笼统来说就是decoder每预测一个词，都拿着decoder现在的信息去和encoder所有信息做注意力的计算。基本上所有的attention都采用了这个原理，只不过算权重的函数形式可能会有所不同，但想法相同。

- self-attention

    自注意力的思想很简单，建立两两之间的注意力权重，作用时与特征信息线性加权。

- Multi-head attention

    多头注意力与自注意力的核心原理差不多，由多个自注意力组成，相当于从多个角度关注。

- Transformer

    Transformer也是由 Encoder-Decoder 结构组成，但不是RNN中的Encoder-Decoder，简单说就是直接对词向量使用注意力。(Query, Key, Value)

    参考：[The Illustrated Transformer]([https://link](http://jalammar.github.io/illustrated-transformer/))
