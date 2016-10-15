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
