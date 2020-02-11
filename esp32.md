#ESP32 Projects

##ESP32 as a IoT remote
This is a project that stemmed from a real-life problem: I needed to be able to remotely control the IoT appliances in my home (both store-bought and built by myself).
This need came along since the main device I used to use to control said devices was my smartphone, but in an effort to better my night's sleep I decided it was going to be left on my desk instead of close to the bed. This allowed avoiding hyperstimulation before sleep which would have been due, according to multiple studies, to the insane amount of information and interaction a smartphone provides. Supposedly, it is way worse than watching content (like tv) before bed. Now, without any content to watch and listen to, I have a really hard time actually falling asleep. This means I usually watch some documentaries on youtube, but I still needed a way to turn off the lights after going to bed (most of the lights in my home can only be controlled via Iot, either because of implementation, or because the switch is in an unfortunate position). Cue in the fantastic ESP32, which allowed me to build a GUI on a display from an old Nokia 8810 and subsequently operate all the devices with a simple 3-button operation.

###The Stack
This project leverages the ability of IFTTT "Webhooks" to receive a simple HTTP request and interface itself with all the devices, either natively or through other services [like with the ESP8266 and thinger.io](/esp8266.html).
These requests were made through native libraries for HTTP handling with the ESP32.
It's very easy to customize what happens once the request is sent to IFTTT via their intuitive web interface.

IFTTT, in this case, sends a request to the specific service according to the URL for the HTTP request. For example, `.../tv-on/` sends a request to SmartLife to turn on the smart plug and subsequently the TV connected to it. `.../tv-off/` does the opposite.

The remote itself only offers the option to turn on and off a device, and cannot read the current state of any device for security reasons (not a good idea to have a stream of data over the web that tells people when things in your home are on or off, because burglars). The turning on and off was implemented on a toggle basis because of personal preference.

###The Code
The code for this project presented some challenges:

-Interfacing the display and the uController
-Creating a decent GUI without depleting the ESP's resources nor the programmer's time
-Sending HTTP requests reliably

The various solutions were then implemented as functions for the main program.

####Implementing the GUI

```
void printMenuHighLighted(int num, String menuItems[], int highlighted, bool itemStatus[])
{
  int currentItem = highlighted;
  display.clearDisplay();

  // Display handling ahead


  for (int i = 0; i < OPTNUM; i++)
  {
    int j = i;
    String itemName = menuItems[i];
    if (itemName.length() > 12)
    {
      itemName = itemName.substring(0, 12);
    }

    if (i > 5) j = i % 5;
    display.setTextSize(1);
    display.setCursor(0, j * 8);
    if (i != currentItem) display.setTextColor(BLACK, WHITE);
    else display.setTextColor(WHITE, BLACK); // 'inverted' text
    display.println(menuItems[i]);
    display.setTextSize(1);
    display.setCursor(76, j * 8);
    display.setTextColor(WHITE, BLACK);
    display.println(itemStatus[i]);
  }
  display.display();
  delay (100);
}
```

