---
layout: post
title: Design Concepts
---

* Web / Mobile apps – minimise state
* Bot Apps – lots of state

General Requirements

* State
* Resiliency
* Security
* Maintainability as the app grows

## Building for scalability

### Typical web app design

![Thread pool](/public/thread-pool.png)

![Blocking requests](/public/blocking-requests.png)

### Reactive Asynchronous Design

Ideal for messaging apps.

![Asynchronous messaging](/public/asynchronous-messaging.png)

### The Actor Model

Non-blocking and resilient.

![The Actor Model](/public/actor-model.png)

Automatic failover.

![Automatic failover](/public/automatic-failover.png)

### Collaborating Actors

![Collaborating actors](/public/collaborating-actors.png)

## Interfaces

Messaging Provider.

{% highlight scala %}
trait MessagingProvider {
  def sendTextMessage(sender: String, text: String): Unit
  def sendLoginCard(sender: String, conversationId: String = ""): Unit
  def sendHeroCard(sender: String): Unit
  def sendReceiptCard(sender: String, slot: Slot): Unit
  def sendQuickReply(sender: String, text: String): Unit
  def getUserProfile(sender: String): Future[String]
}
{% endhighlight %}

Conversation.

{% highlight scala %}
trait Conversation {
  def converse(sender: String, message: Any): Unit
}
{% endhighlight %}

## Integrating with API Services

Call API in response to matching an Intent.

{% highlight scala %}
case Event(Qualify(platform, sender, productType, text), _) =>
  if (isAuthenticated) {
    productType match {
      case Some(typ) =>
        val message = s"What type of $typ did you have in mind?"
        history append Exchange(Some(text), message)
        provider.sendTextMessage(sender, message)
        goto(Qualifying) using Offer
      case None =>
        val message = "What did you want to buy?"
        history append Exchange(Some(text), message)
        provider.sendTextMessage(sender, message)
        stay
    }
  }
}
{% endhighlight %}

Providing enhanced UI responses.

{% highlight scala %}
// building a response card
val payload = (
  loginCard
    usingApi api
    forSender sender
    withText "You need to authorize me"
    withButtonTitle "Connect"
    build()
  )
{% endhighlight %}

## Implementing a Conversation Engine

Actors as Finite State Machines.

{% highlight scala %}
class ConversationActor extends Actor with FSM[State, Data] {

  startWith(Starting, Uninitialized)

  when(Qualifying) {

    case Event(Greet(sender, user), _) =>
      greet(sender, user)
      stay

    case Event(Respond(sender, text), _) =>
      provider.sendHeroCard(sender)
      goto(Buying)
  }
}

object ConversationActor extends NamedActor {
  // events  case class Greet(sender: String, user: User)
  case class Respond(sender: String, text: String)

  sealed trait State
  case object Starting extends State
  case object Qualifying extends State
  case object Buying extends State
}
{% endhighlight %}

Watson Intents.

![Watson intents](/public/watson-intents.png)

<small>IBM Watson is a registered trademark of International Business Machines Corporation.</small>

Watson Conversation Service.

![Watson Conversation](/public/watson-conversation.png)

Providing Watson Context.

![Watson context](/public/watson-context.png)

Watson needs help. It doesn't include the ability to parse time, measurements, etc. These entities must be identified and parsed before calling Watson. They can be provided to Watson as context in the request.

I'm using the excellent [Duckling](https://duckling.wit.ai/) library for parsing time, and the [Humanize](http://mfornos.github.io/humanize/) library to present time in natural language.

{% highlight scala %}
// parse text for time information
val parsed = parse.invoke(Symbol.intern("en$core"), text, Clojure.read("[:time]")).asInstanceOf[LazySeq]

for (x <- parsed.toArray) yield {
...val timeType = timeValue(CKeyword(null, "type")).asInstanceOf[String]
  timeType match {
    case "value" =>
      val value = timeValue(CKeyword(null, "value")).toString
      val grain = timeValue(CKeyword(null, "grain")).toString.substring(1)
      val timeCtx = new util.HashMap[String, String]()
      timeCtx.put("value", value)
      timeCtx.put("grain", grain)
      ctx.put("time", timeCtx)

    case "interval" => ...
  }}
val service = new WatsonConversationSvc("2016-07-11")
val partialMessage = new MessageRequest.Builder().inputText(text).context(ctx)
val message = partialMessage.build()
service.message(workspaceId, message).execute()
{% endhighlight %}
