If you happen to study about automat theory , you would have come across terms like `state machines` or in a more general term `FSM( Finite state machines)`.
These are quite importatnt tools and some specific problems can be very well modeled with state machines.

I think they are such immportant tools that they shoud be more often used . in this post we like gain a basic understanding of what makes a state machine when should.

A state machine is a piece of sftware that accepts inputs and porvide outputs. if you are thinking that any program ever written does that, then you are right every software is indeed a state machine of some sort.

Softwares usually have data or values associated with them which we call as states and also usually the behaviour of the program depends upon the state that program has. consier the example of a cofee brewing machine, it can have several states

Conside an example we are making a 2d game in which we are tasked to desgin the various movements of the characters depending upon the key press.

A) When pressed key `SPACE` player should perform a jump.

Lets write this some code for this.


```
def HandlePlayerInput( key ):
    if key == SPACE :
      Yvelocity = JUMP_VELOCITY # set velocity in y direction to jump
      player = PLAYER_JUMP_IMAGE  
```





B) When key `SHIFT` is pressed player should perform a duck.
C) 
