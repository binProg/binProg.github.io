---
title: BERT学习笔记--李宏毅
date: 2021-12-15 20:46:32
tags: nlp Bert
mathjax: true
---

# BERT学习笔记(原理篇1)

> 1. 机器学习最早使用**1-of-N encoding**，把每个词汇表示成一个N维的向量，N维向量中只有1个1，其余全是0。造成维度爆炸，且各个词汇之间的上下文关系和语义关系都没有考虑进去。
> 2. 接着，有了**Word Class**的概念，比如 dog/bird/fish是一类，因为都是动物。但是只关注了类间单词的区别而忽略了类内单词的区别
> 3. 进阶的，有了**Word Embedding**的概念，用dense vector（向量）来表示一个词，意思相近的词其向量之间的距离较近。常见的Word Embedding方法有word2vec, GloVe等

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg-blog.csdnimg.cn%2F2021031620201211.png%3Fx-oss-process%3Dimage%2Fwatermark%2Ctype_ZmFuZ3poZW5naGVpdGk%2Cshadow_10%2Ctext_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDE3NzU5NA%3D%3D%2Csize_16%2Ccolor_FFFFFF%2Ct_70&refer=http%3A%2F%2Fimg-blog.csdnimg.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1642056957&t=8dcbc4be84686bc9ccd1e1ba051e10a0" width="500px" height="350px" />

## 一、Introduction 

ELMO: RNN-based language model， trained from lots of sentences

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg-blog.csdnimg.cn%2Fimg_convert%2Fb4e03affb8f391fca6cb35d5a9e164c6.png&refer=http%3A%2F%2Fimg-blog.csdnimg.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1642056079&t=f237adc41fa64029446bde333612455c" width ="500px" height="500px" />



BERT: Encoder of Transformer

## 二、Training of BERT

- Approach 1：MASKED LM
  - 相当于完型填空，即随机覆盖住15%的词，去预测覆盖住的词
- Approach 2： NEXT SENTENCE PREDICTION
  - 做句子之间的判断，判断两个句子是否存在上下文关系

![img](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg-blog.csdnimg.cn%2F20210223193921145.png%3Fx-oss-process%3Dimage%2Fwatermark%2Ctype_ZmFuZ3poZW5naGVpdGk%2Cshadow_10%2Ctext_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjgzODA2MQ%3D%3D%2Csize_16%2Ccolor_FFFFFF%2Ct_70&refer=http%3A%2F%2Fimg-blog.csdnimg.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1642055881&t=6df93deba3f9b048d2cdd6789939d97e)

## 三、How to use BERT aka:Bert的应用

### CASE 1:  决定一整个句子属于那个类别（句子的分类）

<img src="https://tse4-mm.cn.bing.net/th/id/OIP-C.oDJW0d8K-EIKnlBZ1S13TwHaFn?pid=ImgDet&rs=1" aligin="center">

> **[CLS]**：代表分类的特殊符号，放在开头是因为Bert是基于Transformer的，而Transformer基于self-attention，即**每个位置都会得到整个位置的句子信息**，所以不需要放在最后

### Case 2: 决定一个句子中每个单词所属的类别

<img src="https://tse1-mm.cn.bing.net/th/id/OIP-C.zzrwVkfLyNFk2tc1hroTLwHaFU?pid=ImgDet&rs=1">

> 如：决定一个句子中w1是位置词还是时间词还是其它词

### CASE 3: 根据Sentence1（前提）决定Sentence2（假设）是对还是错

<img src="https://img-blog.csdnimg.cn/20210306173935877.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlcm9zdW5seQ==,size_16,color_FFFFFF,t_70" width="500px">

### CASE 4: QA

<img src="https://tse1-mm.cn.bing.net/th/id/OIP-C.54m5zFtXFwz6b6PXf_s6CAHaFR?pid=ImgDet&rs=1" aligin="center">

Input：文章+问题   ===>  Output：答案在文章中起始位置 $A = \{d_s, \cdots, d_e\}$ 

​          <img src="https://tse4-mm.cn.bing.net/th/id/OIP-C.8uzrcyIwoOhxi0z68ozRxAHaFg?pid=ImgDet&rs=1" width="500">



<img src="https://tse3-mm.cn.bing.net/th/id/OIP-C.ej_jhIuiHIo2Vt8bGZ-FGQHaFK?pid=ImgDet&rs=1" width="500">

## 四、扩展-ERNIE

<img src="https://tse1-mm.cn.bing.net/th/id/OIP-C.y30CYJ41Pba7iwXAzXBV7AHaFh?pid=ImgDet&rs=1">

针对中文的MASK提出的想法：

- BERT中做MASK时，MASK的是中文中的一个字，通过上下文很容易predict出被MASK的词是什么。而且不符合中文的实际，中文语义是以***词*** 为单位，而英文语义是一个字或者word为单位。
- ERNIE则是专为中文设计的MASK方法，每次以中文的“词”为单位进行MASK和分类预测，而不再以单个的字为单位进行分类预测。

目前的变种实现有：

- 华为：ZEN
- 清华：ERNIE-T

## 参考链接

1. 《李宏毅Bert学习笔记 》<https://blog.csdn.net/herosunly/article/details/94720139>  