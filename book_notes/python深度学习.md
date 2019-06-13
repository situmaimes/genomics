[TOC]

# 书评
今天是双十一过后第13天，我读完了《Python深度学习》。
这本书长这个样子
![20181124-QQ截图20181124223634](http://cdn.simonyang.club/20181124-QQ截图20181124223634.jpg)
我读的很快，里面的Keras代码几乎没怎么实现，就试了几次，数据下不下来，applications里面的训练好的卷积层也下载不下来，就放弃了，主要重在理解整个神经网络的搭建过程。

其实我也算读了几本python机器学习的书了，也自己动手改过、写过神经网络，但是真的，这本书，读完这本书，我才算敢说我入门了。这本书由浅入深，有基础也有前沿发展的知识，而且把我之前零碎的知识点都串通起来了，如果你想真的入门，推荐这本，不过新手看起来依旧会有很多困难。建议先读《图解机器学习》，下面有说。

> 下面是不正经的吐槽时间

这本书是双十一图灵电子书满100减50的时候买的，是新出的书，第一次看的时候，就觉得是keras之父写的，挺屌的，可以买来看看，当时买了四本就这一本跟python有关，跟深度学习有关。

最近一次接触深度学习是在nibs做蛋白互作的预测，用的chainer，不过也是改的别人的代码，看了很久也不知所以然。主要是当时他自己实现了一个反向传播，然后顿时就傻眼了，真的看不懂。当时师兄想让我用一个库叫deepchem，文档不详细，看了半天也看不懂，最后也没用上。感觉又一次栽深度学习坑里了。其实当时对于深度学习已经免疫了，每次都是抄抄github上的代码，看了几百遍反向传播算法也搞不懂，卷积层池化层都是什么乱七八糟的操作，每次改代码都要在shape上面纠结半天，好不容易成功准确率也低的要命。总的来说，就是别人都说好，我就觉得是玄学。

暑假的时候靠着图灵的银子，换了一本《图解机器学习》，还是对深度学习持有执念，就是想搞懂。看的时候，感觉挺好的，解释的也挺清楚，不过里面有很多公式，看着很烦，很久没学数学，最讨厌看公式了，也没细看，只是理解概念。那时候才第一次了解自编码器是在干什么，也不知道拿什么实现。那本书我两天就看完了，里面讲的东西非常非常基础，只是在讲最简单的神经网络的概念和实现，也没有代码实现，都是公式。

刚看的这本，里面讲的是keras，keras是深度学习入门首选，小白都能用，而且官方有中文文档，值得推荐。这本书前面讲了密集连接，以及代码实现，没有公式，告诉我们只要把这些shape写对，就肯定能跑起来，而且还教了如何调网络，对于特定的问题如何选用合适的loss和评判标准，真正的让人能拿到一个新问题的时候，直接往里套，代码就能跑起来。当然，作者显然不止于此，在讲完Dense之后，对常用的二维卷积也进行了讲解，主要也是在讲他的shape是如何变化的，卷积层的优点是什么，他为什么有这样的优点，因为这样的优点他才适用于什么问题。再之后，大招来了，因为我之间做过两次中文文本相关的机器学习，所以对词向量挺熟悉，看了很多资料，但是懂的话，不是真懂，作者也用非常浅显的话语，讲出了Embedding的作用，让我明白了为什么他的shape是(maxlen,maxfeature)。作者也讲了LSTM的含义，但是没有展开，LSTM这个东西对于序列来说经常用，但是我每次试图去理解都理解不了，读完这本书我也算能用了。作者当然还有更高的目标，他又讲了Conv1D，讲了SeparableConv2D，讲了最新的VAE和GAN，因为是新出的书，所以对前沿方向的把握也是非常准确的。

总的来说，这本书贵在，回避了神经网络的复杂数学基底，而从空间映射的形象角度上讲了每一种神经网络的特殊之处。对于很多常见的深度学习用来解决的问题，也总结性的告诉我们每一种问题，可以用什么神经网络，用什么神经网络可以解决的更好，里面的参数应该怎么选，怎样对一个神经网络进行超参调节就算对数学一窍不通，也能根据这本书的指引，解决一个简单的问题。

我真的爱死这本书了。


# 深度学习用于文本和序列
处理文本、时间序列和一般的序列数据
循环神经网络和一维卷积神经网络
应用
1. 文本分类
2. 时间序列对比
3. seq2seq
4. 情感分析
5. 时间序列预测

## 预处理文本
文本向量化
1. 将文本分割为单词，并将每个单词转换为一个向量。
2. 将文本分割为字符，并将每个字符转换为一个向量。
3. 提取单词或字符的 n-gram，并将每个 n-gram 转换为一个向量。n-gram 是多个连续单词
或字符的集合（n-gram 之间可重叠）。
将文本分解而成的单元（单词、字符或 n-gram）叫作标记（token），将文本分解成标记的
过程叫作分词（tokenization）。


在使用轻量级的浅层文本处理模型时（比
如 logistic 回归和随机森林），n-gram 是一种功能强大、不可或缺的特征工程工具。

### one hot
```python
from keras.preprocessing.text import Tokenizer
samples = ['The cat sat on the mat.', 'The dog ate my homework.']
tokenizer = Tokenizer(num_words=1000)
tokenizer.fit_on_texts(samples)
sequences = tokenizer.texts_to_sequences(samples)
one_hot_results = tokenizer.texts_to_matrix(samples, mode='binary')
word_index = tokenizer.word_index
print('Found %s unique tokens.' % len(word_index))
```
### 词嵌入
1. 利用 Embedding 层学习词嵌入
```python
from keras.layers import Embedding
embedding_layer = Embedding(1000, 64)
```
单词索引 Embedding层 对应的词向量
 (samples, sequence_length) Emedding层  (samples, sequence_length, embedding_
dimensionality) 

IMDB实例
p154 错了
```python
from keras.datasets import imdb
from keras import preprocessing
max_features = 10000
maxlen = 20
(x_train, y_train), (x_test, y_test) = imdb.load_data(num_words=max_features)
x_train = preprocessing.sequence.pad_sequences(x_train, maxlen=maxlen)
x_test = preprocessing.sequence.pad_sequences(x_test, maxlen=maxlen)
from keras.models import Sequential
from keras.layers import Flatten, Dense, Embedding
model = Sequential()
model.add(Embedding(10000, 8, input_length=maxlen))
model.add(Flatten())
model.add(Dense(1, activation='sigmoid'))
model.compile(optimizer='rmsprop', loss='binary_crossentropy', metrics=['acc'])
model.summary()
history = model.fit(x_train, y_train,
        epochs=10,
        batch_size=32,
        validation_split=0.2)
```
2. 使用预训练的词嵌入
word2vec算法和 GloVe

索引 0 不应该代表任何
单词或标记，它只是一个占位符。
```python
embedding_dim = 100
embedding_matrix = np.zeros((max_words, embedding_dim))
for word, i in word_index.items():
    if i < max_words:
        embedding_vector = embeddings_index.get(word)
        if embedding_vector is not None:
            embedding_matrix[i] = embedding_vector
from keras.models import Sequential
from keras.layers import Embedding, Flatten, Dense
model = Sequential()
model.add(Embedding(max_words, embedding_dim, input_length=maxlen))
model.add(Flatten())
model.add(Dense(32, activation='relu'))
model.add(Dense(1, activation='sigmoid'))
model.summary()
model.layers[0].set_weights([embedding_matrix])
model.layers[0].trainable = False
model.compile(optimizer='rmsprop',
loss='binary_crossentropy',
metrics=['acc'])
history = model.fit(x_train, y_train,
epochs=10,
batch_size=32,
validation_data=(x_val, y_val))
model.save_weights('pre_trained_glove_model.h5')
```
1. 将原始文本转换为神经网络能够处理的格式。
2. 使用 Keras 模型的 Embedding 层来学习针对特定任务的标记嵌入。
3. 使用预训练词嵌入在小型自然语言处理问题上获得额外的性能提升。

## 循环神经网络
密集连接网络和卷积神经网络都是前馈网络
循环神经网络（RNN，recurrent neural network）处理序列的方式是，遍历所有序列元素，并保存一个状态（state），其中包含与已查看内容相关的信息。实际上，RNN 是一类具有内部环的神经网络。在处理两个不同的独立序列（比如两条不同的 IMDB 评论）之间，RNN 状态会被重置，因此，你仍可以将一个序列看作单个数据点，即网络的单个输入。真正改变的是，数据点不再是在单个步骤中进行处理，相反，网络内部会对序列元素进行遍历。
### SimpleRNN
```python
#RNN伪代码
state_t = 0
for input_t in input_sequence:
    output_t = activation(dot(W, input_t) + dot(U, state_t) + b)
    state_t = output_t
```
```python
import numpy as np
timesteps = 100
input_features = 32
output_features = 64
inputs = np.random.random((timesteps, input_features))
state_t = np.zeros((output_features,))
W = np.random.random((output_features, input_features))
U = np.random.random((output_features, output_features))
b = np.random.random((output_features,))
successive_outputs = []
for input_t in inputs:
    output_t = np.tanh(np.dot(W, input_t) + np.dot(U, state_t) + b)
    successive_outputs.append(output_t)
    state_t = output_t
final_output_sequence = np.stack(successive_outputs, axis=0)
```
```python
from keras.datasets import imdb
from keras.preprocessing import sequence
max_features = 10000
maxlen = 500
batch_size = 32
(input_train, y_train), (input_test, y_test) = imdb.load_data(num_words=max_features)
input_train = sequence.pad_sequences(input_train, maxlen=maxlen)
input_test = sequence.pad_sequences(input_test, maxlen=maxlen)
from keras.layers import Dense
model = Sequential()
model.add(Embedding(max_features, 32))
model.add(SimpleRNN(32))
model.add(Dense(1, activation='sigmoid'))
model.compile(optimizer='rmsprop', loss='binary_crossentropy', metrics=['acc'])
history = model.fit(input_train, y_train,
epochs=10,
batch_size=128,
validation_split=0.2)
```
输入只考虑了前 500 个单词，而不是整个序列，因此，RNN 获得的信息比前面的基准模型更少。
另一部分原因在于， SimpleRNN 不擅长处理长序列，比如文本。
### LSTM
LSTM 层是 SimpleRNN 层的一种变体，它增加了一种携带信息跨越多个时间步的方法。假
设有一条传送带，其运行方向平行于你所处理的序列。序列中的信息可以在任意位置跳上传送带，
然后被传送到更晚的时间步，并在需要时原封不动地跳回来。这实际上就是 LSTM 的原理：它
保存信息以便后面使用，从而防止较早期的信号在处理过程中逐渐消失。
```python
#LSTM伪代码
output_t = activation(dot(state_t, Uo) + dot(input_t, Wo) + dot(C_t, Vo) + bo)
i_t = activation(dot(state_t, Ui) + dot(input_t, Wi) + bi)
f_t = activation(dot(state_t, Uf) + dot(input_t, Wf) + bf)
k_t = activation(dot(state_t, Uk) + dot(input_t, Wk) + bk)
c_t+1 = i_t * k_t + c_t * f_t
```
![20181118-QQ截图20181118161710](http://cdn.simonyang.club/20181118-QQ截图20181118161710.jpg)
```python
from keras.layers import LSTM
model = Sequential()
model.add(Embedding(max_features, 32))
model.add(LSTM(32))
model.add(Dense(1, activation='sigmoid'))
model.compile(optimizer='rmsprop',
    loss='binary_crossentropy',
    metrics=['acc'])
history = model.fit(input_train, y_train,
    epochs=10,
    batch_size=128,
    validation_split=0.2)
```
LST适用于评论分析全局的长期性结构
1. 循环神经网络（RNN）的概念及其工作原理。
2. 长短期记忆（LSTM）是什么，为什么它在长序列上的效果要好于普通 RNN。
3. 如何使用 Keras 的 RNN 层来处理序列数据。

## 高级用法
1. 循环 dropout（recurrent dropout）。这是一种特殊的内置方法，在循环层中使用dropout来降低过拟合。
1. 堆叠循环层（stacking recurrent layers）。这会提高网络的表示能力（代价是更高的计算负荷）。
1. 双向循环层（bidirectional recurrent layer）。将相同的信息以不同的方式呈现给循环网络，可以提高精度并缓解遗忘问题。


时间序列
1. 预处理标准化
2. 编写一个python生成器即时生成样本

在尝试机器学习方法之前，建立一个基于常识的基准方法是很有用的；同样，在开始研究复杂且计算代价很高的模型（比如 RNN）之前，尝试使用简单且计算代价低的机器学习模型也是很有用的，比如小型的密集连接网络。

对每个时间步应该使用相同的 dropout 掩码（dropoutmask，相同模式的舍弃单元），而不是让 dropout 掩码随着时间步的增加而随机变化。此外，为了对 GRU 、 LSTM 等循环层得到的表示做正则化，应该将不随时间变化的 dropout掩码应用于层的内部循环激活（叫作循环 dropout 掩码）。对每个时间步使用相同的 dropout 掩码，可以让网络沿着时间正确地传播其学习误差，而随时间随机变化的 dropout 掩码则会破坏这个误差信号，并且不利于学习过程。
```python
from keras.models import Sequential
from keras import layers
from keras.optimizers import RMSprop
model = Sequential()
model.add(layers.GRU(32,
        dropout=0.2,
        recurrent_dropout=0.2,
        input_shape=(None, float_data.shape[-1])))
model.add(layers.Dense(1))
model.compile(optimizer=RMSprop(), loss='mae')
history = model.fit_generator(train_gen,
        steps_per_epoch=500,
        epochs=40,
        validation_data=val_gen,
        validation_steps=val_steps)
```
循环层堆叠
```python
from keras.models import Sequential
from keras import layers
from keras.optimizers import RMSprop
model = Sequential()
model.add(layers.GRU(32,
        dropout=0.1,
        recurrent_dropout=0.5,
        return_sequences=True,
        input_shape=(None, float_data.shape[-1])))
model.add(layers.GRU(64, activation='relu',
        dropout=0.1,
        recurrent_dropout=0.5))
model.add(layers.Dense(1))
model.compile(optimizer=RMSprop(), loss='mae')
history = model.fit_generator(train_gen,
        steps_per_epoch=500,
        epochs=40,
        validation_data=val_gen,
        validation_steps=val_steps)
```
性能提升不明显
使用双向RNN 常用于自然语言处理
RNN 特别依赖于顺序或时间，RNN 按顺序处理输入序列的时间步
双向 RNN 利用了 RNN 的顺序敏感性：它包含两个普通 RNN，比如你已经学过的 GRU 层和 LSTM 层，每个 RN 分别沿一个方向对输入序列进行处理（时间正序和时间逆序），然后将它们的表示合并在一起。通过沿这两个方向处理序列，双向
RNN 能够捕捉到可能被单向 RNN 忽略的模式。

如果逆序和正序一样好或者更好，值得用双向RNN
```python
model = Sequential()
model.add(layers.Embedding(max_features, 32))
model.add(layers.Bidirectional(layers.LSTM(32)))
model.add(layers.Dense(1, activation='sigmoid'))
model.compile(optimizer='rmsprop', loss='binary_crossentropy', metrics=['acc'])
history = model.fit(x_train, y_train,
        epochs=10,
        batch_size=128,
        validation_split=0.2)
```

1. 我们在第 4 章学过，遇到新问题时，最好首先为你选择的指标建立一个基于常识的基准。如果没有需要打败的基准，那么就无法分辨是否取得了真正的进步。
1. 在尝试计算代价较高的模型之前，先尝试一些简单的模型，以此证明增加计算代价是有意义的。有时简单模型就是你的最佳选择。
1. 如果时间顺序对数据很重要，那么循环网络是一种很适合的方法，与那些先将时间数据展平的模型相比，其性能要更好。
1. 想要在循环网络中使用 dropout，你应该使用一个不随时间变化的 dropout 掩码与循环dropout 掩码。这二者都内置于 Keras 的循环层中，所以你只需要使用循环层的 dropout和 recurrent_dropout 参数即可。
1. 与单个 RNN 层相比，堆叠 RNN 的表示能力更加强大。但它的计算代价也更高，因此不一定总是需要。虽然它在机器翻译等复杂问题上很有效，但在较小、较简单的问题上可能不一定有用。
1. 双向 RNN 从两个方向查看一个序列，它对自然语言处理问题非常有用。但如果在序列数据中最近的数据比序列开头包含更多的信息，那么这种方法的效果就不明显。
自然语言处理两个概念 循环注意（recurrent attention）和序列掩码（sequence masking）。

## 用卷积神经网络处理序列
一维卷积神经网络
```python
from keras.models import Sequential
from keras import layers
from keras.optimizers import RMSprop
model = Sequential()
model.add(layers.Embedding(10000, 128, input_length=150))
model.add(layers.Conv1D(32, 7, activation='relu'))
model.add(layers.MaxPooling1D(5))
model.add(layers.Conv1D(32, 7, activation='relu'))
model.add(layers.GlobalMaxPooling1D())
model.add(layers.Dense(1))
model.summary()
model.compile(optimizer=RMSprop(lr=1e-4),
        loss='binary_crossentropy',
        metrics=['acc'])
history = model.fit(x_train, y_train,
        epochs=10,
        batch_size=128,
        validation_split=0.2)
```
```
_________________________________________________________________
Layer (type)                 Output Shape              Param #
=================================================================
embedding_1 (Embedding)      (None, 150, 128)          1280000
_________________________________________________________________
conv1d_1 (Conv1D)            (None, 144, 32)           28704
_________________________________________________________________
max_pooling1d_1 (MaxPooling1 (None, 28, 32)            0
_________________________________________________________________
conv1d_2 (Conv1D)            (None, 22, 32)            7200
_________________________________________________________________
global_max_pooling1d_1 (Glob (None, 32)                0
_________________________________________________________________
dense_1 (Dense)              (None, 1)                 33
=================================================================
Total params: 1,315,937
Trainable params: 1,315,937
Non-trainable params: 0
_________________________________________________________________
```
结合卷积神经网络的速度和轻量与 RNN 的顺序敏感性，一种方法是在 RNN 前面使用一维卷积神经网络作为预处理步骤。对于那些非常长，以至于 RNN 无法处理的序列（比如包含上千个时间步的序列），这种方法尤其有用
```python
from keras.models import Sequential
from keras import layers
from keras.optimizers import RMSprop
model = Sequential()
model.add(layers.Conv1D(32, 5, activation='relu',
        input_shape=(None, float_data.shape[-1])))
model.add(layers.MaxPooling1D(3))
model.add(layers.Conv1D(32, 5, activation='relu'))
model.add(layers.GRU(32, dropout=0.1, recurrent_dropout=0.5))
model.add(layers.Dense(1))
model.summary()
model.compile(optimizer=RMSprop(), loss='mae')
history = model.fit_generator(train_gen,
        steps_per_epoch=500,
        epochs=20,
        validation_data=val_gen,
        validation_steps=val_steps)
```
1. 二维卷积神经网络在二维空间中处理视觉模式时表现很好，与此相同，一维卷积神经网络在处理时间模式时表现也很好。对于某些问题，特别是自然语言处理任务，它可以替代 RNN，并且速度更快。
1. 通常情况下，一维卷积神经网络的架构与计算机视觉领域的二维卷积神经网络很相似，它将 Conv1D 层和 MaxPooling1D 层堆叠在一起，最后是一个全局池化运算或展平操作。
1. 因为 RNN 在处理非常长的序列时计算代价很大，但一维卷积神经网络的计算代价很小，所以在 RNN 之前使用一维卷积神经网络作为预处理步骤是一个好主意，这样可以使序列变短，并提取出有用的表示交给 RNN 来处理。


本章总结
1. 你在本章学到了以下技术，它们广泛应用于序列数据（从文本到时间序列）组成的数据集。
 如何对文本分词。
 什么是词嵌入，如何使用词嵌入。
 什么是循环网络，如何使用循环网络。
 如何堆叠 RNN 层和使用双向 RNN，以构建更加强大的序列处理模型。
 如何使用一维卷积神经网络来处理序列。
 如何结合一维卷积神经网络和 RNN 来处理长序列。
1. 你可以用 RNN 进行时间序列回归（“预测未来”）、时间序列分类、时间序列异常检测和序列标记（比如找出句子中的人名或日期）。
1. 同样，你可以将一维卷积神经网络用于机器翻译（序列到序列的卷积模型，比如SliceNet）、文档分类和拼写校正。
1. 如果序列数据的整体顺序很重要，那么最好使用循环网络来处理。时间序列通常都是这样，最近的数据可能比久远的数据包含更多的信息量。
1. 如果整体顺序没有意义，那么一维卷积神经网络可以实现同样好的效果，而且计算代价更小。文本数据通常都是这样，在句首发现关键词和在句尾发现关键词一样都很有意义。

# 高级的深度学习最佳实践

## Keras 函数式 API
Sequential 模型假设，网络只有一个输入和一个输出，而且网络是层的线性堆叠
类图模型：网络结构为有向无环图 Inception 系列网络  ResNet 系列网络（残差连接）
多输入模型、多输出模型和类图模型
```python
from keras.models import Sequential, Model
from keras import layers
from keras import Input
seq_model = Sequential()
seq_model.add(layers.Dense(32, activation='relu', input_shape=(64,)))
seq_model.add(layers.Dense(32, activation='relu'))
seq_model.add(layers.Dense(10, activation='softmax'))
input_tensor = Input(shape=(64,))
x = layers.Dense(32, activation='relu')(input_tensor)
x = layers.Dense(32, activation='relu')(x)
output_tensor = layers.Dense(10, activation='softmax')(x)
model = Model(input_tensor, output_tensor)
model.summary()
```
```python
model.compile(optimizer='rmsprop', loss='categorical_crossentropy')
import numpy as np
x_train = np.random.random((1000, 64))
y_train = np.random.random((1000, 10))
model.fit(x_train, y_train, epochs=10, batch_size=128)
score = model.evaluate(x_train, y_train)
```
双输入问答模型
```python
from keras.models import Model
from keras import layers
from keras import Input
text_vocabulary_size = 10000
question_vocabulary_size = 10000
answer_vocabulary_size = 500
text_input = Input(shape=(100,), dtype='int32', name='text')
embedded_text = layers.Embedding(
text_vocabulary_size, 64)(text_input)
encoded_text = layers.LSTM(32)(embedded_text)
question_input = Input(shape=(100,),
            dtype='int32',
            name='question')
embedded_question = layers.Embedding(
question_vocabulary_size, 32)(question_input)
encoded_question = layers.LSTM(16)(embedded_question)
concatenated = layers.concatenate([encoded_text, encoded_question],
            axis=-1)
answer = layers.Dense(answer_vocabulary_size,
            activation='softmax')(concatenated)
model = Model([text_input, question_input], answer)
model.compile(optimizer='rmsprop',
        loss='categorical_crossentropy',
        metrics=['acc'])
```
```
__________________________________________________________________________________________________
Layer (type)                    Output Shape         Param #     Connected to
==================================================================================================
text (InputLayer)               (None, 100)          0
__________________________________________________________________________________________________
question (InputLayer)           (None, 100)          0
__________________________________________________________________________________________________
embedding_3 (Embedding)         (None, 100, 64)      640000      text[0][0]
__________________________________________________________________________________________________
embedding_4 (Embedding)         (None, 100, 32)      320000      question[0][0]
__________________________________________________________________________________________________
lstm_3 (LSTM)                   (None, 32)           12416       embedding_3[0][0]
__________________________________________________________________________________________________
lstm_4 (LSTM)                   (None, 16)           3136        embedding_4[0][0]
__________________________________________________________________________________________________
concatenate_2 (Concatenate)     (None, 48)           0           lstm_3[0][0]
                                                                 lstm_4[0][0]
__________________________________________________________________________________________________
dense_2 (Dense)                 (None, 500)          24500       concatenate_2[0][0]
==================================================================================================
Total params: 1,000,052
Trainable params: 1,000,052
Non-trainable params: 0
__________________________________________________________________________________________________
```
向模型输入一个由Numpy 数组组成的列表，或者也可以输入一个将输入名称映射为 Numpy 数组的字典
```python
import numpy as np
num_samples = 1000
max_length = 100
text = np.random.randint(1, text_vocabulary_size,
                size=(num_samples, max_length))
question = np.random.randint(1, question_vocabulary_size,
                size=(num_samples, max_length))
answers = np.random.randint(answer_vocabulary_size, size=(num_samples))
answers = keras.utils.to_categorical(answers, answer_vocabulary_size)
model.fit([text, question], answers, epochs=10, batch_size=128)
model.fit({'text': text, 'question': question}, answers,
                epochs=10, batch_size=128)
```
多输出模型
```python
model = Model(posts_input,
[age_prediction, income_prediction, gender_prediction])
model.compile(optimizer='rmsprop',
        loss=['mse', 'categorical_crossentropy', 'binary_crossentropy'])
model.compile(optimizer='rmsprop',
        loss={'age': 'mse',
        'income': 'categorical_crossentropy',
        'gender': 'binary_crossentropy'})
```
损失值相加得到一个全局损失
严重不平衡的损失贡献会导致模型表示针对单个损失值最大的任务优先进行优化，而不考虑其他任务的优化
分配权重
```python
model.compile(optimizer='rmsprop',
            loss=['mse', 'categorical_crossentropy', 'binary_crossentropy'],
            loss_weights=[0.25, 1., 10.])
model.compile(optimizer='rmsprop',
            loss={'age': 'mse',
            'income': 'categorical_crossentropy',
            'gender': 'binary_crossentropy'},
            loss_weights={'age': 0.25,
                'income': 1.,
                'gender': 10.})
```
Keras 中的神经网络可以是层组成的任意有向无环图
1×1 卷积［也叫作逐点卷积（pointwise convolution）］是 Inception 模块的特色，它有助于区分开通道特征学习和空间特征学习。如果你假设每个通道在跨越空间时是高度自相关的，但不同的通道之间可能并不高度相关，那么这种做法是很合理的。
向任何多于 10 层的模型中添加残差连接，都可能会有所帮助。
恒等残差连接 直接相加
线性残差连接 使用 1×1 卷积，将原始 x 张量线性下采样为与 y 具有相同的形状
深度学习中的表示瓶颈和梯度消失


构建具有共享分支的模型，即几个分支全都共享相同的知识并执行相同的运算。也就是说，这些分支共享相同的表示，并同时对不同的输入集合学习这些表示。

评估语义相似度问题
用一个 LSTM 层来处理两个句子。这个 LSTM 层的表示（即它的权重）是同时基于两个输入来学习的。我们将其称为连体 LSTM（Siamese LSTM）或共享LSTM（shared LSTM）模型。

像使用层一样使用模型
y = model(x)

1. 如果你需要实现的架构不仅仅是层的线性堆叠，那么不要局限于 Sequential API。
1. 如何使用 Keras 函数式 API 来构建多输入模型、多输出模型和具有复杂的内部网络拓扑
结构的模型。
1. 如何通过多次调用相同的层实例或模型实例，在不同的处理分支之间重复使用层或模型
的权重。

## 使用 Keras 回调函数和 TensorBoard 来检查并监控深度学习模型
当观测到验证损失不再改善时就停止训练，可以使用Keras的回调函数实现
用法：
1. 模型检查点（model checkpointing）：在训练过程中的不同时间点保存模型的当前权重。
1. 提前终止（early stopping）：如果验证损失不再改善，则中断训练（当然，同时保存在训
练过程中得到的最佳模型）。
1. 在训练过程中动态调节某些参数值：比如优化器的学习率。
1. 在训练过程中记录训练指标和验证指标，或将模型学到的表示可视化（这些表示也在不
断更新）：你熟悉的 Keras 进度条就是一个回调函数！
```
keras.callbacks.ModelCheckpoint
keras.callbacks.EarlyStopping
keras.callbacks.LearningRateScheduler
keras.callbacks.ReduceLROnPlateau
keras.callbacks.CSVLogger
```
```python
import keras
callbacks_list = [
keras.callbacks.EarlyStopping(
                monitor='acc',
                patience=1,
                ),
keras.callbacks.ModelCheckpoint(
                filepath='my_model.h5',
                monitor='val_loss',
                save_best_only=True,
                )
]
model.compile(optimizer='rmsprop',
                loss='binary_crossentropy',
                metrics=['acc'])
model.fit(x, y,
                epochs=10,
                batch_size=32,
                callbacks=callbacks_list,
                validation_data=(x_val, y_val))
```
```python
callbacks_list = [
keras.callbacks.ReduceLROnPlateau(
                monitor='val_loss'
                factor=0.1,
                patience=10,
                )
]
model.fit(x, y,
                epochs=10,
                batch_size=32,
                callbacks=callbacks_list,
                validation_data=(x_val, y_val))
```

TensorBoard 具有下列巧妙的功能，都在浏览器中实现。
1. 在训练过程中以可视化的方式监控指标
1. 将模型架构可视化
1. 将激活和梯度的直方图可视化
1. 以三维的形式研究嵌入

1. Keras 回调函数提供了一种简单方法，可以在训练过程中监控模型并根据模型状态自动
采取行动。
1. 使用 TensorFlow 时，TensorBoard 是一种在浏览器中将模型活动可视化的好方法。在Keras 模型中你可以通过 TensorBoard 回调函数来使用这种方法。

高级架构模式
1. 残差连接 
2. 批标准化 网络每次变化后的数据标准化
训练过程中在内部保存已读取每批数据均值和方差的指数移动平均值
有助于梯度传播
BatchNormalization 层接收一个 axis 参数，它指定应该对哪个特征轴做标准化。这
个参数的默认值是 -1，即输入张量的最后一个轴。

批再标准化
自标准化神经网络，使用特殊的激活函数（ selu ）和特殊的初始化器（ lecun_normal ），能够让数据通过任何 Dense 层之后保持数据标准化。
3. 深度可分离卷积 SeparableConv2D
对输入的每个通道分别执行空间卷积，然后通过逐点卷积（1×1 卷积）将输出通道混合，
超参数优化
超参数优化的过程通常如下所示。
(1) 选择一组超参数（自动选择）。
(2) 构建相应的模型。
(3) 将模型在训练数据上拟合，并衡量其在验证数据上的最终性能。
(4) 选择要尝试的下一组超参数（自动选择）。
(5) 重复上述过程。
(6) 最后，衡量模型在测试数据上的性能。
搜索技术：贝叶斯优化、遗传算法、简单随机搜索
Hyperopt用于超参数优化的 Python 库，其内部使用 Parzen 估计器的树来预测哪组超
参数可能会得到好的结果。另一个叫作 Hyperas 的库将 Hyperopt 与 Keras 模型集成在一起。
小心验证集过拟合
模型集成
集成依赖于这样的假设，即对于独立训练的不同良好模型，它们表现良好可能是因为不同
的原因
```
preds_a = model_a.predict(x_val)
preds_b = model_b.predict(x_val)
preds_c = model_c.predict(x_val)
preds_d = model_d.predict(x_val)
final_preds = 0.25 * (preds_a + preds_b + preds_c + preds_d)
```
只有这组分类器中每一个的性能差不多一样好时，这种方法才奏效
加权平均，使用随机搜索或简单的优化算法（比如 Nelder-Mead 方法）
想要保证集成方法有效，关键在于这组分类器的多样性。
因此，集成的模型应该尽可能好，同时尽可能不同。
实践中很有效将基于树的方法（比如随机森林或梯度提升树）和深度神经网络进行集成
一种在实践中非常成功的基本集成方法是宽且深（wide and deep）的模型类型，
它结合了深度学习与浅层学习。

# 生成式深度学习
## 使用 LSTM 生成文本
使用前面的标记作为输入，训练一个网络（通常是循环神经网络或卷积神经网络）来预测序列中接下来的一个或多个标记
给定前面的标记，能够对下一个标记的概率进行建模的任何网络都叫作语言模型。

贪婪采样 
随机采样
为了在采样过程中控制随机性的大小，我们引入一个叫作 softmax 温度的参数，用于表示采样概率分布的熵，









