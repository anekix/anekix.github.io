If you happen to study about automat theory , you would have come across terms like `state machines` or in a more general term `FSM( Finite state machines)`.
These are quite importatnt tools and some specific problems can be very well modeled with state machines.

I think they are such immportant tools that they shoud be more often used . in this post we like gain a basic understanding of what makes a state machine when should.

A state machine is a piece of sftware that accepts inputs and porvide outputs. if you are thinking that any program ever written does that, then you are right every software is indeed a state machine of some sort.

Softwares usually have data or values associated with them which we call as states and also usually the behaviour of the program depends upon the state that program has. consier the example of a cofee brewing machine, it can have several states

Conside an example we are making a 2d game in which we are tasked to desgin the various movements of the characters depending upon the key press.

A) When pressed key `SPACE` player should perform a jump.

Lets write this some code for this.


```python
def HandlePlayerInput(key):
    if key == SPACE:
      Yvelocity = JUMP_VELOCITY # set velocity in y direction to jump
      player = PLAYER_JUMP_IMAGE  
```
Now there is some problem with the code above. can you guess what it is?

If you have ever tried to write eve the simplest game you would know there is always an `update()` loop which looks for various events( and thus appropriatley set relevant actions like increasing/decreasing health, rendering appropriate graphics to the screen.. etc) during the game lifecycle.

what happens if i go on and press `SPACE` key immediately follwed by another `SPACE` key press? our player would perform an air jump. that is it will jump again while in mid air! well thats not a required behaviour. So what do we do now?

Lets add a Flag/Boolean to check if the player is on ground and perform jump only if the player is on ground. The new code is:
```python
def HandlePlayerInput(key):
    if key == SPACE and player_is_jumping == False: # this new conditions will avoid mid air jump.
      Yvelocity = JUMP_VELOCITY 
      player = PLAYER_JUMP_IMAGE
      player_is_jumping = True  #indicate that player is jumping so hamering SPACE wont make player to jump infinetly!
```

Now we aslo have a requirement that our Hero should duck down when pressing `DOWN_ARROW` key and stand back up when we release it.

The above code can be modified as:
```python
def HandlePlayerInput(key):
    if key == SPACE and player_is_jumping == False: 
        Yvelocity = JUMP_VELOCITY 
        player = PLAYER_JUMP_IMAGE
        player_is_jumping = True 
      
    if key == PRESS_DOWN and player_is_jumping == False:
        plyer = PLAYER_DUCK_IMAGE
    if key == RELEASE_DOWN:
        player = PLAYER_STANDING_IMAGE
```








B) When key `SHIFT` is pressed player should perform a duck.
C) 
