---
layout: post
title: Python Twisted Klein tutorial
categories:
- blog
---

{% highlight python %}
from klein import route, run, Klein

class Users(object):
    routes = Klein()
    @routes.route("/<string:username>")
    def an_user(self, request, username):
        request.setHeader('content-type', 'text/plain')
        return 'A User Named "{username}".'.format(username=username)

class MyFacebookApis(object):
    fb_apis = Klein()
    @mathroutes.route("/getPageLikes/<int:fb_id>")
    def add(self, request, a, b):
        return str(a + b)


@route("/users", branch=True)
def users_app(request):
    return UsersApp().routes.resource()

@route("/facebook", branch=True)
def math_app(request):
    return MyFAcebookApis().fb_apis.resource()

@route("/")
def hi(request):
    return "Hi"

run("localhost", 8123)
end

{% endhighlight %}