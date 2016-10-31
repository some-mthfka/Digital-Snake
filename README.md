# Digital-Snake
Built with [Logisim](http://www.cburch.com/logisim/)

## Screenshots

[Screenshot #1](https://github.com/meatich/Digital-Snake/blob/master/sshot-16.png)

[Screenshot #2](https://github.com/meatich/Digital-Snake/blob/master/sshot-25.png)

[Screenshot #3](https://github.com/meatich/Digital-Snake/blob/master/sshot-27.png)

## Documentation
An LED matrix 32x32 is used. A special circuit is used to work with it: display circuit. The interface is simple: an x and y coordinate is passed to it and, on a clock tick, it turns on that place on the display. Previously drawn cells are kept alive. To remove a cell from the screen, a similar procedure happens, but with different pins.

Two RAM cards are used: one for the x-coordinates, the other one for the y-coordinates. This is done for simplicity. To access a coordinate, the same address is passed to both cards. 

Basically, there are two stages of circuit’s execution life: initialization and main loop. 

Initialization and switching to main loop is done automatically (as the clock ticks, of course). The initialization part is quite straightforward: snake’s length is initialized to two, the address of snake’s head is set to one and the direction of snake’s motion is set to right. 

Main loop is more interesting. Much more. The main loop is implemented as a counter and a demultiplexer. There are four “modules”, which the main loop switches between. Each module runs for as long as it wants until it signals the counter, which, in turn, switches clock ticks to the next module. I am going to discuss the four modules separately:

1. Snake mover. How does a snake move? Here is how: coordinates of its head are taken, changed according to the current direction and written instead of its tail’s coordinates. It is done so that inside memory, the tail is always in front of the head. That’s what snake mover module assumes and does. Also, the tail is removed from the screen.

2. Module two is the display module. It is not where the display is controlled. The only purpose of the module is to draw the head (since we replaced the tail with a new head in module 1).

3. The third module checks if snake’s head is colliding with the rest of the snake. It is a loop from zero to snake’s length, so all elements are checked. As the snake grows, more elements need to be checked, obviously. If a collision is detected – game over.

4. This module is responsible for (a) generating apples, (b) displaying them, (c) checking if the snake is eating an apple and (d) growing the snake when it eats an apple. 

  a.	Generation of apples. Two random number generators are ticked and the resulting values are used. These values are not checked for collision with the snake. What is the problem with this? Well, if an apple happens to be “inside” the snake, the player will not see it, because it is drawn only once. The solution would be to check for a collision. I decided to not do it, because it would imply several other layers of complexity: if the apple indeed crosses the snake, it would be needed to add a while loop, in which an apple would be shifted until a free position is found. But things work well nevertheless, because of the large screen size. But, just in case, the coordinates of a current apple are displayed right next to the keyboard. In hexadecimal. Hardcore. 
  
  b.	Drawing of an apple is done right after generating one.
  
  c.	When checking for collision of snake’s head with an apple, it is decided whether to hand the control to the next module (no collision) or to continue, which would mean that a collision happened and some action need to be taken.
  d.	 When snake eats an apple, it grows. Here is how it happens: all the memory cells starting from snake’s position + 1 are shifted to the right. This means that there will be a space between the current head and the shifted part. In fact, this space will be considered as tail by module 1, which will write a correct value instead. Then, execution is handed to module 1 again.

The game ends when either the size of the snake grows to fifty cells or it eats itself (or when Logisim lags, ha ha ha).
