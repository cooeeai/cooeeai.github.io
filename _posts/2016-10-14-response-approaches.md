---
layout: post
title: Response Approaches
---

## Simple Rules-based Approach

{% highlight scala %}
(defineRule startsWith "what"
  and() contains "bot|virtual agent"
  and() contains "is"
  caseInsensitive()
  build(),
  """
    |A Bot, otherwise known as a chatbot, is an application that operates as a user in a messaging app. It can
    |receive and respond to messages using natural language.
  """.stripMargin
  )
{% endhighlight %}
<br>

## NLP-based Information Retrieval Approach

![NLP-based information retrieval](/public/nlp-information-retrieval.png)
<br>

## Deep Learning Approach

![Deep learning approach](/public/deep-learning-approach.png)

* Word embeddings are a geometric way to capture the “meaning” of a word via a low-dimensional vector.
* A property of embeddings is cosine similarity.
* Analogy relationships can be solved using linear algebra, e.g. man is-to woman, as king is to ___ ?

### Future State - Generative Approach

![Generative approach](/public/generative-approach.png)

For example.

> **Human:** connection refused or something like that

> **Machine:** may i know the version of network connect you connect?

> **Human:** i am not sure i know that

> **Machine:** is the network connect prompting for any user certificate?

> **Human:** yes

> **Machine:** are you at home or at work at the moment? office?

> **Human:** i am at home

> **Machine:** try this. goto <URL redacted> page and enter your name and password

> **Human:** done

> **Machine:** try logging in with <NAME redacted> and check if its working fine now

> **Human:** yes, now it works!

> **Machine:** great. anything else that i can help?

Taken from [Deep Learning model self-trained on chat logs between Google employees and Google’s Tech Support team](https://arxiv.org/pdf/1506.05869.pdf)

* The machine doesn’t actually understand anything about networking. It is pattern matching on sequence of words in the training responses that match patterns in the input.
* Does it matter? As long as issues can be solved without having to create pre-canned responses for every possible combination of user question.
* The acceptance threshold will be crossed when responses have a 99% accuracy in content and grammar.
* The acceptance threshold will be exceeded when responses can change in tone and personality to match the customer’s preferences and sentiment.
