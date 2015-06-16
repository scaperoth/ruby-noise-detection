ruby-noise-detection with Keen IO Integration
====================
Special thanks to [@mmornati](https://github.com/mmornati) for the base code for this library

Noise Detection Ruby Script. This code, instead of [@mmornati](https://github.com/mmornati)'s original intention of sending an email, will make a call 
out to a [Keen IO](http://keen.io) API to track when the noise happens. Will be used with my Dog Caller application

> *The Below README was created from both the [original noise detection repo](https://github.com/mmornati/ruby-noise-detection), [created by mmornati](https://github.com/mmornati), and from [keen.io](https://keen.io) on their [keen-gem repo](https://github.com/keenlabs/keen-gem).*

Installation
------------

#### Keen IO Setup   
Add to your Gemfile:

    gem 'keen'

or install from Rubygems:

    gem install keen

keen is tested with Ruby 1.8 and 1.9 on:

* MRI
* Rubinius
* jRuby (except for asynchronous methods - no TLS support for EM on jRuby)

#### Noise Detection Setup   
The noise dector ruby script uses two linux tool to record and analyse de
output: **sox** and **arecord**.
So you need to install these packages on your raspberrypi (or other linux
system):

```bash
sudo apt-get install sox alsa-utils
```

Then, in the current version, the script does not accept parameters to change
file location (log, tmp recording and pid file), so you need to allow the script
execution user (**pi** for example) the write access to these files and folders:

```
RECORD_FILENAME='/tmp/noise.wav'
LOG_FILE='/var/log/noise_detector.log'
PID_FILE='/etc/noised/noised.pid'
```

or you can edit the script changin these variables values with what you prefer
for your system.

KEEN IO Ruby Configuration
------------

Before making any API calls, you must supply keen-gem with a Project ID and one or more authentication keys.
(If you need a Keen IO account, [sign up here](https://keen.io/signup?s=gh-gem) - it's free.) 

Setting a write key is required for publishing events. Setting a read key is required for running queries. 
Setting a master key is required for performing deletes. You can find keys for all of your projects
on [keen.io](https://keen.io?s=gh-gem).

The recommended way to set keys is via the environment. The keys you can set are 
`KEEN_PROJECT_ID`, `KEEN_WRITE_KEY`, `KEEN_READ_KEY` and `KEEN_MASTER_KEY`.
You only need to specify the keys that correspond to the API calls you'll be performing. 
If you're using [foreman](http://ddollar.github.com/foreman/), add this to your `.env` file:

    KEEN_PROJECT_ID=aaaaaaaaaaaaaaa
    KEEN_MASTER_KEY=xxxxxxxxxxxxxxx
    KEEN_WRITE_KEY=yyyyyyyyyyyyyyy
    KEEN_READ_KEY=zzzzzzzzzzzzzzz

If not, make a script to export the variables into your shell or put it before the command you use to start your server.

When you deploy, make sure your production environment variables are set. For example,
set [config vars](https://devcenter.heroku.com/articles/config-vars) on Heroku. (We recommend this
environment-based approach because it keeps sensitive information out of the codebase. If you can't do this, see the alternatives below.)

Once your environment is properly configured, the `Keen` object is ready to go immediately.

Detect Audio Card
-----------------

```bash
pi@raspberrypi ~ $ noise_detection.rb -d
Detecting your soundcard...
 0 [ALSA           ]: BRCM bcm2835 ALSbcm2835 ALSA - bcm2835 ALSA
                      bcm2835 ALSA
 1 [U0x46d0x8d7    ]: USB-Audio - USB Device 0x46d:0x8d7
                      USB Device 0x46d:0x8d7 at usb-bcm2708_usb-1.2, full speed
```

Test Audio Card Record
----------------------
```bash
pi@raspberrypi ~ $ noise_detection.rb -t 1
Testing soundcard...
Samples read:             40000
Length (seconds):      5.000000
Scaled by:         2147483647.0
Maximum amplitude:     0.013245
Minimum amplitude:    -0.041565
Midline amplitude:    -0.014160
Mean    norm:          0.013523
Mean    amplitude:    -0.013466
RMS     amplitude:     0.014662
Maximum delta:         0.023560
Minimum delta:         0.000000
Mean    delta:         0.003664
RMS     delta:         0.004679
Rough   frequency:          406
Volume adjustment:       24.059
```

Starting Noise Detection with Alert
-----------------------------------
```bash
noise_detection.rb -m 1 -n 0.30 -e me@server.com -v
```
The script will be started in background

Terminating Noise Detection
---------------------------
```bash
noise_detection.rb -k
```
