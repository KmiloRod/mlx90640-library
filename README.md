# mlx90640-library
MLX90640 library functions for Odroid XU4

## Odroid XU4 Users

** EXPERIMENTAL **

This port uses the generic Linux I2C.
 Upon building, the mode is set with the I2C_MODE property, i.e. `make I2C_MODE=LINUX` or `make I2C_MODE=RPI`. The default is LINUX, without the need for the bcm2835 library or root access. (bcm2835 is used with Raspberry Pi; does not work with Odroid).

### Sensor connection to Odroid XU4
The MLX90640 sensor must be connected to the I2C_5 lines in the CONN11 header of the Odroid XU4. A level shifter from 1.8V to 3.3V must be used in order to connect with the sensor. The XU4 Shifter Shield from HardKernel is recommended for this application, since it uses the TXS-type translators from Texas Instruments. Logic level converters such us the ones from Adafruit use TXB-type translators, which are not designed for open-drain applications, don't work well for I2C communication.

### Generic Linux I2C Mode

Make sure the Linux I2C dev library is installed:

```text
sudo apt-get install libi2c-dev
```

To get the best out of your sensor you should modify `/boot/config.txt` and change your I2C baudrate.

The fastest rate recommended for compatibility with other sensors is 400kHz. This is compatible with SMBus devices:

```text
dtparam=i2c1_baudrate=400000
```

This will give you a framerate of - at most - 8FPS.

If you're just using the MLX90640 and, for example, the 1.12" OLED, you can safely use 1MHz:

```text
dtparam=i2c1_baudrate=1000000
```

This will give you a framerate of - at most - 32FPS.

Now build the MLX90640 library and examples in LINUX I2C mode:

```text
make clean
make I2C_MODE=LINUX
```

### Dependencies

libav for `video` example:

```text
sudo apt-get install libavutil-dev libavcodec-dev libavformat-dev
```

SDL2 for `sdlscale` example:

```text
sudo apt install libsdl2-dev
```

# Building

After installing the dependencies, you can build the library. Build-modes are:

* `make` or `make all`: build the library and all dependencies. Default is to use standard linux I2C-Drivers, specify Raspberry Pi driver with `make I2C_MODE=RPI`
* `make examples`: only build examples, see below
* `sudo make install`: install libraries and headers into `$PREFIX`, default is `/usr/local`

Afterwards you can run the examples or build the python binding, see readme in the subfolder.
If you built the examples or library using the native bcm2835 I2C-Driver, you need to run all applications and examples as root.
Hence, `sudo examples/<exampleame>` for one of the examples listed below, or without `sudo` when using the standard Linux driver.

# Examples
## fbuf

```
make examples/fbuf
sudo examples/fbuf
```

This example uses direct-to-framebuffer rendering and black-blue-green-yellow-red-purple-white false colouring.

If you gave issues with the output image, set "`IMAGE_SCALE`" to a smaller number.

## interp

```
make examples/interp
sudo examples/interp
```

This example uses direct-to-framebuffer rendering and black-blue-green-yellow-red-purple-white false colouring.

It also has 2x bicubic resize filter.

If you have issues with the output image, set "`IMAGE_SCALE`" to a smaller number.

## test

```
make examples/test
sudo examples/test
```

This example draws out to the console using ANSI colours and the full block char.

To see the actual temperature values, change "`FMT_STRING`" from the block char to the float format.

## step

```
make examples/step
sudo examples/step
```

Attempt to run in step by step mode (experimental)

## sdlscale

Displays the MLX90640 sensor full-screen using hardware acceleration in SDL2.

Hit Spacebar to change from aspect-ratio correct to full-screen-stretched modes.

Hit Escape to exit.

```
make examples/sdlscale
sudo examples/sdlscale
```

Requires SDL2 libraries:

```
sudo apt install libsdl2-dev
```

On Raspbian Lite you may wish to build SDL2 from source with X support disabled to avoid pulling in ~200MB of dependencies. Before configuring/compiling ensure you have `libudev-dev` installed for input support.
