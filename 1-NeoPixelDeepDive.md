# Introduction

Now that we've gotten to grips with some of the possibilities demonstrated in [The First Session](0-GettingStarted.md), we're going to do a deep dive into the use of the [NeoPixel](https://www.adafruit.com/category/168) range of RGB lights. 

We've already experimented with the [NeoPixel Ring](https://www.adafruit.com/product/1463) in the last session, so we'll start off there again, but from there we're going to extend on from here to strip lights and matrix arrays

![](img/ring.jpg)

## Background / Reminder

Last time we were using the ESP32 microcontroller which had advanced networking capabilities and better sensor processing power. 

This time around we're using the simpler Arudino Uno boards, which are more stable for interfacing with the NeoPixel range with longer/more modules.

We started with the simple code below. It's worth taking some time to re-read the steps of the code and make sure we understand whats going on. Remember that lines that start with `//` are "comments" that the controller ignores and are just for the programmers aid.

```cpp
// NeoPixel Ring simple sketch (c) 2013 Shae Erisson
// Augmented 2019 Andrew Bolster
// Released under the GPLv3 license to match the rest of the
// Adafruit NeoPixel library

#include <Adafruit_NeoPixel.h>
#ifdef __AVR__
 #include <avr/power.h> // Required for 16 MHz Adafruit Trinket
#endif

// Which pin on the Arduino is connected to the NeoPixels?
//#define PIN        6 // On Trinket or Gemma, suggest changing this to 1
//#define PIN 		 25 // Best for the ESP32
#define PIN 			 4 // Best for the Arudino Uno

// How many NeoPixels are attached to the Arduino?
#define NUMPIXELS 16 // Popular NeoPixel ring size

// When setting up the NeoPixel library, we tell it how many pixels,
// and which pin to use to send signals. Note that for older NeoPixel
// strips you might need to change the third parameter -- see the
// strandtest example for more information on possible values.
Adafruit_NeoPixel pixels(NUMPIXELS, PIN, NEO_GRB + NEO_KHZ800);

#define DELAYVAL 500 // Time (in milliseconds) to pause between pixels

// This is run once when the controller starts up
void setup() {
  // These lines are specifically to support the Adafruit Trinket 5V 16 MHz.
  // Any other board, you can remove this part (but no harm leaving it):
#if defined(__AVR_ATtiny85__) && (F_CPU == 16000000)
  clock_prescale_set(clock_div_1);
#endif
  // END of Trinket-specific code.

  pixels.begin(); // INITIALIZE NeoPixel strip object (REQUIRED)
}

// This section is run repeatedly forever after setup() has completed
void loop() {
  pixels.clear(); // Set all pixel colors to 'off'

  // The first NeoPixel in a strand is #0, second is 1, all the way up
  // to the count of pixels minus one.
  for(int i=0; i<NUMPIXELS; i++) { // For each pixel...

    // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
    // Here we're using a moderately bright green color:
    pixels.setPixelColor(i, pixels.Color(0, 150, 0));

    pixels.show();   // Send the updated pixel colors to the hardware.

    delay(DELAYVAL); // Pause before next pass through loop
  }
}
```

## Rings, Strips and Matrixes

Fundamentally there is no difference between the Rings, Strips and Matrixes other than the number of and physical layout of LED modules.

In all cases, NeoPixel kits can be "Daisy Chained", so if you have two strips of 60 LED's each and you connect them together, you can address pixels from 0-120.

They are all numbered from 0-N, which makes sense for the Rings and the Strips, but for the Matrix, the layout is a bit more subtle, but might give you some inspiration for where to take your projects next!

![Matrix](img/leds_shield-horiz.jpg)

The red circles module is the `0` module, the 'top right' module is number `7`, and the first module on the left of the second row is module `8`. This is much the same as how CRT's build up an image on their displays, and how LED/LCD monitors store their state in memory (as 'one' long line that we know the 'width' of)

![raster](img/1280px-Raster-scan.png)


# Ring/Strip Challenges

All of these challenges have 'solutions' provided for them, but remember, there is more than one way to do things, and you should *not* check out the solutions until you've attempted the challenges yourself. And as always, Google is your friend!

There are also optional unsolved 'Extensions' to challenges that might be interesting additions to existing challenges for you to explore

Also feel free to explore your own 'extensions' to challenges and let Andrew know and he can add them to the next class!

These challenges can be done using any of the Pixel Rings, Strips or Matrixes.


## SimpleSingle: "Simple" Ring with one light at a time

Starting off with the `simple` example, either based on the above code or from the **Examples > Adafruit Neopixels > Simple** sketch, instead of the lights "growing" around the ring, make them go one at a time. 

<details>
	<summary>Solution</summary>
	<p>
	
```cpp
// This section is run repeatedly forever after setup() has completed
void loop() {
  pixels.clear(); // Set all pixel colors to 'off'

  // The first NeoPixel in a strand is #0, second is 1, all the way up
  // to the count of pixels minus one.
  for(int i=0; i<NUMPIXELS; i++) { // For each pixel...

    // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
    // Here we're using a moderately bright green color:
    pixels.setPixelColor(i, pixels.Color(0, 150, 0));

    pixels.show();   // Send the updated pixel colors to the hardware.

    delay(DELAYVAL); // Pause before next pass through loop
    
    pixels.setPixelColor(i, pixels.Color(0, 0, 0)); // Turn this light off 
    // (this won't be applied to the strip until `pixels.show()` is called in the next loop
  }
}
```

	</p>
	
</details>

## SimpleStrip/SimpleSingleStrip: "Simple" with the NeoPixel Strip

What do you have to change to adapt the original "simple" code (or indeed, your solution to the "Simple Single") to use the full length of the strip.

<details>
	<summary>Hint</summary>
	<p>The NeoPixel Strip has 60 LED's and the NeoMatrix has 64 LEDs</p>
</details>
<details>
	<summary>Solution</summary>
	
Update the `#define NUMPIXELS` value defined at near the top of the sketch to _60_ for the pixel strip
	
</details>

## Random: 

Turn random LED's on and off

<details>
	<summary>Solution</summary>
	
Sometimes it's better to keep things simple!

```cpp
void loop() {
    pixels.clear();
    // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
    // Here we're using a moderately bright green color:
    pixels.setPixelColor(random(NUMPIXELS), pixels.Color(0, 150, 0));
    pixels.show();   // Send the updated pixel colors to the hardware.

    delay(DELAYVAL); // Pause before next pass through loop
}
```

</details>

### Extension: Random with Random Colours

<details>
	<summary>Hint</summary>

Arduino has a built in `random(N)` function where `N` is the maximum possible number, so `random(10)` will return random numbers between 0-10

</details>


### Extension: Random Fill

Instead of "clearing" the `pixels` on each random LED activation, allow the selection of random LED's on the strip (with random colours too if you like!) until the strip is filled, and then clear it and start again.

How do you keep track of what Pixels are on and off? How do you know they're all off? It's not very efficient to randomly pick as you'll regularly pick LED's that are already on. 

Now, that effect is pretty cool on it's own, but try to work out a way to rapidly 'guess' random pixels, check if they're on or not, and if they're already on, move on to the next one.

<details>
	<summary>Hint</summary>

`pixels.getPixelColor(i)`

</details>


## Even/Odd

All at once, turn every other LED (0,2,4,6,8, etc) on and off, then turn the alternate (1,3,5,7,etc) on and off, and repeat

<details>
	<summary>Solution</summary>
	
Above the `setup()` function add

```cpp
#define SKIP 2 // Width of Skip (we jump every other LED)
```

and then update the `loop` with

```cpp
void loop() {
  // The first NeoPixel in a strand is #0, second is 1, all the way up
  // to the count of pixels minus one.
  for(int i=0; i<SKIP; i++) { // For each 'SKIP' start (0,1,etc)
    
    pixels.clear(); // Set all pixel colors to 'off'
    
    for( int j=i; j<NUMPIXELS; j+=SKIP){ // (Start from the i, and 
                                  // skip over until the end of the strip

      // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
      // Here we're using a moderately bright green color:
      pixels.setPixelColor(j, pixels.Color(0, 150, 0));
    }
     
    pixels.show();   // Send the updated pixel colors to the hardware.

    delay(DELAYVAL); // Pause before next pass through loop
  }
}
```

</details>

### Extension: Alternate Colours

One colour for evens, one colour for odds

### Extension: Random Colours

Random colour for each loop

### Extension: Every N

Could you extend this to skip in 3's instead of 2's? What about 5's?

## Line Chase

When we did "Simple Single", we had one LED 'moving' up the strip. How could we change this to being 5 LED's moving across the strip (or any number of LEDs) moving as a block.

<details>
	<summary>Solution</summary>

Above the `setup()` function add

```cpp
#define CHASE 5 // How long a block of active LEDs we have
```

and then update the `loop` with
	
```cpp
void loop() {
  pixels.clear(); // Set all pixel colors to 'off'

  // The first NeoPixel in a strand is #0, second is 1, all the way up
  // to the count of pixels minus one.
  for(int i=0; i<NUMPIXELS; i++) { // For each pixel...

    // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
    // Here we're using a moderately bright green color:
    pixels.setPixelColor(i, pixels.Color(0, 150, 0));

    pixels.show();   // Send the updated pixel colors to the hardware.

    delay(DELAYVAL); // Pause before next pass through loop

    // turn off the LED 'CHASE' elements behind me
    if (i>(CHASE-1)){
      pixels.setPixelColor(i-(CHASE-1), pixels.Color(0, 0, 0));
    }
  }
}
```

Why is this using `(CHASE-1)` instead of just `CHASE`?

</details>

### Extension: Read The Docs

Look for the documentation for the Adafruit NeoPixel library and see if there is a better way to accomplish this.

<details>
	<summary>Hint</summary>
	
	`fill()`
	
</details>

### Extension: Looping Line

When the "block" gets to the end of the strip, make it continue "around" the strip as if the strip was in a ring, so there are always 5 LED's active.

<details>
	<summary>Hint</summary>
	
![formula](https://render.githubusercontent.com/render/math?math=mod(59,60)=59)

![formula](https://render.githubusercontent.com/render/math?math=mod(60,60)=0)

![formula](https://render.githubusercontent.com/render/math?math=mod(61,60)=1)

[Extra Link](https://www.arduino.cc/reference/en/language/structure/arithmetic-operators/modulo/)

</details>

### Extension: Bouncing Line

When the "block" gets to the end of the strip, make it "bounce back" and come backwards down the line instead of just looping "around" the strip

<details>
	<summary>Hint</summary>

What goes `i++`, must go `i--`

</details>

## Rainbow

Given that we know that our `pixels.Color(red,green,blue)` function lets us create specific colours for pixels, how can we generate a smooth 'rainbow' of colours, 

<details>
	<summary>Hint</summary>

![](img/colourwheel.png)
</details>

<details>
	<summary>Solution</summary>
	
This time it makes more sense to break things out into "functions" rather than having it all in one `loop()`

A few things to note:

* `uint32_t` means "unsigned 32-bit integer" so it can store values from 0-4294967295. This might seem like a random number but it might be more familiar in it's hexadecimal form; 0xFFFFFFFF. This is enough room to store RGBA (Red, Green, Blue, Alpha) values in one "number" to save space, and is the same thing that has been "returned" by our previous calls to `pixels.Color()`
* 0-255 is a range of values that can be stored in 8-bits cleanly. We could use "0-360" or "0-100" however this would produce edge cases related to decimal places that would be very very messy...
* There is a built in arduino function called `map` that translates from one range to an other; i.e. we have 60 pixels on the strip, and our `ColourWheel` function goes from 0-255, so we need to convert our "pixel id" into the relevant "colour id" 

```cpp

// Input a value 0 to 255 to get a color value.
// The colours are a transition r - g - b - back to r.
uint32_t ColourWheel(byte WheelPos) {
  if(WheelPos < 85) {		//first 1/3rd of a colourwheel (Red to Green)
    return pixels.Color(
    	WheelPos * 3, 
    	255 - WheelPos * 3, 
    	0
    );
  } 
  else if(WheelPos < 170) {//middle 1/3rd of a colourwheel (Green to Blue)
    WheelPos -= 85;
    return pixels.Color(
    	255 - WheelPos * 3, 
    	0, 
    	WheelPos * 3
    );
  } 
  else {						//last 1/3rd of a colourwheel (Blue to Red)
    WheelPos -= 170;
    return pixels.Color(
    	0, 
    	WheelPos * 3, 
    	255 - WheelPos * 3
    );
  }
}

// Generate a rainbow across the array
void loop() {
	for(int i=0; i<pixels.numPixels(); i++) {
	  strip.setPixelColor(i, 
	  	ColourWheel(
	  		map(i,
	  			 0,pixels.numPixels(),
	  			 0,255
	  		)
	  	);
	}
	pixels.show();
	delay(100)
}

```

</details>

## Rolling Rainbow

How can you adapt the Rainbow to that it continuously 'slides' across the strip?

<details>
	<summary>Hint</summary>
	
This is similar to the solution form the 'Extension: Looping Line' hint...

</details>
	
<details>
	<summary>Solution</summary>
	
```cpp
void rainbow(uint8_t wait) {
  for(int j=0; j<256; j++) {
    for(int i=0; i<pixels.numPixels(); i++) {
	  strip.setPixelColor((i+j) % pixels.numPixels(), 
	  	ColourWheel(
	  		map(i,
	  			 0,pixels.numPixels(),
	  			 0,255
	  		)
	  	);
    }
    pixels.show();
    delay(wait);
  }
}

void loop() {
   rainbow(10);
   delay(10);
}
```

</details>

# Matrix Challenges

When moving on to the Matrix challenges, these are best accomplished in teams of three, as the experimentation is a bit more involved (and we have fewer Matrix displays than Strips as they're more expensive...)

**Be very careful about the wiring of the matrix displays as they are different than the strips; Check with Andrew before powering anything on!**


