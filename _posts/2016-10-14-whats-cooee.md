---
layout: post
title: Introducing Cooee
---

The purpose of this project is to create a robust bot building framework for the JVM and enterprise use. It aims to support non-trivial use cases:
* Massive scalability
* Multi-platform conversations
* Complex transactions
* Deep personalisation
* Maintainable code base as bot capabilities grow
* Highly productive for commercial applications

Development for AI and bots requires refinement of design patterns and techniques, similar to how desktop and web application development had to evolve to create mobile apps. It includes system and UX design techniques to make use of natural language text and voice interfaces. It also involves combining rule-based and machine learning-based program control. I hope to support efforts to define these new design patterns and techniques.

Cooee includes Scala APIs for:
* Facebook Messenger
* Skype
* Google NLP
* Google Maps
* Wit.ai

And it currently integrates the following services:
* Address Service using [Google Geocoding API](https://developers.google.com/maps/documentation/geocoding/start)
* Alchemy Keywords Service from [IBM Bluemix](http://www.ibm.com/watson/developercloud/alchemy-language.html)
* [Facebook Messenger API](https://developers.facebook.com/docs/messenger-platform)
* [Humour Service](https://github.com/KiaFathi/tambalAPI)
* Intent Parsing Service from [Wit.ai](https://wit.ai/)
* [Google NLP API](https://cloud.google.com/natural-language/docs/)
* [Skype API](https://docs.botframework.com/en-us/skype/getting-started)
* Small-talk API from [Houndify](https://www.houndify.com/)
* [Wolfram Alpha Knowledge API](http://www.wolframalpha.com/widgets/)
* Simple Rules Service
