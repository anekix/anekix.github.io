---
layout: post
title: serving and uploading static files using nginx
categories:
- blog
---

Serving and uploading static files with nginx:

Assuming we have nginx installed in system and our nginx configuration file resides at `/etc/nginx/sites-enabled/testconf.conf`

create a directory called `data` that will store all our images that we wish to serve.


add these in  `testconf.conf`

{% highlight python %}

 server{
      listen *:9999;
      location /images/ {
      root /data;
      }
   }
{% endhighlight %}

now if we make a request to `localhost:9999/images/a.jpg`

we will get the image file `a.jpg` located in `/data/images`.

importat part to note above is `root` directive. Any request coming as `localhost:9999/images/...` matches the above location block,
therefore nginx will strip the part after `localhost:9999/images` ( as we have defined `/images/` in location ) and append it after the path defined as root.




