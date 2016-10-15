---
layout: post
title: Bot Training
---

## Key Terms

<dl>
  <dt>Utterance</dt>
  <dd>The message text sent from a user. Sample utterances are used to train the natural language processing system.</dd>

  <dt>Intent</dt>
  <dd>An intent is the purpose or goal of a user's message. Natural language processing is used to extract intent from unstructured text. Intent may be used to route messages to more than one bot. The NLP engine is trained using sample utterances.</dd>

  <dt>Entity</dt>
  <dd>An entity is a term, which is mentioned in a user message, such as a product reference, time, quantity, or location, and that helps clarify the intent of the message. The NLP engine will attempt to extract entities along with the intent term/phrase.</dd>

  <dt>Conversation</dt>
  <dd>A Conversation is a defined flow of message exchanges, e.g. to enable a purchase – discover products, select product, confirm address, etc. Branching may occur at points in the flow, e.g. cross-sell, additional order details, etc.</dd>

  <dt>Action</dt>
  <dd>Actions may be performed at any step, e.g. to validate an address, or to call a Web Service API. The action may perform, for example, additional input processing, or interface with information retrieval and fulfilment systems.</dd>

  <dt>Slot</dt>
  <dd>An item of data required to perform an action. For example, the following slots may be required to place an order: ‘address’, and ‘card details’. Filled slots should be remembered. Unfilled slots will result in additional questions from the bot.</dd>

  <dt>Grammar</dt>
  <dd>The syntax of expected texts from a user. In some systems, the grammar must be defined explicitly. Whereas, in more advanced systems, the natural language processing engine can learn a grammar from sample utterances and existing documents.</dd>
</dl>

## Entity Extraction and Intent Parsing

![Intent parsing](/public/intent-parsing.png)

## Conversation Design

![Conversation design](/public/conversation-design.png)
