rp-gpio.js
==========


Control Raspberry Pi GPIO pins with node.js

## Setup
See this guide on how to get [node.js running on Raspberry Pi](http://elsmorian.com/post/23474168753/node-js-on-raspberry-pi).

## Usage
After loading the module, initialise a pin by calling `setup`. Each GPIO pin can be set as either an input or output, which lets you read and write to it respectively. The 'channel' must be specified, to indicate which pin to use. There are two different ways to reference a channel; either using the Raspberry Pi or the BCM naming schema (sadly, neither of which match the physical pins!). This module supports both schemas, with Raspberry Pi being the default. Please see [this page](http://elinux.org/RPi_Low-level_peripherals) for more details.

All functions within this module are asynchronous, so where necessary - for example in reading the value of a pin - a callback must be provided.

### Query the value of a pin
```js
var gpio = require('./rp-gpio');

gpio.setup(7, gpio.DIR_IN);
gpio.read(7, function(value) {
    console.log('The value is ' + value);
});
```

### Listen for changes on a pin
The GPIO module inherits from `EventEmitter` so any of the [EventEmitter functions](http://nodejs.org/api/events.html) can be used. The example below shows how to listen for a change in value to a channel.
```js
var gpio = require('./rp-gpio');

gpio.setup(7, gpio.DIR_IN);
gpio.on('change', function(channel, value) {
	console.log('Channel ' + channel + ' value is now ' + value);
});
```

### Voltage cycling a pin
This example shows how to set up a channel for output mode. After it is set up, it executes a callback which in turn calls another, causing the voltage to alternate up and down three times.
```js
var gpio = require('./rp-gpio');

var pin   = 7,
    delay = 2000,
    count = 0,
    max   = 3;

gpio.setup(pin, gpio.DIR_OUT, on);

function on() {
    if (count >= max) {
        process.exit(0);
        return;
    }

    setTimeout(function() {
        gpio.write(pin, 1, off);
        count += 1;
    }, delay);
}

function off() {
    setTimeout(function() {
        gpio.write(pin, 0, on);
    }, delay);
}
```

### Querying a pin in BCM mode
```js
var gpio = require('./rp-gpio');

gpio.setMode(gpio.MODE_BCM);
gpio.setup(4, gpio.DIR_IN);

gpio.read(4444, function(value) {
    console.log('The value is ' + value);
});
```
