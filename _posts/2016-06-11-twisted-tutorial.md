---
layout: post
title: Python Twisted Tutorial in depth
categories:
- blog
---


A Twisted protocol handles data in an asynchronous manner. The protocol responds to events as they arrive from the network and the events arrive as calls to methods on the protocol.
{% highlight python %}



from twisted.internet.protocol import Protocol

class Echo(Protocol):
    def dataReceived(self, data):
        self.transport.write(data)
{% endhighlight %}
 
  This is one of the simplest protocols. It simply writes back whatever is written to it, and does not respond to all events. Here is an example of a Protocol responding to another event:
 {% highlight python %}
from twisted.internet.protocol import Protocol

class QOTD(Protocol):

    def connectionMade(self):
        self.transport.write("An apple a day keeps the doctor away\r\n")
        self.transport.loseConnection()
{% endhighlight %}
  This protocol responds to the initial connection with a well known quote, and then terminates the connection.
  The connectionMade event is usually where setup of the connection object happens, as well as any initial greetings (as in the QOTD protocol above, which is actually based on RFC 865). The connectionLost event is where tearing down of any connection-specific objects is done. Here is an example:
  Here connectionMade and connectionLost cooperate to keep a count of the active protocols in a shared object, the factory. The factory must be passed to Echo.__init__ when creating a new instance. The factory is used to share state that exists beyond the lifetime of any given connection. You will see why this object is called a “factory” in the next section.

 {% highlight python %}
from twisted.internet.protocol import Protocol

class Echo(Protocol):

    def __init__(self, factory):
        self.factory = factory

    def connectionMade(self):
        self.factory.numProtocols = self.factory.numProtocols + 1
        self.transport.write(
            "Welcome! There are currently %d open connections.\n" %
            (self.factory.numProtocols,))

    def connectionLost(self, reason):
        self.factory.numProtocols = self.factory.numProtocols - 1

    def dataReceived(self, data):
        self.transport.write(data)
{% endhighlight %}
Using the protocol:
Here is code that will run the QOTD server discussed earlier:
In this example, I create a protocol Factory. I want to tell this factory that its job is to build QOTD protocol instances, so I set its buildProtocol method to return instances of the QOTD class. Then, I want to listen on a TCP port, so I make a TCP4ServerEndpoint to identify the port that I want to bind to, and then pass the factory I just created to its listen method.
endpoint.listen() tells the reactor to handle connections to the endpoint’s address using a particular protocol, but the reactor needs to be running in order for it to do anything. reactor.run() starts the reactor and then waits forever for connections to arrive on the port you’ve specified. You can stop the reactor by hitting Control-C in a terminal or calling reactor.stop().

 {% highlight python %}
from twisted.internet.protocol import Factory
from twisted.internet.endpoints import TCP4ServerEndpoint
from twisted.internet import reactor

class QOTDFactory(Factory):
  def buildProtocol(self, addr):
      return QOTD()

# 8007 is the port you want to run under. Choose something >1024
endpoint = TCP4ServerEndpoint(reactor, 8007)
endpoint.listen(QOTDFactory())
reactor.run()
{% endhighlight %}
Simpler: factory creation<br>
For a factory which simply instantiates instances of a specific protocol class, there is a simpler way to implement the factory.
The default implementation of the buildProtocol method calls the protocol attribute of the factory to create a Protocol instance,
and then sets an attribute on it called factory which points to the factory itself. This lets every Protocol access, and possibly modify, the persistent configuration. Here is an example that uses these features instead of overriding buildProtocol:
 {% highlight python %}
from twisted.internet.protocol import Factory, Protocol
from twisted.internet.endpoints import TCP4ServerEndpoint
from twisted.internet import reactor

class QOTD(Protocol):

    def connectionMade(self):
        # self.factory was set by the factory's default buildProtocol:
        self.transport.write(self.factory.quote + '\r\n')
        self.transport.loseConnection()


class QOTDFactory(Factory):

    # This will be used by the default buildProtocol to create new protocols:
    protocol = QOTD

    def __init__(self, quote=None):
        self.quote = quote or 'An apple a day keeps the doctor away'

endpoint = TCP4ServerEndpoint(reactor, 8007)
endpoint.listen(QOTDFactory("configurable quote"))
{% endhighlight %}
A Factory has two methods to perform application-specific building up and tearing down (since a Factory is frequently persisted,
it is often not appropriate to do them in __init__ or __del__, and would frequently be too early or too late).
Here is an example of a factory which allows its Protocols to write to a special log-file:
 {% highlight python %}
from twisted.internet.protocol import Factory
from twisted.protocols.basic import LineReceiver

class LoggingProtocol(LineReceiver):

    def lineReceived(self, line):
        self.factory.fp.write(line + '\n')

class LogfileFactory(Factory):

    protocol = LoggingProtocol

    def __init__(self, fileName):
        self.file = fileName

    def startFactory(self):
        self.fp = open(self.file, 'a')

    def stopFactory(self):
        self.fp.close()
{% endhighlight %}

Chat server using thing slearned above:
 {% highlight python %}
from twisted.internet.protocol import Factory
from twisted.protocols.basic import LineReceiver
from twisted.internet import reactor
from clint.textui import colored
import sys
import datetime

port = 8000
chatLog = []

class ChatProtocol(LineReceiver):
    def __init__(self, factory):
        self.factory = factory
        self.name = None
        self.state = "REGISTER"

    def getTime(self):
        return '({:%Y/%m/%d %H:%M:%S})'.format(datetime.datetime.now())

    def connectionMade(self):
        banner = ("""
        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        #### Successfully Connected to the Chat Server ####
        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        """)
        self.sendLine(banner)
        self.sendLine(self.getTime())
        self.sendLine("Choose a username:")

    def connectionLost(self, reason):
        leftMsg = colored.red('%s has left the channel.' % (self.name,))
        if self.name in self.factory.users:
            del self.factory.users[self.name]
            self.broadcastMessage(leftMsg)
        chatLog.append(self.name + " exits.")
        self.updateSessionInfo()

    def lineReceived(self, line):
        if self.state == "REGISTER":
            self.handle_REGISTER(line)
        else:
            self.handle_CHAT(line)

    def handle_REGISTER(self, name):
       if name in self.factory.users:
           self.sendLine("Sorry, %r is taken. Try something else." % name)
           return

       welcomeMsg = ('Welcome to the chat, %s.' % (name,))

       joinedMsg = colored.green('%s has joined the chanel.' % (name,))
       self.sendLine(welcomeMsg)
       self.broadcastMessage(joinedMsg)
       self.name = name
       self.factory.users[name] = self
       self.state = "CHAT"
       self.updateSessionInfo()

       if len(self.factory.users) > 1:
           self.sendLine('Participants in chat: %s ' % (", ".join(self.factory.users)))
       else:
           self.sendLine("You're the only one here, %r" % name)


    def handle_CHAT(self, message):
       message = self.getTime() + "<%s> %s" % (self.name, message)
       self.broadcastMessage(colored.magenta(message))
       chatLog.append(message)
       self.updateSessionInfo()

    def broadcastMessage(self, message):
       for name, protocol in self.factory.users.iteritems():
           if protocol != self:
               protocol.sendLine(colored.white(message))
               self.updateSessionInfo()

    def updateSessionInfo(self):
        print(chr(27) + "[2J")
        molding = "============================================"
        print(molding)
        print('Users in chat: %s ' % (", ".join(self.factory.users)))
        print(molding)
        global chatLog
        chatLog = chatLog[-20:]
        print("\n".join(chatLog))

class ChatFactory(Factory):
    def __init__(self):
        self.users = {}

    def buildProtocol(self, addr):
        return ChatProtocol(self)

reactor.listenTCP(port, ChatFactory())
print("Chat Server started on port %s" % (port,))
reactor.run()

{% endhighlight %}

UDP Networking:
Unlike TCP, UDP has no notion of connections. A UDP socket can receive datagrams from any server on the network and send datagrams to
any host on the network. In addition, datagrams may arrive in any order, never arrive at all, or be duplicated in transit.
Since there are no connections, we only use a single object, a protocol, for each UDP socket. We then use the reactor to connect this protocol to a UDP transport, using the twisted.internet.interfaces.IReactorUDP reactor API.

The class where you actually implement the protocol parsing and handling will usually be descended from
twisted.internet.protocol.DatagramProtocol or from one of its convenience children. The DatagramProtocol class receives datagrams and
can send them out over the network. Received datagrams include the address they were sent from. When sending datagrams the destination
address must be specified.


 {% highlight python %}
from __future__ import print_function

from twisted.internet.protocol import DatagramProtocol
from twisted.internet import reactor


class Echo(DatagramProtocol):

    def datagramReceived(self, data, addr):
        print("received %r from %s" % (data, addr))
        self.transport.write(data, addr)

reactor.listenUDP(9999, Echo())
reactor.run()
{% endhighlight %}

Twisted in built client :Agent<br>
A simple GET request
 {% highlight python %}
from __future__ import print_function

from twisted.internet import reactor
from twisted.web.client import Agent
from twisted.web.http_headers import Headers

agent = Agent(reactor)
d = agent.request(
    'GET',
    'http://example.com/',
    Headers({'User-Agent': ['Twisted Web Client Example']}),
    None)

def cbResponse(ignored):
    print('Response received')
d.addCallback(cbResponse)

def cbShutdown(ignored):
    reactor.stop()
d.addBoth(cbShutdown)

reactor.run()
{% endhighlight %}

Simple POST Request using Producers:
 {% highlight python %}
from zope.interface import implementer
from twisted.internet.defer import succeed
from twisted.web.iweb import IBodyProducer
from __future__ import print_function
from twisted.internet import reactor
from twisted.web.client import Agent
from twisted.web.http_headers import Headers
from stringprod import StringProducer


@implementer(IBodyProducer)
class StringProducer(object):
def __init__(self, body):
    self.body = body
    self.length = len(body)

def startProducing(self, consumer):
    consumer.write(self.body)
    return succeed(None)

def pauseProducing(self):
    pass

def stopProducing(self):
    pass


agent = Agent(reactor)
body = StringProducer("hello, world")
d = agent.request(
    'GET',
    'http://example.com/',
    Headers({'User-Agent': ['Twisted Web Client Example'],
             'Content-Type': ['text/x-greeting']}),
    body)

def cbResponse(ignored):
    print('Response received')
d.addCallback(cbResponse)

def cbShutdown(ignored):
    reactor.stop()
d.addBoth(cbShutdown)

reactor.run()
{% endhighlight %}
  Handling Response: ;)
 {% highlight python %}
                                      from __future__ import print_function

                                      from pprint import pformat

                                      from twisted.internet import reactor
                                      from twisted.internet.defer import Deferred
                                      from twisted.internet.protocol import Protocol
                                      from twisted.web.client import Agent
                                      from twisted.web.http_headers import Headers

                                      class BeginningPrinter(Protocol):
                                          def __init__(self, finished):
                                              self.finished = finished
                                              self.remaining = 1024 * 10

                                          def dataReceived(self, bytes):
                                              if self.remaining:
                                                  display = bytes[:self.remaining]
                                                  print('Some data received:')
                                                  print(display)
                                                  self.remaining -= len(display)

                                          def connectionLost(self, reason):
                                              print('Finished receiving body:', reason.getErrorMessage())
                                              self.finished.callback(None)

                                      agent = Agent(reactor)
                                      d = agent.request(
                                          'GET',
                                          'http://jsonplaceholder.typicode.com/posts',
                                          Headers({'User-Agent': ['Twisted Web Client Example']}),
                                          None)

                                      def cbRequest(response):
                                          print('Response version:', response.version)
                                          print('Response code:', response.code)
                                          print('Response phrase:', response.phrase)
                                          print('Response headers:')
                                          print(pformat(list(response.headers.getAllRawHeaders())))
                                          finished = Deferred()
                                          response.deliverBody(BeginningPrinter(finished))
                                          return finished
                                      d.addCallback(cbRequest)

                                      def cbShutdown(ignored):
                                          reactor.stop()
                                      d.addBoth(cbShutdown)

                                      reactor.run()
{% endhighlight %}
 {% highlight python %}


    from twisted.web.server import Site
    from twisted.web.static import File
    from twisted.internet import reactor

    resource = File('/tmp')
    factory = Site(resource)
    reactor.listenTCP(8888, factory)
    reactor.run()

{% endhighlight %}
  Generating page dynaically
  <pre><code class="python">
  from twisted.internet import reactor
  from twisted.web.server import Site
  from twisted.web.resource import Resource
  import time

  class ClockPage(Resource):
      isLeaf = True
      def render_GET(self, request):
          return "<html><body>%s</body></html>" % (time.ctime(),)

  resource = ClockPage()
  factory = Site(resource)
  reactor.listenTCP(8880, factory)
  reactor.run()
  </code></pre>

  Static url dispatch
  <pre><code class="python">
  from twisted.web.server import Site
  from twisted.web.resource import Resource
  from twisted.internet import reactor
  from twisted.web.static import File

  root = Resource()
  root.putChild("foo", File("/tmp"))
  root.putChild("bar", File("/lost+found"))
  root.putChild("baz", File("/opt"))

  factory = Site(root)
  reactor.listenTCP(8880, factory)
  reactor.run()
    </code></pre>
  Dynamic url dispatch
    <pre><code class="python">
  from twisted.web.server import Site
  from twisted.web.resource import Resource
  from twisted.internet import reactor

  from calendar import calendar

  class YearPage(Resource):
      def __init__(self, year):
          Resource.__init__(self)
          self.year = year

      def render_GET(self, request):
          return "<html><body><pre>%s</pre></body></html>" % (calendar(self.year),)

  class Calendar(Resource):
    def getChild(self, name, request):
        return YearPage(int(name))

  root = Calendar()
  factory = Site(root)
  reactor.listenTCP(8880, factory)
  reactor.run()
      </code></pre>


Handling errors
<pre><code class="python">
  from twisted.web.server import Site
  from twisted.web.resource import Resource
  from twisted.internet import reactor
  from twisted.web.resource import NoResource

  from calendar import calendar

  class YearPage(Resource):
      def __init__(self, year):
          Resource.__init__(self)
          self.year = year

      def render_GET(self, request):
          return "<html><body><pre>%s</pre></body></html>" % (calendar(self.year),)

  class Calendar(Resource):
      def getChild(self, name, request):
          try:
              year = int(name)
          except ValueError:
              return NoResource()
          else:
              return YearPage(year)

  root = Calendar()
  factory = Site(root)
  reactor.listenTCP(8880, factory)
  reactor.run()
</code></pre>
Aynchronous response:
Next we need to define the render method. Here’s where things change a bit. Instead of using callLater ,
We’re going to use deferLater this time. deferLater accepts a reactor, delay (in seconds, as with callLater ), and a function to call
after the delay to produce that elusive object discussed in the description of Deferred s. We’re also going to use _delayedRender as the
callback to add to the Deferred returned by deferLater . Since it expects the request object as an argument, we’re going to set up the
deferLater call to return a Deferred which has the request object as its result.

<div id="pp">
<pre><code class="python">
  from twisted.internet.task import deferLater
  from twisted.web.resource import Resource
  from twisted.web.server import NOT_DONE_YET
  from twisted.internet import reactor

  class DelayedResource(Resource):
      def _delayedRender(self, request):
          request.write("<html><body>Sorry to keep you waiting.</body></html>")
          request.finish()

      def render_GET(self, request):
          d = deferLater(reactor, 5, lambda: request)
          d.addCallback(self._delayedRender)
          return NOT_DONE_YET

  resource = DelayedResource()
</code></pre>
</div>
Interrupting Connection:
Notice that since _responseFailed needs a reference to the delayed call object in order to cancel it, we passed that object to addErrback .
Any additional arguments passed to addErrback (or addCallback ) will be passed along to the errback after the Failure instance which is
always passed as the first argument. Passing call here means it will be passed to _responseFailed , where it is expected and required.


<pre><code class="python">
  from twisted.web.resource import Resource
  from twisted.web.server import NOT_DONE_YET
  from twisted.internet import reactor

  class DelayedResource(Resource):
      def _delayedRender(self, request):
          request.write("<html><body>Sorry to keep you waiting.</body></html>")
          request.finish()

      def _responseFailed(self, err, call):
          call.cancel()

      def render_GET(self, request):
          call = reactor.callLater(5, self._delayedRender, request)
          request.notifyFinish().addErrback(self._responseFailed, call)
          return NOT_DONE_YET
</code></pre>
encoding response :
Using compression on SSL served resources where the user can influence the content can lead to information leak, so be careful which resources use request encoders.

Note that only encoder can be used per request: the first encoder factory returning an object will be used, so the order in which they are specified matters.
<pre><code class="python">
  from twisted.web.server import Site, GzipEncoderFactory
  from twisted.web.resource import Resource, EncodingResourceWrapper
  from twisted.internet import reactor, endpoints

  class Simple(Resource):
      isLeaf = True
      def render_GET(self, request):
          return "<html>Hello, world!</html>"

  resource = Simple()
  wrapped = EncodingResourceWrapper(resource, [GzipEncoderFactory()])
  site = Site(wrapped)
  endpoint = endpoints.TCP4ServerEndpoint(reactor, 8080)
  endpoint.listen(site)
  reactor.run()
</code></pre>

  <body>
    <!-- <div id="full">
    </div> -->
  </body>
</html>
