# Home Assistant Add-on: Python executor add-on

This addon is mainly for development purpose. It will install, if needed,
the given requirements with pip and then launch the python script.

## Options

### code

Python source code

### requirements (optional)

List of requirements to install with pip

### preexec (optional)

Path to the python source file.

## Usage

```yaml
code: |
  print('Hello, world!')
```

## Advanced usage (luma-oled + ssd1306)

```yaml
code: |
  #!/usr/bin/env python
  from random import randint, gauss
  from luma.core.interface.serial import i2c
  from luma.oled.device import ssd1306
  from luma.core.render import canvas
  from luma.core.sprite_system import framerate_regulator

  wrd_rgb = [
      (154, 173, 154),
      (0, 255, 0),
      (0, 235, 0),
      (0, 220, 0),
      (0, 185, 0),
      (0, 165, 0),
      (0, 128, 0),
      (0, 0, 0),
      (154, 173, 154),
      (0, 145, 0),
      (0, 125, 0),
      (0, 100, 0),
      (0, 80, 0),
      (0, 60, 0),
      (0, 40, 0),
      (0, 0, 0)
  ]
  clock = 0
  blue_pilled_population = []

  serial = i2c(port=1)
  device = ssd1306(serial)

  max_population = device.width * 8
  regulator = framerate_regulator(fps=10)

  while True:
      clock += 1
      with regulator:
          with canvas(device, dither=True) as draw:
              for person in blue_pilled_population:
                  x, y, speed = person
                  for rgb in wrd_rgb:
                      if 0 <= y < device.height:
                          draw.point((x, y), fill=rgb)
                      y -= 1
                  person[1] += speed
      if clock % 5 == 0 or clock % 3 == 0:
          blue_pilled_population.append([randint(0, device.width), 0, gauss(1.2, 0.6)])
      while len(blue_pilled_population) > max_population:
          blue_pilled_population.pop(0)
requirements:
  - luma.oled>=3.8.1
preexec: apk add -q --no-cache build-base linux-headers zlib-dev jpeg-dev
```
