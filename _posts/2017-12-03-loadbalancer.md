---
layout: post
title: lets try to create a load balancer from scratch in C.
categories:
- blog
---

create some variables that will be used.
{% highlight cpp %}
    int socket_desc, c, client_sock, read_size;
    char client_message[2000];
{% endhighlight %}

create a socket and print some helpful error if somethign goes wrong.
{% highlight cpp %}
    socket_desc = socket(AF_INET,SOCK_STREAM,0);
    if (socket_desc == -1){
        printf("socket could not be created");
    }
{% endhighlight %}

Now we ned to bind the socket to a socket address, so fisrt crrate an internet address and bind it to socket

{% highlight cpp %}

        struct sockaddr_in server, client;
        server.sin_family = AF_INET;
        server.sin_addr.s_addr = INADDR_ANY;
        server.sin_port = htons(9789);

        // bind socket to the internet address
        if( bind(socket_desc,(struct sockaddr*)&server, sizeof(server)) < 0){
            perror("bind failed");
        }
        puts("binding done");

{% endhighlight %}


once our socket is bound to the address make it listen to the incoming connections using `listen()`
{% highlight cpp %}

    listen(socket_desc, 3);

{% endhighlight %}

now accept any incoming connection using `accept` and send back the response that is recieved


{% highlight cpp %}

    c = sizeof(struct sockaddr_in);
    client_sock = accept(socket_desc,(struct sockaddr*)&client, (socklen_t*)&c);
    if (client_sock < 0)
    {
        perror("accept failed");
        return 1;
    }
    puts("Connection accepted");
    printf("here");
     while( (read_size = recv(client_sock , client_message , 2000 , 0)) > 0 )
    {
        //Send the message back to client
        write(client_sock , client_message , strlen(client_message));
    }
    if(read_size == 0)
    {
        puts("Client disconnected");
        fflush(stdout);
    }
    else if(read_size == -1)
    {
        perror("recv failed");
    }
{% endhighlight %}


put all of the above code in main functionso that we have :

{% highlight cpp %}

#include<stdio.h>
#include<string.h>    //strlen
#include<sys/socket.h>
#include<arpa/inet.h> //inet_addr
#include<unistd.h>    //write
 

int main(){
    int socket_desc, c, client_sock, read_size;
        char client_message[2000];

    //create an internet socket
    socket_desc = socket(AF_INET,SOCK_STREAM,0);
    if (socket_desc == -1){
        printf("socket could not be created");
    }

        struct sockaddr_in server, client;
        server.sin_family = AF_INET;
        server.sin_addr.s_addr = INADDR_ANY;
        server.sin_port = htons(9789);

        // bind socket to the internet address
        if( bind(socket_desc,(struct sockaddr*)&server, sizeof(server)) < 0){
            perror("bind failed");
        }
        puts("binding done");

    // listen on that socket
    listen(socket_desc, 3);


    c = sizeof(struct sockaddr_in);

    //accept any conection coming to that socket
    client_sock = accept(socket_desc,(struct sockaddr*)&client, (socklen_t*)&c);

    if (client_sock < 0)
    {
        perror("accept failed");
        return 1;
    }
    puts("Connection accepted");
    printf("here");

     while( (read_size = recv(client_sock , client_message , 2000 , 0)) > 0 )
    {
        //Send the message back to client
        write(client_sock , client_message , strlen(client_message));
    }
     
    if(read_size == 0)
    {
        puts("Client disconnected");
        fflush(stdout);
    }
    else if(read_size == -1)
    {
        perror("recv failed");
    }
     
    return 0;
}
{% endhighlight %}




