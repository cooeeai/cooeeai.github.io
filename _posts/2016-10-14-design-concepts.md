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

## Conversation Flow

### Requirements

* Perform tasks that require multiple exchanges, holding onto context from earlier exchanges
* Context switch - cancel a task, switch to a different topic, handle chitchat during a task flow
* Direct the user back to the point or end early
* Handle incomplete responses
* Use existing knowledge to shortcut the process where possible
* Help the user maintain locus of control

### High-level approaches

* If-then-else (decision tree) approach - quickly becomes difficult to scale to complex cases, especially handling tangential concerns
* Workflow style approach, with possible sub-flows
* Finite state machine
* A grammar or scripting approach
* Black-box approaches, e.g. neural net
* Ensemble approach

Cooee's first implementation has adopted a state-machine approach.

* It can encapsulate the current state of the conversation (and therefore succinctly define next steps), while also allowing jumps (context-switching) through catching unhandled events.
* An event-driven approach handles non-linear flows more easily than a workflow or decision-tree approach

### Case Study - [Microsoft Bot Framework](https://docs.botframework.com/)

The Microsoft Bot Framework (MBF) has the concept of a Dialog. A common type of dialog is the Waterfall Dialog.
It implements a sequence of steps. Steps are completed in turn, and all steps must be completed to finish the task
(dialog flow).

A step may consist of a sub-dialog. MBF supports commands to go back or to cancel a dialog.

To move to the next step, a valid user response must be given to the current question. A library of built-in Prompts
is used to validate responses, e.g. a prompt for numeric input, date-time input, or to confirm a previous response.

MBF implements something they call "Guided Dialog", meaning that the bot is generally driving (or guiding) the
conversation with the user.

Data captured along the way can be stored in multiple scopes:

* dialog (local)
* private conversation/session (private for the current user)
* conversation (visible to all user sessions), or
* user (global across all conversations)

### Case Study - [Abot](https://github.com/itsabot/abot)

Abot uses a finite state machine approach.

### Case Study - [ChatScript](https://github.com/bwilcox-1234/ChatScript/blob/master/PAPERS/Paper-%20ChatBots%20102.pdf)

ChatScript is similar to the If-then-else approach in that it is a collection of rules which match patterns against the input
and executes output code when the pattern matches. ChatScript using a domain-specific language (DSL) to make the process of
constructing rules easier to write and read.

Here is a simple example:

    ?: (you * love * me) I love you
        a: (you are just saying that) No, I mean it
            b: (no) You think I love someone else?

The `?:` is the rule type. `s:` rules only react to statements. `?:` rules only react to questions. `u:` rules react to
the union of both. ChatScript doesn't have to match all words of the input. Wildcards and regular expressions can be used.

ChatScript rejoinders occur visually immediately following the corresponding rule and get tested only on user input
immediately after that rule fired. You can have multiple levels of rejoinders. (Similar to the Microsoft waterfall dialog.)

ChatScript rules are organized by topic. (Sort of like a sub-flow.) In addition to the responders (`s:` `u:` `?:`) and
rejoinders (`a:` `b:` etc.), topics have a type of rule called a gambit (`t:`). It's something the chatbot can say when it
has control. Even if the system cannot find a direct response to your input, if your input suggests that we are talking about,
say baseball, then the chatbot can offer you a relevant gambit. Or the bot can initiate a topic and say a gambit.

ChatScript supports concepts. It comes with around 1400 predefined concepts, you can define your own, and includes
WordNet's ontology.

Case Study - [SuperScript](http://superscriptjs.com/) and [Introducing SuperScript](https://medium.com/@rob_ellis/superscript-ce40e9720bef#.b7ev8dgyi)

Takes a similar a path to ChatScript. An example script is:

    + *
    - What is your favorite color?

        + *1
        % what is your favorite color
        - <cap> is my favorite color, too!

1. The user says something, and this is matched by the `*` generic wildcard. The bot then replies with **What is your favorite color?**.
2. When the next input from the user comes into the system, we first check the history and see if the previous reply has more dialogue. This is noted by the `% What is your favorite color?` line.
3. We then pull the next trigger, in this case it is `*1*` meaning one word. The bot then replies with **<user input> is my favorite color, too!**.

Replies can execute a custom function where they have access to the entire message object, the user object and other resources like the fact databases. You can also pass parameters to the functions from captured input parts:

    + * weather in <name>?
    - ^getWeather(<cap1>)

We can pass extra metadata back to the client, using a specific function, `^addMessageProp(key,value)`. The reply object can have extra properties added on from any reply.

    + can you (text/sms) me the name of the place
    - {^hasNumber(false)} Can I have your phone number, please?
    - {^hasNumber(true)}  ^addMessageProp(medium,txt) The Canteen

In the above example, if the user asks **Can you text me the name of the place?**, the system will check if the system has the user’s phone number (using the ^hasNumber filter). If the user does not have a phone number, the reply will be **Can I have your phone number, please?**

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
