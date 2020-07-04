---
layout: post
title: "Play Hangman using BERT"
excerpt: ""
categories: articles
tags: [deep learning]
comments: true
share: true
---

最近这段时间闲着没事，开始看一些NLP的东西，从2012年的word2vec开始，deep learning在NLP中的领域是越来越多，五花八门，自己关于nlp的知识库也好久没有更新了，于是决定写点代码，看看最近的这些模型都是什么样的。

# 机器翻译

当人们意识可以通过深度学习实现seq2seq的任务的时候，Neural machine translation很快成为了一个热门的领域。机器翻译的目标是，给定一个原始句（A语言），输出一个目标句（B语言）。人们很快想到，既然我们可以得到每个单词的向量表示，那么就可以通过一层/多层LSTM组成一个encoder，得到这个句子的向量表示，即一个双向的LSTM的输出，就是每个单词的hidden state的逐步迭代输出结果。得到原始句子的整体表征之后，用一个decoder，结合目标句的单词表征，输出目标句的下个单词。

# 机器翻译的问题

用DL做机器翻译得到了很大的成功， 很快人们发现这个网络结构有些不对的地方，比如说学习到的原始句的表征$$h_t$$，本质上是经过$$h_2= f(h_1), h_3 = f(h_2)...$$这样的迭代得到的，可以看到$$h_1$$显然被使用了很多次，而且为了让$$h_t$$去表示整个句子，意味着要把所有$$h_t$$之前的hidden state都压缩在$$h_t$$上面，这对$$h_t$$来说显然是不小的压力，毕竟$$h_t$$和之前所有的hidden state一样，拥有同样的维度，把所有的信息压缩在这个$$h_t$$未免有些困难。


# 注意力机制

为了解决上面说到的问题，注意力机制被开发出来了，本质上来说就是人们希望找到给之前的hidden state一个加权平均的方式，通过这样的方式，整个句子的信息就不再是被压缩在最后一个hidden state身上，而是由每个单词对应的hidden state加权组成。为什么把这样的方法叫做注意力机制呢，就是因为加权平均过程中的weight就是由目标单词的hidden state，与原始句子中单词的hidden state的dot product（dot product也可以被考虑成similarity）所以，这个网络想要翻译句子的时候，先看上一个翻译出的单词，并检查它与目标句子中单词的相似性，i.e. paying attention，得到目标句子的表征，通过这个表征进一步计算决定要翻译出的下一个单词


# 还需要RNN吗

有了注意力的想法之后，人们就把注意力机制归结为，Query, Key, Value的形式，这里不多赘述。人们意识到，RNN-like的网络结构训练起来实在有点慢，这样的网络结构注定没办法享受并行计算带来的好处，因为为了得到最后一个hidden state，你必须得把之前的hidden state都算出来。那为了训练更多的数据，不如把recurrent的网络结构删除，只留下一个注意力机制，注意力本质上就是矩阵乘法和softmax而已，这些操作可以非常方便地被并行化，放到GPU上运行，网络中的flop或许很多，但由于GPU的并行，这些操作几乎是免费的（之后这样的网络结构也就变成autoencoder pretraining, RNN类似的就变成Autoregressive pretraining）。 接下来就到了2017年的paper，“attention is all you need”，这篇文章展示了扔掉RNN，只保留注意力，一样可以得到很好的结果。具体细节上，文章用了一些LayerNorm, PositionEncoding, Warmup in optimizer这样的方法。

# Transfomer 

有了把RNN扔掉的想法之后，各类使用transformer的模型层出不穷，我理解的transformer，就是使用注意力的encoder。BERT，提出大家模型里面现在都在用encoder，encoder接decoder，decoder接loss，为什么我不把decoder那些删掉，自己做一个pretraining的encoder呢？只要训练出一个好的pretrain模型，之后人们把这个encoder接到他们自己定义的decoder上面，应该都能取得很好的效果，也就可以为人们省下很多encoding的时间。扔掉了decoder之后，该怎么做训练自己的encoder，并且保证这是一个好的encoder，可以让其他人接到自己的模型上面得到好结果呢？BERT提出了两种任务，masked prediction和next sentence prediction，第一个就是把句子里的一些单词给遮起来，让encoder去预测这个单词，这样的好处是相比起bi-LSTM，先从左到右，用单词左边的context，再从右到左使用单词右边的context，这样的做法相当于同时使用了左边和右边的context，第二个就是输入两个句子，预测这句子B是否是句子A 的下一句。通过这样的方法得到的encoder，可以很好的整合句子中的contextual信息。之后涌现了越来越多的pretrain模型，他们在模型规模上都变得无比巨大，像open ai的GPT-2， XLnet(autoregressive), RoBERTa，这里就不赘述。

# HangmanBot v1.0

回到我在家无所事事的时候，此时翻看BERT的mask prediction任务的我，突然想起今年三四月份的时候，曾有一家公司给我发来面试，面试题是让我写一个bot，玩hangman这个游戏，也就是猜单词里面的字母，最多猜六次，每次猜中则会把单词中每个猜中的字母显示出来。当时我曾有一闪而过用DL来解决这个问题的念头，但当时考虑到这家公司的背景，可能是希望我展示挖掘特征的能力，于是就结合n-gram，写了模式匹配的策略，总的来说就是匹配特定模式，根据频率预测出现字母的概率。现在仔细一想，这个游戏似乎和BERT的mask prediction差不多，既然闲来无事，不如用BERT的思路写个hangman player。我相信互联网上的其他地方早就有人有过这个想法，但我没有去搜索他们的solution，因为我想自己玩玩hangman这个游戏。

模型结构(结构来自于)：

1. input，被遮盖过后的单词，例如 hello, 被遮盖之后就是，#ell#
2. Embedding, 首先是字母的embedding，其次是位置的embedding
3. 四层Encoder, 其中每层包括
	1. self attention(masked)，被遮盖后单词中的字母对其他字母的注意力
	2. skip connection，ResNet中提出之后就很火的做法，总的来说就是一个short cut
	3. LayerNorm
	4. Fully Connected Layer, 
	5. skip connection
	6. LayerNorm
 4. output, 标准地接一个linear layer然后softmax，计算每个输入字母所对应的目标字母的概率
 5. Loss，使用CrossEntropy Loss

最后计算loss时，也是使用之前的mask，loss只包括那些被遮盖的字母的loss，所以模型收敛的速度会比较慢，因为这样做相当于浪费了那些已知字母对其他字母的注意力，毕竟他们没有被纳入到最后的loss计算当中。

我训练了30万个单词，对每个单词随机遮掉40%的字母，目前看训练的效果还可以，只是自己没有卡，无奈只能在CPU上跑跑，所以模型的size也没有拉多大，希望跑个一段时间能够得到好结果。

# 具体如何玩这个Hangman游戏呢？

可以想象，如果输入单词是全部被遮盖的，我们是无法使用模型得到任何输出的，因为没有注意力可以被计算，所以第一步，我们需要有一个好的initialization，自然而然地想到可以猜测元音字母，毕竟元音字母在大多数单词中都存在，只要猜中第一个，就可以根据元音字母的结构继续往下猜。

# HangmanBot v2.0

HangmanBot 1.0的缺点其实也很明显，首先Hangman的这个任务，并不是完全像BERT的目的那样，得到一个Language Model（即单词的condition在context上的distribution）。为了取胜Hangman这个游戏，需要在背景字母中，猜出一个最有可能出现的字母，所以我们可以在最后的output上加一层MaxPooling


1. input，被遮盖过后的单词，例如 hello, 被遮盖之后就是，#ell#
2. Embedding, 首先是字母的embedding，其次是位置的embedding
3. 四层Encoder, 其中每层包括
	1. self attention(masked)，被遮盖后单词中的字母对其他字母的注意力
	2. skip connection，ResNet中提出之后就很火的做法，总的来说就是一个short cut
	3. LayerNorm
	4. Fully Connected Layer, 
	5. skip connection
	6. LayerNorm
 4. output, 标准地接一个linear layer然后在字母的维度上做一层MaxPooling，再做softmax，计算最有可能出现字母的概率。
 5. Loss，使用KLDivergence Loss

比如说一个单词中有三个被遮住的字母，HangmanBot 1.0输出的是每个被遮住的位置对应的字母概率( shape, (3, 26))，而HangmanBot 2.0输出的是对这两个被遮住的位置，最有可能出现的字母的概率(shape, (1, 26))。



# HangmanBot v3.0

HangmanBot 2.0的缺点其实也很明显，因为在玩Hangman这个游戏的时候，被遮住的字母显然是不可能属于那些没有被遮住的字母。那么我们可以给最后的output加一层mask，也就是把那些已经见到过的字母给mask掉，这样模型也就不会输出那些字母的概率。


1. input，被遮盖过后的单词，例如 hello, 被遮盖之后就是，#ell#
2. Embedding, 首先是字母的embedding，其次是位置的embedding
3. 四层Encoder, 其中每层包括
	1. self attention(masked)，被遮盖后单词中的字母对其他字母的注意力
	2. skip connection，ResNet中提出之后就很火的做法，总的来说就是一个short cut
	3. LayerNorm
	4. Fully Connected Layer, 
	5. skip connection
	6. LayerNorm
 4. output, 标准地接一个linear layer然后在字母的维度上做一层MaxPooling，再加上已经见过字母的mask，再做softmax，计算最有可能出现字母的概率。
 5. Loss，使用KLDivergence Loss

# HangmanBot v4.0

我的模型实现到3.0也就止步了，因为胜率已经超出了我之前写的手工规则，但同样3.0也具有自己的缺点，也就是在玩hangman的过程中，难免会猜错几次，而这些猜错的字母在之前的模型中都没有给到模型预测上的帮助，而事实上，我们知道这些猜错的字母是不可能出现在被遮掉的部分的。

要利用这部分信息，模型需要一个decoder，把之前encoder的部分像BERT一样当作一个Language Model，即给出P(w1|context), 接着把猜错的字母作为“sequence”放进模型，即让模型学习P(w1|context, ~w2, ~w3,...)，而且既然猜错的字母并不具有顺序的性质，所以不需要positional encoding。

Decoder可以先做一层self-attention，再把encoder的信息作为key，value，猜错的字母作为query做attention，最后输出最有可能出现的字母的概率。

但这个模型需要更大的数据量，也就是需要模拟在玩hangman过程中的真实数据，这个数据量也会比原来大不少，但是可以想象这个模型还可以把hangman的胜率提升一个层次。可以看出HangmanBot 1.0其实就是3.0的pretrain。


项目地址：[https://github.com/ryanyuan42/transformer-hangman-bot](https://github.com/ryanyuan42/transformer-hangman-bot)


