---
layout: post
title: Messaging Platform Integrations
---

## Facebook Messenger

Facebook Messenger uses webhooks (HTTP callbacks).

Each message is sent to your web server, which is registered when you setup the bot with Facebook.

Page Subscription.

![Page subscription](/public/page-subscription.png)

Set Access Token.

![Access token](/public/page-token.png)

## Webhooks

Webhooks are "user-defined HTTP call-backs"

Akka HTTP routes.

{% highlight scala %}
// define a webhook
path("authorize") {
  get {
    parameters("redirect_uri", "account_linking_token") {
      (redirectURI, accountLinkingToken) => ...
  }
}

path("skypewebhook") {
  post {
    logger.info("skypewebhook called")
    entity(as[JsObject]) { data =>
      logger.debug("received body:\n" + data.prettyPrint)
      val fields = data.fields
      ...
    }
  }
}
{% endhighlight %}

## Cisco Spark

One key difference between Spark Bots and regular users is that, in group rooms, bots only have access to messages in which they are mentioned. This means that `messages:created` webhooks only fire when the bot is mentioned in a room.

Also, `GET /messages` requires that you specify a special `?mentionedPeople=me` query parameter.

    GET /messages?mentionedPeople=me&roomId=SOME_INTERESTING_ROOM
    Authorization: Bearer THE_BOTS_ACCESS_TOKEN
