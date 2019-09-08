# Setup Micropython on ESP32

> Tested only on **Ubuntu 18.04**

This project uses pipenv to install the `esptool` and `rshell` python libraries.

`esptool` is used to install the micropython firmware on to the ESP32.
More information at [https://github.com/espressif/esptool](https://github.com/espressif/esptool).

`rshell` is used to interact with the EPS32,
write some code directly on it and see its output.
More information at [https://github.com/dhylands/rshell](https://github.com/dhylands/rshell).

## Install

If you don't pipenv installed, you can do it with the following commands.

```bash
sudo apt install python3-pip python3-virtualenv
sudo pip install pipenv
```

Install the libraries listed inside Pipfile.

```bash
pipenv install
```

Run the installed libraries with pipenv by prepending `pipenv run` 
to the command as shown next.

```bash
pipenv run esptool.py --help
pipenv run rshell --help
```

## Flash

To be able to use micropython on the ESP32, the micropython firmware
needs to be flashed on to it. This only needs to be done once
and should be reversible by uploading a project from the Arduino IDE.

Download the micropython firmware from
[http://micropython.org/download#esp32](http://micropython.org/download#esp32)
and place it inside the same folder from where you want to execute the commands.

The current stable version is `esp32-20190529-v1.11.bin`.

In order use each library you need to tell it
which device it should work with by specifying the port.
For that you need to find out which tty devices under /dev
corresponds to the connected ESP32 with `ls /dev/tty*`.
Usually it should be `/dev/ttyUSB0` on Linux 
(maybe `/dev/tty.SLAB_USBtoUART` on OS X). 
Use it in the port part of the next command.

```bash
pipenv run esptool.py --chip esp32 --port /dev/ttyUSB0 erase_flash
pipenv run esptool.py --chip esp32 --port /dev/ttyUSB0 write_flash -z 0x1000 esp32-20190529-v1.11.bin
```

## Troubleshoot

If an exception saying `[Errno 13] Permission denied: '/dev/ttyUSB0'`
appears while executing the previous commands,
add the your current user to the dialout group.

```bash
sudo usermod -a -G dialout ${USER}
```

Logout and login to apply this change.

## Try out

Access the interactive console.

```bash
pipenv run rshell --port /dev/ttyUSB0 repl
```

Read a value from the temperature and magnetic field (hall effect) sensor.

```python
import esp32

esp32.raw_temperature()
esp32.hall_sensor()
```

Press <kbd>CTRL</kbd> + <kbd>X</kbd> to exit.
