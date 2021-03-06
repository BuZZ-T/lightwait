# lightwait-stack

**A lightweight wait light!**

***NOTE** This is the description of the lightwait-stack, the architecture of the lightwait eco-system. For the product "lightwait", see [lightwait-go-shell](https://github.com/BuZZ-T/lightwait-go-shell) , the reference implemenation of a [lightwait-trigger](#trigger), containing information how to connect to [lightwait-arduino](https://github.com/BuZZ-T/lightwait-arduino), the reference implementation of a lightwait-presenter!* (or take a look at the [CLI-status notifier](#cli-status-notifier) section)

![The lightwait stack](https://raw.githubusercontent.com/BuZZ-T/lightwait/master/lightwait-stack.png  "The lightwait stack")

#### Splitted in a three layer stack

*lightwait* consists of a three layered stack. Each stack can be implemented by a different program which then connects to its previous or next layer via protocols.  
One program also may implement more than one layer, removing the need to a protocol based communication. This also makes the program and its usage less flexibel.

The three layers are:

* [Trigger](#trigger)
* [Transmitter](#transmitter)
* [Presenter](#presenter)

#### CLI based (or integrated as library)

A unit which provides a layer in the lightwait-stack might be:

* an executable
* a library for the next layer
* part of a multilayer program
The lightwait-trigger contains the complete logic when to set certain colors or blinkings. Therefore, it also contains the meaning of each color and/or blinking.

#### Pure push based

To establish a protocoal which is as simple as possible, the trigger is the only unit initiating updates, there is no way to pull the latest status.

This has the following consequences:

* The trigger can be implemented stateless
* The presenter most likely can be implemented stateless besides displaying the latest status as color
* If the communication based on a not-reliable protocol (like e.g. UDP), updates might be lost
* If the presenter is switched off and on again, it displays no status until the next update is sent

#### Pure color based

TODO

## Table of contents
* [Trigger](#trigger)
* [Transmitter](#transmitter)
* [Presenter](#presenter)
* [Inter-layer communication protocols](#inter-layer-communication-protocols)
* [Known colors](#known-colors)
* [All related Repositories](#all-related-repositories)
* [Acknowledgements](#acknowledgements)
* [License of this documentation/specification](#license-of-this-documentationspecification)
* [FAQ](#faq)

## Trigger

A trigger calls the connected transmitter to deliver the change to the presenter. It contains all the logic regarding what color corresponds to which status and which colors (and therefore) statuses are available.

### CLI-status notifier

This type of trigger was the reason to initially create the lightwait project. It tries to provide a support for developers, who run a long-running task in the shell (like `npm start`, `gradle build` or `mvn clean install`) and wants to do other work while the task is executed. Besides that, they want to be informed, when the task it completed (or in a stable state, when it's a non-terminating task like `npm start`).

For that, a CLI-wrapper named `lw` is created, which calls the real task. So just launch
```bash
lw mvn clean install
lw npm start
lw gradle build
```
instead, and the status will be passed to the connected lightwait-transmitter, which will forward this to a lightwait-presenter. For more details, take a look at the documentation of the trigger projects.

Currently there are two triggers which implements this logic:

| Trigger | programming language | description |
|-|-|-|
| [lightwait-go-shell](https://github.com/BuZZ-T/lightwait-go-shell) | [go](https://golang.org/) | This project may be compiled to a binary CLI for multiple operation systems and multiple architectures. It may call a lightwait-transmitter by a sub-process, or include lightwait-go as library to already contain the transmitter.
| [lightwait-shell](https://github.com/BuZZ-T/lightwait-shell) | bash/batch | This project contains one bash file for linux/unix/cygwin machines and a batch file for windows machines. They are able to call a lightwait-transmitter as a sub-process. They lack some functionality compared to lightwait-go-shell.

## Transmitter

A lightwait-transmitter receives are color code (which may include a blink code) and sends this information to the presenter. As the presenter may be a <u>different hardware device</u>, the transmitter establishes and handles the communication stream to its presenter. A transmitter may support more than one presenter and therefore more than one communication stream.  
The default port for TCP and UDP based connections is: 3030

Use a transmitter of the programming language of your choice as base of your own trigger.

Available transmitters and the presenters they can connect to:

| Transmitter | programming language | description
|-|-|-|
| [lightwait-go](https://github.com/BuZZ-T/lightwait-go) | [go](https://golang.org/) | Is able to connect to an arduino via serial communication
| [lightwait-python](https://github.com/BuZZ-T/lightwait-python) | [python](https://www.python.org/) | Is able to connect to an arduino via serial communication
| [lightwait-python-tcp-udp](https://github.com/BuZZ-T/lightwait-python-tcp-udp) | [python](https://www.python.org/) | Is able to communicate to presenters via TCP or UDP
| [lightwait-python-multiplexer](https://github.com/BuZZ-T/lightwait-python-multiplexer) | [python](https://www.python.org/) | Is not a real transmitter, but an entity placed before, to split every received signal to multiple transmitters at the same time
| [lightwait-python-jambel](https://github.com/BuZZ-T/lightwait-python-jambel) | [python](https://www.python.org/) | Is able to communicate with a jambel device, using [python-jambel](https://github.com/jambit/python-jambel)

## Presenter

A lightwait presenter displays the state set by the trigger using one RGB LED. Supporting multiple LEDs is planned.

A presenter may be a program running locally on the same machine as the trigger and transmitter, or it may be a different hardware device.
So the lightwait presenter is in fact one of the following:
* just a program, which can be started locally
* A program which must be running on the additional hardware device, like an arduino or a raspberry pi
* An existing (third party) hardware device, which is able to display a color (like the fast-feedback lights also called "jambel"). So no presenter code exists in this case.

Presenters should be independent of a lightwait-trigger logic, although they might be optimized for specific usecases.

Available presenters:

| Presenter | programming language | description
|-|-|-
| [lightwait-arduino](https://github.com/BuZZ-T/lightwait-arduino) | [C](https://arduino.cc) | Code that runs on an arduino to display the received color with a connected RGB LED
| [lightwait-gnome-extension](https://github.com/BuZZ-T/lightwait-gnome-extension) | [JavaScript for gjs](https://wiki.gnome.org/Projects/GnomeShell/Extensions) | An extention for the gnome 3 Desktop Environment placing a received color in the gnome panel
| [lightwait-python-gtk](https://github.com/BuZZ-T/lightwait-python-gtk) | [python](https://www.python.org/) | A GTK3 application displaying the received color in a gtk window
| (jambel) | --- | A hardware device without code written in this project. Can be used to display triafficlight-like colors and can be controled using [python-jambel](https://github.com/jambit/python-jambel)
[lightwait-js-web-extension](https://github.com/BuZZ-T/lightwait-js-web-extension) | JavaScript | A Web-extension supporting Firefox and Chrome displaying the received color in the toolbar

Also possible (not not actually planned):
* lightwait-android
* lightwait-ios

## Inter-layer communication protocols

### Trigger -> Transmitter

*This is also called lightwait-tt communication protocol*

The way the trigger communicates with the transmitter. Based on the used components, the transmitter should be separate from the trigger or may be combined with the trigger to one program.
A separation may be useful, when the presenter should be an external hardware device (like an arduino, when using [lightwait-arduino](https://github.com/BuZZ-T/lightwait-arduino) or raspberry pi. An addition to the ISO layer 7 protocol, they also need lower-layer communication. To keep this out of the trigger, which should only contain the business logic of the status (i.e. color) change, an additional transmitter should be used.

#### library-based

If the transmitter is in fact a library, which can be imported and used by a trigger to include the functionality in a combined program, take a look at the readme of this library to see how to use it.

Currently available transmitters which can be used as a library are:

* [lightwait-go](https://github.com/BuZZ-T/lightwait-go)

#### CLI parameter-based

lightwait-transmitters which are CLI based, are scripts or binary programs, which accept the color/blinking code as parameter.

The first parameter should consist the complete color/blinking code. Being of one of the following formats

* A known color, e.g. "yellow"
* A 6-digit hex-code, e.g. "FF0034"
* A 3-digit hex-code, e.g. "F0C" (means "FF00CC" in the 6-digit format, so double every digit)
* One of the three above, prefixed with "b-", e.g. "b-red", "b-F0C" or "b-FF4423"
* (A proposal for a multi-color-blinking status)

In every case, the color-code should be interpreted case-insensitive, although some letters are lowercase and some uppercase, for readability purposes.

Currently available CLI parameter-based transmitters:

* [lightwait-go](https://github.com/BuZZ-T/lightwait-go)
* [lightwait-python](https://github.com/BuZZ-T/lightwait-python)
* [lightwait-python-jambel](https://github.com/BuZZ-T/lightwait-python-jambel)
* [lightwait-python-tcp-udp](https://github.com/BuZZ-T/lightwait-python-tcp-udp)
* [lightwait-python-multiplexer](https://github.com/BuZZ-T/lightwait-python-multiplexer)

<a name="lightwait-tp"></a>
### Transmitter -> Presenter

*This is also called "lightwait-tp communication protocol"*

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

Schema: `b<red>:<green>:<blue>` where every color value is between 0 and 255.

Some examples:

| Color | Value |
|-|-|
| Black | `b0:0:0`
| Red | `b255:0:0`
| White | `b255:255:255`
| Magenta | `b255:0:255`

#### Multiple blinking colors

Schema: `b<red>:<green>:<blue>|<red>:<green>:<blue>|...`

Some examples:

| Color | Value |
|-|-|
| Red and green | `b255:0:0|0:255:0`
| Red-yellow-green | `b255:0:0|255:255:0|0:255:0`

#### Multiple blinking with custom delays

This is a non-standardized idea and currently not part of the lightwait-stack.

## Known colors

Currently, these are all known colors, which should be available for the [lightwait-tt](#transmitter---presenter) communication.

| Name | Hex-code | lightwait-tp | comment
|-|-|-|-
| red | #FF0000 | 255:0:0
| green | #00FF00 | 0:255:0
| blue | #0000FF | 0:0:255
| yellow | #FFFF00 | 255:255:0
| magenta | #FF00FF | 255:0:255
| cyan | #00FFFF | 0:255:255
| white | #FFFFFF | 255:255:255
| black | #000000 | 0:0:0
| off | #000000 | 0:0:0 | might also close the connection of the transmitter, check the description

## All related repositories

| Name | type | programming language | description
|-|-|-|-
| [lightwait-go-shell](https://github.com/BuZZ-T/lightwait-go-shell) | [Trigger](#trigger) | [go](https://golang.org/)
| [lightwait-shell](https://github.com/BuZZ-T/lightwait-shell) | [Trigger](#trigger) | bash/batch
| [lightwait-go](https://github.com/BuZZ-T/lightwait-go) | [Transmitter](#transmitter) | [go](https://golang.org/)
| [lightwait-python](https://github.com/BuZZ-T/lightwait-python) | [Transmitter](#transmitter) | [python](https://www.python.org/)
| [lightwait-python-tcp-udp](https://github.com/BuZZ-T/lightwait-python-tcp-udp) | [Transmitter](#transmitter) | [python](https://www.python.org/)
| [lightwait-python-multiplexer](https://github.com/BuZZ-T/lightwait-python-multiplexer) | [(Meta-) Transmitter](#transmitter) | [python](https://www.python.org/)
| [lightwait-python-jambel](https://github.com/BuZZ-T/lightwait-python-jambel) | [Transmitter](#presenter) | [python](https://www.python.org/) | Using [python-jambel](https://github.com/jambit/python-jambel) to connect to fast-feedback lights
| [lightwait-arduino](https://github.com/BuZZ-T/lightwait-arduino) | [Presenter](#presenter) | [C](https://www.arduino.cc/)
| [lightwait-gnome-extension](https://github.com/BuZZ-T/lightwait-gnome-extension) | [Presenter](#presenter) | [JavaScript for gjs](https://wiki.gnome.org/Projects/GnomeShell/Extensions)
| [lightwait-python-gtk](https://github.com/BuZZ-T/lightwait-python-gtk) | [Presenter](#presenter) | [python](https://www.python.org/)
[lightwait-js-web-extension](https://github.com/BuZZ-T/lightwait-js-web-extension) | [Presenter](#presenter) | JavaScript | Currently only supporting Firefox and Chrome


## Acknowledgements

* Thanks to the [serial-port-json-server](https://github.com/johnlauer/serial-port-json-server) project, for the code for determine the arduino port for the go version!
* Thanks to the [system-monitor](https://github.com/paradoxxxzero/gnome-shell-system-monitor-applet) project, helping me to understand how to write gnome-extensions, by looking at the source!
* Thanks to Max for a printable arduino case design and the catchy slogan! :)
* Thanks to moepi for suggesting to implement it in go and made me to want to learn it! :)

## License of this documentation/specification

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
