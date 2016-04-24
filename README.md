# lightwait

**A lightweight wait light!**

Use lightwait to get informed when your longrunning task has been finished.  
You can switch to another window or even leave the desk and you'll get notified when your task is completed, without the need to take a look to the monitor!

## lightwait stack

![The lightwait stack](/home/bgebhard/iotlab/lightwait/lightwait-doc.git/lightwait-stack.png  "The lightwait stack")

*ligthwait* consists of a three layered stack. Each stack can be implemented by a different program which then are connected via protocols.  
One program also may implement more than one layer, removing the need to a protocol based communication. This also makes the program and its usage less flexibel.

### Trigger

The lightwait-trigger contains the logic when to set certain colors or blinkings. It calls the connected transmitter to deliver the change to the presenter.

#### lightwait-go-shell

TODO

#### lightwait-shell

This project contains one bash file for linux/unix/cygwin machines and a batch file for windows machines. They lack some functionality compared to lightwait-go-shell

##### Setup

Use the `lw.sh` script for Linux, Mac OS and Cygwin and use the `lw.bat`. In both scripts, the variable `LW_SERIAL` in line 3 can be used to select the program for the serial communication.

Make the script available on the command line:

* Link it to a bin folder which is in the PATH variable
    * `ln -s /path/to/lw.sh /usr/local/bin/lw`
    * `ln -s /path/to/lw.sh ~/bin/lw`

**TODO: linking the transmitter**

##### Usage

`lw <task> <task parameters>`
e.g.:

    lw mvn clean install
    lw java -jar calculation.jar
    lw mkfs.vfat /dev/sdb1
    lw rsync --numeric-ids -avze ssh /home/user user@example.com:/backups 

* It turns blue, when the task starts
* It turns green, once the task is succesfully finished
* It turns red if the task returns an error code other than 0
* Press Enter to complete the lw task. The LED turns off


### Transmitter

A lightwait-transmitter receives are color code (which may include a blink code) and sends this information to the presenter. As the presenter may be a <u>different hardware device</u>, the transmitter establishes and handles the communication stream to its presenter. A transmitter may support more than one presenter and therefore more than one communication stream.

Use a transmitter of the programming language of your choice as base of your own trigger.

Available transmitters and the presenters they can connect to:

* lightwait-go
	* lightwait-arduino
* lightwait-python
	* lightwait-arduino

### Presenter

A lightwait presenter displays the state set by the trigger using one RGB LED. Supporting multiple LEDs is planned.

A presenter may be a program running locally on the same machine as the trigger and transmitter, or it may be a different hardware device.
So the lightwait presenter is in fact one of the following:
* just a program, which can be started locally
* A program which must be running on the additional hardware device, like an arduino or a raspberry pi
* An existing (third party) hardware device, which is able to display a color (like the fast-feedback lights also called "jambel"). So no presenter code exists in this case

Available presenters:

* lightwait-arduino

Planned presenters:

* lightwait-jambel
* lightwait-indicator
* lightwait-gtk

Also possible (not not actually planned):
* lightwait-android
* lightwait-ios

## Inter-layer communication protocols

### Trigger -> Transmitter

The way the trigger communicates with the 

### Transmitter -> Presenter

Every Presenter may offer different payload formats, especially when using third party LED devices as lightwait-presenters.

Anyways it exists a <u>recommendation for implementing a lightwait-presenter</u>, which simplifies the interoperability between different transmitters and presenters. It's therefore suggested, that a program which operates as lightwait-transmitter, should implement this communication protocol, even when implementing others in addtion.

This communication protocol is called **lightwait-tpp (lightwait-transmitter-presenter-protocol)**.

#### non-blinking

Schema: `<red>:<green>:<blue>` where every color value is between 0 and 255.
Some examples:

| Color | Value |
|-|-|
| Black | `0:0:0`
| Red | `255:0:0`
| White | `255:255:255`
| Magenta | `255:0:255`

#### blinking

Schema: `b<red>:<green>:<blue>` where every color value is between 0 and 255


Some examples:

| Color | Value |
|-|-|
| Black | `b0:0:0`
| Red | `b255:0:0`
| White | `b255:255:255`
| Magenta | `b255:0:255`

#### Multiple blinking colors (proposal)

Schema: `b<red>:<green>:<blue>|<red>:<green>:<blue>|...`

## Acknowledgements

* Thanks to the [serial-port-json-server](https://github.com/johnlauer/serial-port-json-server) project, for the code for determine the arduino port for the go version!
* Thanks to Max for printable case design!
* Thanks to moepi for suggesting to implement it in go and made me to want to learn it! :)

## FAQ
* What's the License of that all?
* Do you accept pull requests?
     * Sure! Your contribution is appreciated!
* Why do you use bash/batch scripts for calling the program and don't call them directly from lw?
    * This is a first step. Sure, it would be smarter to call them directly from the go/python/whatever program. I'll change that. Go ahead and change it by yourself, if you need it fast.
* Why are there so many programming languages in the feature matrix (most of them not implemented!)?
* Why do you only support Arduino? I have an ESP8266/Espruino/Oak/...!
    * Exactly. Would be great! The arduino is a first step. Feel free to contribute!
* Why do you only support tasks that terminate? I'm calling `mvn jetty:run` and want to be notified when it's ready!
    * Great idea! I thought of this, too! :P As it might be a bit more work to do, it's postponed. Feel free to contribute!
* An Arduino Uno is really big. Wouldn't be an Arduino Micro enough?
    * True. For a proof of concept, we implemented it for an uno as we already had one laying around. Once we have the time create a circuit board and a case for a smaller device, we'll do it!