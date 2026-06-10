## SunFounder PiCar
SunFounder PiCar

Quick Links:

 * [About PiCar](#about_this_repo)
 * [Update](#update)
 * [About SunFounder](#about_sunfounder)
 * [License](#license)
 * [Contact us](#contact_us)

<a id="about_this_{thing}"></a>
### About PiCar:
This is a basic control module for [PiCar V](https://github.com/sunfounder/SunFounder_Smart_Video_Car_Kit_V2.0_for_Raspberry_Pi) and [PiCar S](https://github.com/sunfounder/SunFounder_Smart_Sensor_Car_Kitfor_Raspberry_Pi)

<a id="update"></a>
### Update:
2016-11-16:
 - Change name to PiCar
2016-11-04:
 - New Release

----------------------------------------------
<a id="about_sunfounder"></a>
### About SunFounder
SunFounder is a technology company focused on Raspberry Pi and Arduino open source community development. Committed to the promotion of open source culture, we strives to bring the fun of electronics making to people all around the world and enable everyone to be a maker. Our products include learning kits, development boards, robots, sensor modules and development tools. In addition to high quality products, SunFounder also offers video tutorials to help you make your own project. If you have interest in open source or making something cool, welcome to join us!

----------------------------------------------
<a id="license"></a>
### License
This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 2 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, write to the Free Software Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

SunFounder PiCar comes with ABSOLUTELY NO WARRANTY; for details run ./show w. This is free software, and you are welcome to redistribute it under certain conditions; run ./show c for details.

SunFounder, Inc., hereby disclaims all copyright interest in the program 'SunFounder PiCar' (which makes passes at compilers).

Mike Huang, 21 August 2015

Mike Huang, Chief Executive Officer

Email: service@sunfounder.com, support@sunfounder.com

----------------------------------------------
<a id="contact_us"></a>
### Contact us:
website:
	www.sunfounder.com

E-mail:
	service@sunfounder.com, support@sunfounder.com

----------------------------------------------
<a id=troubleshooting></a>
## Troubleshooting

### Motor and servo control not working after OS/kernel upgrade

**Symptom:**
- Motors and servos do not respond
- `picar servo-install` prints *Servos now are set to 90 degree.* but nothing actually moves
- Running `python3 -c "import picar; picar.setup()"` shows:

```
[Errno 5] Input/output error
I2C bus number is: 1
Device is missing.
```

**Root cause:**

The PCA9685 PWM chip on this board has address pin **A1 tied high**, so it responds at I2C address **`0x42`** instead of the chip factory default `0x40`. Verify with:

```bash
sudo apt-get install -y i2c-tools
sudo i2cdetect -y 1
```

You should see a device at `42`, not `40`.

When `install_dependencies.sh` is re-run after a kernel or OS upgrade, it re-clones and reinstalls the picar module from GitHub, resetting the address back to the hardcoded default `0x40`. All I2C communication then silently fails.

> **Note:** The misleading success message from `picar servo-install` is printed unconditionally at the end of the function — I2C errors are swallowed silently inside `_write_byte_data`.

**Fix:**

The default address in `PCA9685.py` and `Servo.py` has been changed to `0x42` in this fork. After any reinstall, use this repo (not the upstream SunFounder original):

```bash
cd ~/git/SunFounder_PiCar
sudo python3 setup.py install
```

Then verify:
```bash
picar servo-install   # servos should physically move to 90°
```
