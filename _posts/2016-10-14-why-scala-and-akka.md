---
layout: post
title: Why Scala and Akka
---

Scala is a general purpose programming language that uses the same runtime engine as Java.

It also incorporates functional programming concepts, which emphasizes transformation of data, as a flow, without side effects. It͛s much easier, to develop a complex system, when program functions act like mathematical functions. That is, we expect the same output always for a given set of inputs.

Through its
* Checking of data types at compile time
* Language features for scalability
* And general features from its Java heritage

...Scala is a solid option for building fast and reliable data processing systems.

* Type-safe integrations with messaging providers

For example,

{% highlight scala %}
val a = event.convertTo[FacebookAccountLinkingEvent]

val sender = a.sender.id
val recipient = a.recipient.id
val status = a.accountLinking.status
val authCode = a.accountLinking.authorizationCode.get
{% endhighlight %}

* Type-safe small DSLs

For example, akka-http routes:

{% highlight scala %}
path("authorize") {
  get {
    parameters("redirect_uri", "account_linking_token") { (redirectURI, accountLinkingToken) =>
      ...
  }
}
{% endhighlight %}

For example, building response cards:

{% highlight scala %}
val payload = (
  loginCard
    usingApi api
    forSender sender
    withText "You need to authorize me"
    withButtonTitle "Connect"
    build()
  )
{% endhighlight %}

* Actors using the FSM (finite state machine) DSL to implement conversational state

For example,

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

  // events
  case class Greet(sender: String, user: User)
  case class Respond(sender: String, text: String)

  sealed trait State
  case object Starting extends State
  case object Qualifying extends State
  case object Buying extends State

}
{% endhighlight %}

* Performance and scalability
* Libraries to support large-scale system design

For example, dependency injection using Google Guice

{% highlight scala %}
class FacebookController @Inject()(config: Config,
                                   logger: LoggingAdapter,
                                   intentService: IntentService) {}
{% endhighlight %}

* Functional programming to simplify concurrent system design
