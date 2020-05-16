---
layout: post
title:  "Building chatbots - basics"
date:   2018-06-29 12:00:00 +0500
permalink: "/building-chatbot-basics/"
---

## Chatbots might be the future of user interfaces.

With the development of Deep Learning and NLP chatbots become more and more popular. The hype for chatbots is already high and it will be increasing for the next several years.

> By 2020, over 50% of medium to large enterprises will have deployed product chatbots
>
> -- <cite>Van Baker, research vice president at Gartner</cite>


With Google’s release of the Sequence to [Sequence Learning with Neural Networks](https://papers.nips.cc/paper/5346-sequence-to-sequence-learning-with-neural-networks.pdf) paper in 2014 and rapid development of open source tools such us Tensor Flow, chatbots become easier to build.

## So this week I made my own chatbot using Keras and Tensor Flow!

### First thing first — gathering the data

There are many different open-source datasets available. Probably the most popular is [Cornell Movie — Dialogs Corpus](https://www.cs.cornell.edu/~cristian/Cornell_Movie-Dialogs_Corpus.html) and I used this dataset too. But for example, a much bigger dataset based on Reddit comments can be found [here](https://www.reddit.com/r/datasets/comments/3bxlg7/i_have_every_publicly_available_reddit_comment/?st=j9udbxta&sh=69e4fee7).

![chatbot-dataset]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/chatbot_dataset.png)

### Preparing your data

One of the most challenging thing in NLP for me is data preparation. In order to be recognizable for the Neural Network words needs to be represented as vectors. There are different ways to do that, for example, Gensim, but I used Keras implementation of [TensorFlow’s embedding](https://www.tensorflow.org/tutorials/text/word_embeddings).

![example1]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example1.png)

I loaded data as a list of sentences.

![example2]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example2.png)

Then I extracted all unique words from the text and assign a unique integer number for each word.

![example3]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example3.png)

So, as a result, I’ve got a list of lists with encoded words, where each word represents a number. For example, vector `[42, 14, 35, 18]` represents the text: “They do not!” and `[1]` represents “Wow”

**So far so good!** Now we face a problem that every line of text (vector) has a different length, so Neural Network can’t be trained by this. A common solution is to replace each vector with a dummy matrix, filled with zeros, so instead of having vectors with different lengths, we will have matrixes with the same dimensionality, and then incorporate each vector into this matrixes.

### Compiling your model

For the model, I used Neural network with seq2seq architecture (see the picture from cover). This model contains two parts Encoder and Decoder each of them is a set of LSTM cells. Encoder and decoder can share weights or use a different set of parameters.

Define encoder:
```python
encoder_inputs = Input(shape=(None,), name='encoder_inputs')
encoder_embedding = Embedding(input_dim=num_encoder_tokens, output_dim=HIDDEN_UNITS,input_length=encoder_max_seq_length, name='encoder_embedding')

encoder_lstm = LSTM(units=HIDDEN_UNITS, return_state=True, name='encoder_lstm')
encoder_outputs, encoder_state_h, encoder_state_c = encoder_lstm(encoder_embedding(encoder_inputs))
encoder_states = [encoder_state_h, encoder_state_c]
```

Define decoder:
```python
decoder_inputs = Input(shape=(None, num_decoder_tokens), name='decoder_inputs')
decoder_lstm = LSTM(units=HIDDEN_UNITS, return_state=True, return_sequences=True, name='decoder_lstm')
decoder_outputs, decoder_state_h, decoder_state_c = decoder_lstm(decoder_inputs,                                        initial_state=encoder_states)
decoder_dense = Dense(units=num_decoder_tokens, activation='softmax', name='decoder_dense')
decoder_outputs = decoder_dense(decoder_outputs)
```

Compile my model:
```python
model = Model([encoder_inputs, decoder_inputs], decoder_outputs)

model.compile(loss='categorical_crossentropy', optimizer='rmsprop')
json = model.to_json()

open('model/word-architecture.json', 'w').write(json)

X_train, X_test, y_train, y_test = train_test_split(encoder_input_data, target_texts, test_size=0.2, random_state=42)

train_gen = generate_batch(X_train, y_train)
test_gen = generate_batch(X_test, y_test)

train_num_batches = len(X_train) // BATCH_SIZE
test_num_batches = len(X_test) // BATCH_SIZE

checkpoint = ModelCheckpoint(filepath=WEIGHT_FILE_PATH, save_best_only=True)
tbCallBack = TensorBoard(log_dir=TENSORBOARD, histogram_freq=0, write_graph=True, write_images=True)
```

![chatbot_model]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/chatbot_model.png)

###  Train my model

I used GPU 1080Ti but it still took a while for 100 epochs.
```python
model.fit_generator(generator=train_gen, 
                    steps_per_epoch=train_num_batches,
                    epochs=NUM_EPOCHS,
                    verbose=1, 
                    validation_data=test_gen, 
                    validation_steps=test_num_batches, 
                    callbacks=[checkpoint, tbCallBack ])
model.save_weights(WEIGHT_FILE_PATH)
```

![loss_function]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/loss_function.png)

### Testing my chatbot!

So the model is trained! I made a class wrapper for it, so it will be more convenient to use. Method `reply` taking a text as input, transform it into vector, in the same way, I did in the training step and then call `predict` on the trained model.

Let’s start with greetings.

![example4]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example4.png)

Well, I just made this bot, but it’s already depressed.

Let’s try the Turing Test!

![example5]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example5.png)

Shame, but looks like my bot already did not pass this test.

Let’s try asking the [Ultimate question](https://en.wikipedia.org/wiki/Phrases_from_The_Hitchhiker%27s_Guide_to_the_Galaxy#Answer_to_the_Ultimate_Question_of_Life,_the_Universe,_and_Everything_(42))

![example6]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example6.png)

Wow! Amazing!

But to be honest I hardcoded that one as an easter egg…

Finally, let’s ask some jibberish

![example7]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example7.png)

That's it! I think it confirms that my bot suffers from depression.

### Some more examples:

![example8]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example8.png)

![example9]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example9.png)

![example10]({{ site.url }}/{{ site.baseurl }}/assets/2018-07-27-building-chatbot-basics/example10.png)

## What can be done next?

Code for this chatbot can be found here: [https://github.com/subpath/ChatBot](https://github.com/subpath/ChatBot)

1. If you have free time and computational power you can train the model on a bigger dataset like the one from Reddit
2. You can wrap it in some service maybe web service based on a flask or telegram bot

I will try to improve the quality of this bot by tweaking of hyperparameters. And probably will make telegram bot out of it in future.

### References:

1. [Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation](https://arxiv.org/abs/1406.1078)
2. [Sequence to Sequence Learning with Neural Networks](https://arxiv.org/abs/1409.3215)
3. [Neural Machine Translation by Jointly Learning to Align and Translate](https://arxiv.org/abs/1409.0473)