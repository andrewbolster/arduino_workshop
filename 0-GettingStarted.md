# Hardware

* [ESP32](https://smile.amazon.co.uk/gp/product/B071JR9WS9/) - Arduino Compatible Microprocessor board with Wifi and Bluetooth
* [Adafruit FXOS8700+FXAS21002](https://coolcomponents.co.uk/products/precision-nxp-9-dof-breakout-board-fxos8700-fxas21002) - 9-DOF Orientation sensor
* [NeoRing](https://coolcomponents.co.uk/products/12-led-ring-sk6812-5050-rgb-led-with-integrated-drivers-adafruit-neopixel-compatible) 12 segment ring chainable ring of individually addressable RGB LEDs
* Double-wide breadboard
* Micro USB cable

# Introduction

The intention of this workshop is to provide a practical introduction on sensing, inputs, outputs, control, networking and communication for Arduino devices. We use the ESP32 for this purpose but the learnings from this can be applied to any Arduino-family microcontrollers.

By the end of this workshop you should be comfortable with: 

* Manipulating, setting up and wiring up development board, breadboards and sensor packages (and comfortable to fiddle with things when they're not working!)
* Configuring the Arduino IDE for a specific board, including debugging techniques such as the serial monitor/plotter
* Leveraging the Examples provided in the Arduino IDE to validate experimental assumptions about the hardware you're using
* Experimenting and combining said Examples to accomplish unique and customised applications with minimum "green-field" programming.

The structure of this workshop enables both instructor led and self-driven learning; the intention is that we will start off with fairly constrained and prescriptive activities which become increasingly flexible as we go on. Feel free to run ahead if you feel comfortable that you understand whats going on.

By the end of it, the training wheels are off and you will be expected to be able to Google your way out of a paper bag. The mentors are around to help out so ask all the questions you like! We're here to help!

# First Steps

## Setting up the development environment

Download the Arduino API from [here](https://www.arduino.cc/en/main/software) (Ignore the "donate" button) and install it on your machine. 

_For Mac users, you will also have to install an additional driver from [here](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)_

Once the Arduino IDE is installed, launch it and open **File > Preferences**, and add the following to the "Additional Boards Manager URLs:

```https://dl.espressif.com/dl/package_esp32_index.json```

Then go to **Tools > Board > Boards Manager**

And search for the **ESP32**, and install the **ESP32 by Espressif Systems** module.

Once installation is complete, from **Tools > Board** select **FireBeetle-ESP32**

## Testing the board

If it hasn't already been mounted, gently insert the ESP32 into the breadboard. This can be fiddly, and some dev boards may need their pins bent to fit, do it gently...

It's best to place the board with the USB connection at the edge of the breadboard, and with the ESP32 roughly centred, to provide maximum connectivity.

Don't plug the USB cable in yet!

In the Arduino Editor, replace the current code with the following:

```cpp
void setup() {
  Serial.begin(9600);
}

void loop() {
  Serial.println("Hello from ESP32");
  delay(1000);
}
```

This defines two functions; `setup` and `loop`. These take no arguments and return no values, but are the "base" of any arduino program. 

`setup` is run **once** when the controller is started up, and then `loop` is continually run "forever"

Press the ✔ button (Compile) near the top left of the editor. This will "Compile" your Arduino code into machine code that can be run on the microcontroller. If you've made any mistakes then this step will fail, so this is a good way to make sure you're code is going to work on the ESP32 at all! (Pay attention to spelling, Case and semicolons!)

Once your code safely compiles, save this sketch as "SerialTest".

Now plug the microcontroller into the computer, make sure you have the correct port selected under **Tools > Port** and press the ⇨ button (Upload) beside the compilation button. This *also* compiles your code again, but this time, will also "flash" or program the code onto the connected device.

The way that the computer does this is by setting up a "Serial" connection to the microcontroller, like a simple messaging system, where the two ends of the connection can send messages back and forth. We can also interact with and monitor the operation of the ESP32 through this serial connection.

(Depending on what was running on the board before, your serial connection and your drivers, you may get connection errors as part of this Upload step; to resolve this, when the console in the bottom of the editor says "Connecting...", press the "BOOT" button on the board. This reboots the device into an 'easy to connect to' state)

Now open **Tools > Serial Monitor**, and you should see a friendly message repeating on the screen. If you have a jumble of crazy characters, make sure that the "Baudrate" of the serial monitor is set to 9600 (bottom right of the window)

## Sensor Setup and Testing

The sensor board we're using has two chips on it; the FXOS8700 and the FXAS21002C.

The FXOS board is a combined Accelerometer and Magnetometer, where the acceleration can detect "shaking" in the X/Y/Z axes, and the magnetometer can be thought of as a 3D compass (or can also be used as a metal detector, but we won't be using that today!).

In the Arduino IDE, navigate to **Tools > Manage Libraries** and install the following:
* Adafruit FXOS8700
* Adafruit FXAS21002C
* Adafruit AHRS
* Adafruit Unified Sensor

These teach the Arduino platform how to talk to the the sensor chips on the motion sensor we'll wire up next, and how to combine these sensor values into a single, smoothed, orientation system.

Wiring notes (sensor > ESP32): 
* VIN > 3V3
* GND > GND
* SCL > D22
* SDA > D21

From the Arduino IDE, open **File > Examples > Adafruit FXOS8700 > sensorapi**, and Upload that to the connected ESP32.

Hopefully, now, where you open the serial monitor again (**Tools > Serial Monitor**) you'll a stream of messages with the Acceleration and Magnetometer reading from the FXOS8700 chip on the sensor.

Have a look at the code and see where these values come from.

Now fire up **Tools > Serial Plotter**; this is a tool that looks for numerical values in the serial output stream and plots them graphically over time. 

Whats wrong with the graph?

### Fixing Plotting Output

Have a look at this section of code in the FXOS8700 `sensorapi` example

```cpp
  /* Display the accel results (acceleration is measured in m/s^2) */
  Serial.print("A ");
  Serial.print("X: "); Serial.print(aevent.acceleration.x, 4); Serial.print("  ");
  Serial.print("Y: "); Serial.print(aevent.acceleration.y, 4); Serial.print("  ");
  Serial.print("Z: "); Serial.print(aevent.acceleration.z, 4); Serial.print("  ");
  Serial.println("m/s^2");

  /* Display the mag results (mag data is in uTesla) */
  Serial.print("M ");
  Serial.print("X: "); Serial.print(mevent.magnetic.x, 1); Serial.print("  ");
  Serial.print("Y: "); Serial.print(mevent.magnetic.y, 1); Serial.print("  ");
  Serial.print("Z: "); Serial.print(mevent.magnetic.z, 1); Serial.print("  ");
  Serial.println("uT");

  Serial.println("");
```

This is printing out values like this: 

```
A X: 0.2489  Y: 0.2919  Z: 10.1934  m/s^2
M X: -39.9  Y: -55.0  Z: -16.1  uT

A X: 0.2536  Y: 0.2919  Z: 10.1791  m/s^2
M X: -39.7  Y: -54.8  Z: -16.1  uT

```

Because we're getting *two* lines for each "observation", we're getting the spikes in the graph, as each line is jumping between being the Accelerometer values (lines beginning with `A`) and Magnetometer values (beginning with `M`)

Try the following tasks, uploading the sketch to the ESP32 and observing the Serial Plotter Graph each time while you move the board around:

1. Print just the Accelerometer X,Y,Z values, separated by commas, on each line
2. Print just the Magnetometer X,Y,Z values, separated by commas, on each line (If you can, find something metal or magnetic and wave it near the sensor as well)
3. Print both sets of values together

## Using the Gyroscope

Now we can switch over to inspect the output from the other sensor on the board; a Gyroscope. 

From the Arduino IDE, open **File > Examples > Adafruit FXAS21002C > sensorapi**, and Upload that to the connected ESP32. This is almost exactly the same as the previous `sensorapi` file, so try experimenting in the same way with that file and investigate the output from the device. 

## Sensor Fusion: Better than the sum of parts

So, we have three sets of sensor values related to position, movement and orientation. We can use a technique called [Sensor Fusion](https://en.wikipedia.org/wiki/Sensor_fusion) to combine these measurements into a more reliable and stable sensor value.

This is mathematically complex, but thankfully, most of the hard work is done for us by the `Adafruit AHRS` module we installed earlier.

Open **File > Examples > Adafruit AHRS > ahrs_fusion_usb**, upload it to the ESP32, and watch the serial monitor and serial plotter again. 

(You may want to comment out the lines below to get rid of a stupid line related to the time.)

```cpp
  Serial.print(millis());
  Serial.print(" - Orientation: ");
```

You can see that this program takes input from all three values, merges them using a 'filter' object, and from that you can get three values; heading, pitch, and roll.

Heading is effectively the relative top-down compass direction (sometimes called XY rotation), Pitch is the 'forward backward rocking' of the module, (or XZ rotation), and Roll is 'side to side rocking' (or YZ rotation).

We can also directly visualise this rotation using a [Processing](https://processing.org/download/) script. So install processing from the link [here](https://processing.org/download/).

While that's installing, go back to the Arduino code and uncomment the lines you commented out before.

Once that's installed, we also need to manually install an extra library to allow processing to manipulate 3D Object files; Grab the zip-file from [here](https://github.com/adafruit/Adafruit_BNO055/raw/master/OBJLoader/OBJLoader.zip) and unzip it to Processings Libraries path: under Mac this should be `Documents/Processing/Libraries`, under Windows and Linux this should normally be under your Home or My Documents folders

With that done, we still have a missing library for graphics manipulations, but unlike the `OBJLoaders`, we can install these from within Processing itself; Under **Tools > Add Tool > Libraries** and install the `G4P` library. 

Once that's all done, from the Arduino workspace (usually right beside the `Processing` folder) open `Arduino/libraries/Adafruit_AHRS/processing/bunnyrotate_ahrs_fusion_usb` with Processing.

Run it and play with the sensor again and see what happens!


### Calibration (Optional)

You will probably find that these values aren't always perfect, or may not do full 0-360 ranges, or may jump all over the place; this is because different environments, setups, and components interfere with different elements of the sensor process, so we can perform a process of calibration to try and make the device more accurate. 

If your sensor is *really* not making any sense; follow the instructions [here](https://learn.adafruit.com/nxp-precision-9dof-breakout/calibration-usb) to generate calibration in formation for your setup. If you are going to add additional devices or enclosures to your project, it's recommended to repeat these calibration steps once your project is "in place"


# NeoPixels

Back in Arduino land, from **Tools > Manage Libraries** install the "Adafruit NeoPixel" library. 

Launch the **Examples > Neopixels > Simple Sketch**, read through the code to get an idea of what it's doing. 

Now strip the ends off the wires attached to the neopixel, connect the wires up (NeoPixel > ESP32):

* PWR (Red) > VIN
* GND (Black) > GND
* IN (Purple) > D4

Fixes: 
* Check that the PIN variable is set to 4
* Check that the NUMPIXELS variable is set to the same number of LED modules on your pixel ring/strip

Challenges: 

* How do you make it cycle? (i.e. all on-> all off -> Repeat)
* How do you make it change colour?
* How do you make it only one cell at a time?
* Can you make a rotating rainbow?
* Can you "daisy chain" the NeoPixels with another module? (hint; using the Yellow "OUT" pin as the "IN" pin for the next module)

# WiFi

So far, everything we've done is on the device, and using the serial port for control; but what about talking to the outside world? One of the strengths of the ESP32 board is it's built in Wifi and Bluetooth Low Energy capabilities. We're gonna stick to the wifi for now...

Fire up **Examples > WiFi > WifiMulti** and see if you can set it up to connect to the Farset WiFi

Note: if you configure two networks that are both valid in the same location (for instance, Farset-Members and Farset-Guest) the program can get a bit confused and bounce between them...

**If you get a "Brownout detector was triggered" in the serial console when you program the ESP32, try a different USB cable!**

By Default, the `WiFi` client is only IP based (basically Telephone numbers for the internet). To test that our network is properly configured, you need to test how to look up the IP address associated with a name: 

Above the `setup()` function, add the following

```cpp
char server[] = "farsetlabs.org.uk";
IPAddress server_ip = INADDR_NONE;
```

and inside the `loop()` function add

```cpp
WiFi.hostByName(server, server_ip);
Serial.println(server_ip);
```

The way that Arduino (and other C based languages) handles strings/arrays is by strange and dark memory assignments; The first set of lines tell the ESP32 to create a string in memory containing "farsetlabs.org.uk" called server, and a space for an IPAddress but with "No" contents.

The second set inside the loop uses the `WiFi.hostByName` function to take the `server` variable ("farsetlabs.org.uk"), ask the WiFi chip what IPAddress is assigned to that server name, and store the IP address in the `server_ip` variable. Then we print that out the standard way. 

# MQTT

Message Queuing Telemetry Transport (MQTT) is a fancy way of saying "message passing", where devices can publish and subscribe to "messages" on particular "topics", coordinated by a "broker". All devices that want to communicate together need to use the same broker, and that broker is responsible for sending the messages it recieves to the devices that have subscribed to that messages topic.

For larger projects, it is advisable to set up a custom MQTT broker such as [`mosquitto`](https://mosquitto.org/), but for our purposes, we can each set up a free [CloudMQTT](https://www.cloudmqtt.com/) instance.

These instances are limited to 5 simultaneous connections and a maximum throughput of 10kbps, which will suit us for now. 

So register for your [CloudMQTT](https://www.cloudmqtt.com/plans.html) "Cute Cat" plan, and make a note of the:

* Server
* User
* Password
* Port


## Publish Messages

Now, open a brand new Arduino Sketch, and copy/paste the below into it

```cpp
#include <WiFi.h>
#include <WiFiMulti.h>
#include <PubSubClient.h>

#define MQTT_CLIENT_NAME "<SET THIS TO WHATEVER YOU LIKE!>"
#define MQTT_USERNAME "<YOUR USERNAME>"
#define MQTT_PASSWORD "<YOUR PASSWPORD>"
#define MQTT_BROKER "<YOUR SERVER>"
# This "age" topic does not have to be configured ahead of time, and can be dynamic
#define MQTT_TOPIC "age" 

WiFiMulti wifiMulti;
WiFiClient wifi;
PubSubClient client(wifi);

long lastMsg = 0;
char msg[50];
int value = 0;
IPAddress broker_ip = INADDR_NONE;

int port = 15921;
int age = 0;


/****************************************
 * Auxiliar Functions
 ****************************************/

void reconnect_mqtt() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.println("Attempting MQTT connection...");
    
    // Attempt to connect
    if (client.connect(MQTT_CLIENT_NAME, MQTT_USERNAME, MQTT_PASSWORD)) {
      Serial.println("connected");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 2 seconds");
      // Wait 2 seconds before retrying
      delay(2000);
    }
  }
}

void callback(char* topic, byte* payload, unsigned int length) {
  # Don't worry about this function, we'll be using it later!
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i=0;i<length;i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();
}

/****************************************
 * Regular Functions
 ****************************************/

void setup()
{
    Serial.begin(115200);
    delay(10);

    wifiMulti.addAP("Farset-Members", "clubmate");
    wifiMulti.addAP("Bolster", "9794697354");

    Serial.println("Connecting Wifi...");
    while(wifiMulti.run() != WL_CONNECTED) {
      Serial.println("Wifi not connected");
      delay(1000);
    }
    Serial.println("");
    Serial.println("WiFi connected");
    Serial.println("IP address: ");
    Serial.println(WiFi.localIP());
    Serial.println(WiFi.SSID());
    WiFi.hostByName(MQTT_BROKER, broker_ip);
    Serial.println(broker_ip);
    client.setServer(broker_ip, port);
    Serial.println("Broker Set");
    reconnect();
    client.publish("status", "ALIVE");
}

void loop()
{

  if(wifiMulti.run() == WL_CONNECTED) {
        Serial.println("");
        Serial.println("WiFi connected");
        Serial.println("IP address: ");
        Serial.println(WiFi.localIP());
        Serial.println(WiFi.SSID());
        age = millis();					# This is the number of milliseconds that have passed since the program started
        itoa(age,msg,10);  			# This is similar to the WiFi.hostByName function, that converts the int age to a string msg in base 10
        reconnect();
        client.publish(MQTT_TOPIC,msg);	
        Serial.println(age);
        
  } else {
    Serial.println("WiFi connection lost");
  }
  delay(1000);
}
```

## Subscribe Messages

Subscription messages are "asynchronous", i.e. they can be recieved at any time and you don't have to "check" if a new message is available on a given topic; when you configure your connection to the broker, you tell the broker "Theses are the topics I care about" using code like;

```cpp
client.subscribe('topic name');
```

And then you can register a 'callback' function; this function will be executed by the ESP32 when the broker sends it a message from topics that you've subscribed to. We've already included this "Callback" function, but you need to register this callback function with the client using code like the below;

```cpp
client.SetCallback(callback);
```

Remove the `serial.println(age)` call from the `loop` function, and experiment with where to place the code snippets above, so that your device 'publishes' it's age and also is 'subscribed' to that age topic and prints out the messages from that topic.


# Final Challenge

Working as a group, using one ESP32 with the Orientation sensor, and another with the NeoPixel ring; create a remote controlled compass; where moving the sensor of one device changes the colour/orientation/something of the other one, using MQTT messaging in between (i.e. one shared topic) 

Between the group, you should discuss:

* How will the sensor values be formatted to the MQTT broker (topic names, number formats, etc)?
* Will you send all the information across (heading/pitch/roll) or just heading? Which strategy is better and why?
* Can you use the NeoRing to display any other information?

# Brainstorm Session

We've gone through a wide range of options and structures, and covered a lot, so at the end of the session, we'll be taking 15 minutes to discuss possibilities of other hardware and software integrations. Feel free to keep working and experimenting if you get to this step early; how much more complex can you get your programs!

When you're done, please check in with the instructors to make sure that your work is saved!
 




