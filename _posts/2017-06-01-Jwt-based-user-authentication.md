---
layout: post
title: Implementing JWT based authentication scheme in REST Api's
categories:
- blog
---


Code:

{% highlight python %}

import falcon
import json
import jwt


class AuthMiddleware:
	def process_request(self, req, resp):
		auth_free = ['/token']
		description = ('Please provide an auth token '
                           'as part of the request.')

		if req.path not in auth_free:
			token_jwt = req.get_param('token','null')
			try:
				u = jwt.decode(token_jwt, 'secret', algorithms=['HS256'])
			except Exception  as e:
				raise falcon.HTTPUnauthorized('Authentication required',
                                       description,'kk',href='http://docs.example.com/auth'   )

class Hello:	
	def on_get(self, req, resp):
		resp.body = 'hi'
class safe:
	def on_get(self, req, resp):
		resp.body = 'you are authorized ! :)'
class Token:
	def on_get(self, req, resp):
		# In real life after verification send token to the user for subsequent calls
		encoded = jwt.encode({'user':'bob'}, 'secret',algorithm='HS256') 
		resp.body = encoded	


app = falcon.API(middleware=[
    AuthMiddleware(),])


app.add_route('/', Hello())
app.add_route('/token', Token())
app.add_route('/safe', safe())

{% endhighlight %}


