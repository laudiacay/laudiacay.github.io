---
tags:
  - ilya30u30
  - ai
  - writing
  - papers
title: Understanding LSTM Networks
collection: posts
layout: post
---
### [Source post here.](https://colah.github.io/posts/2015-08-Understanding-LSTMs/)

This post is a kind and simple introduction to RNNs (Recursive Neural Nets) and the magical LSTM (a special kind of RNN, a Long Short Term Memory network). RNNs allow the neural net to have continuity of thought- they build up a context from intermediate states as they observe them in order, outputting information and editing the internal context based on each state they see, to allow contextual processing that streams forward in time. RNNs work by forwarding context from one iteration to the next as it steps through inputs, adds them into the context, and outputs outputs.

![rnn](/assets/images/rnn.png)

### RNNs have to do a hard task.

Hopefully, this allows the net to do things like answer the query: "The dog ate my spaghetti. Who ate my spaghetti?". By the time the RNN has finished parsing the entire query, it needs to have encoded into its context both that we're looking for the naughty spaghetti eater, and that that was an (adorable) dog. The dog was seven whole tokens, seven whole iterations of net, before we get to the end. So obviously, this needs to be safely stored somewhere until it's question-answering time. 

In a traditional RNN, that was usually just one tanh layer. And did that tanh layer ever have a ton of jobs to do! To simplify, let's assume that it's just trained on a lot of English prompts where there are one or two simple statements and then a simple question with the answer in the first two statements. 

To perform on this task, it has to encode English grammar and parts of speech, from token embeddings. It has to learn that the answers to questions are likely to be "interesting", so it chooses well what to remember, because it is compressing and combining its inputs into its internal state as it runs. It has to learn a simple model of relationships between objects in the world so that it doesn't think that the spaghetti ate itself! And it has to remember both dog and spaghetti and their relationship until output time, correctly disambiguate between them once it gets to the end of the question, and know when to forget irrelevant information. 

None of these are easy tasks, and it's probably quite a bit to train into a single tanh layer to both learn about all these relationships and all of this choosing where to focus its attention and how to load its small memory choosily, because it can't go back to re-scan the start of the sentence.
![rnn-tanh](/assets/images/rnn-tanh.png)

### They fail at this hard task. LSTMs succeed.

This intuition turns out to be correct. RNNs with this architecture are not good at learning long term dependencies. They forget things. The solution that the LSTM presents is basically giving them a better way to store and organize information rules as they train. You're hard-coding that the internal layers of the RNN need to do the following when they see each input token and recall the most recent output token: forget some information from the old state, add some other information to the old state, and output a token.

The relationships between these are below. The first sigmoid layer picks things to ablate from the memory based on x_t (new input token) and h_{t-1} (last output token). The next sigmoid layer distorts the things we're going to add to the context, by creating weights with which to scale what we're adding to the new context, based on the same input. The tanh layer encodes *what* we're adding into the context, again based on the same input. Then we multiply them and add that to the context. Finally, we tanh the context, take a sigmoid layer on the most recent updates (to decide how they impact the output), multiply them, and out comes our next output token. Then this output token and our context go back into the next iteration of the neural net.

![lstm](/assets/images/lstm.png)

Note that all of these functions are smoothly differentiable, which makes for nice backpropagation during training, and each little net inside the RNN now has a defined question to answer, so the original RNN monolayer doesn't have to learn how to learn anymore.
1. What new information does this new input token, and the presence of the last output token, bring me about the things I can forget about from the context if I want to perform well on my task? (sigmoid 1)
2. What does the last output and the new input tell me about what I need to add to the context to perform well? (tanh)
3. What does the last output and new input tell me about the changes from the old context to the new context? (sigmoid 2)
4. Finally, what do I output, based on the updated context, last output, and new input? (sigmoid 3)

Now it just has to learn to do these four tasks, which still includes all the english structure, but omits all the learning about learning and how information relates through time and sentence structure. And, unsurprisingly, with this issue fixed, LSTMs learn long-term dependencies much better than the Vanilla RNNs we started with.

### Insights for future developments (spoiler alert!)

There are a few interesting things I notice about these four questions. 

First: 1, 2, and 3 are not clearly defined as separate. If these three were roles in a startup, I'd expect them to fight a lot about boundaries of responsibility! The [GRU](https://arxiv.org/pdf/1406.1078v3), Gated Recurrent Unit, is a paper that also notices this, and combines these three layers into one "update" layer. This shrinks the number of connections, probably, so they're probably more efficient to train? I remember my professors saying that these had similar performance to LSTMs, and we used them in class for projects.

Second: this architecture is really making some hardline decisions about chopping up the information that layers have access to! And not everything has access to the cell state, which I think would be important for deciding what to forget- as a (wildly unsuited to this architecture) example, an AP History AI that's "studying" a textbook would be able to heavily discount any information that it detects as being inside a footnote, because that's unlikely to be on the test. [Peephole connections](https://ieeexplore.ieee.org/document/861302) are the solution to this- they patch the cell state back in as an input to the gate. Greatly increased number of weights here, but it's able to learn to count (and probably improve on some other tasks, as well).

Finally: I don't feel like I read straight through anything. I jump around until I feel that I have context, filling gaps and reinforcing concepts until I walk away with the answer to my question. One solution to this, unmentioned in this post, is a bidirectional LSTM (which I wrote in grad school). It parses backwards as well as forwards- building up state in both directions, and then putting the context from both directions into the final output tokens. From the image below, you can probably work out where the wires end up going. This helps somewhat with broadening context and allowing more flexibility in where the "blinders" are allowed to go when the LSTM is looking at inputs. Even more complex is the multi-layered LSTM, where you have multiple layers that take prior states as inputs, washing back and forth through inputs and outputs. These architectures are sort of ungodly, I had to implement this in grad school as well. They do work though.

![bidirectional lstm](/assets/images/bidirectional-lstm.png)

But- (Billy Mays voice)- there has to be a better way than "you can go forward, or you can go backward, and sometimes you can do both, but jumping around like a human does is out of the question", right? and it can't be mapping onto the human analogue of zipping your eyes around a 2D page, or zipping your brain around a knowledge graph of squishy mental associations, because that's a not only a bunch of unnecessary information for most LLM tasks, but also immensely computationally intractable.

The answer turns out to be something called attention, which lets the LLM take a holistic view of the entire sequence at each output, train on spotting important things from it, and think from there. This is something that we started by layering onto the LSTMs and RNNs, and then we let this tech drive on its own with no RNN in 2017 when we invented the transformer. Transformers are the architecture that brought you chatGPT and working machine learning. As it turns out, teaching the nets how to learn with LSTMs was pretty good, but letting them learn how to learn with attention layers was what brought them near human-level cognition. Read about that in another post from this series later on :)