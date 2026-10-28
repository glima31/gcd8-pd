# GCD-8 for PureData

<p align="center">
    <img src="images/gui.png" alt="GCD-8 patch screenshot" width="700">
</p>

GCD-8 is an 8-track euclidean sample player with dynamics and panning controls, making it possible to generate expressive rhythmic patterns with an interesting stereo image. At the heart of this instrument lies the sampler's velocity response section, which makes it possible to determine how different velocities affect the sound. 

The idea behind this instrument arose out of necessity. As a musician with a major interest in Industrial music aswell as in compositional approaches inspired by Musique Concrète, after collecting and cataloguing a vast amount of percussive samples, i found myself in need of sequencing them while also avoid making it all sound too "machine-like". After some initial experiments in VCV Rack that ended up validating my own "velocity response" implementation, i decided ot encapsulate all of it in PureData. 

Once i was happy with my Pd implementation of my own sampler with velcoity response, the obvious next step was to expand it with some rhytmic pattern generation capabilities. So this is where GCD-8 was born.

Of course, the main GCD-8's feature is its custom velocity response alongisde the dynamics controls of the sequencer. But this doesn't mean that the main goal behind it is to create "expressive" rhythmic patterns. As a matter of fact, GCD-8 is capable of generating really interesting robotic/machine-like rhythms, for those who that aesthetically pleasing. This can be seen in my [808 Electro Madness demo video].  

## Table of Contents
- [Demos](#demos)
- [Features](#features)
- [Getting Started](#getting-started)
    - [Requirements](#requirements)
    - [Quick Start](#quick-start)
    - [Using GCD-8 in your own patches](#using-gcd-8-in-your-own-patches)
    - [Using GCD-8 in your DAW](#using-gcd-8-in-your-daw)
- [Usage Guide](#usage-guide)
    - [Global Controls](#global-controls)
    - [Selecting Tracks for Editing](#selecting-tracks-for-editing)
    - [Loading Samples](#loading-samples)
    - [Programming the Base Pattern](#programming-the-base-pattern)
    - [Dynamics](#dynamics)
    - [Tuning the Sampler's Velocity Response](#tuning-the-samplers-velocity-response)
    - [Panning](#panning)
    - [Muting Tracks](#muting-tracks)
    - [Inlets](#inlets)
    - [Outlets](#outlets)
- [License](#license)

## Demos
### Video 1
Link
Description

### Video 2
Link 
Description

### Video 3
Link
Description

## Features
- Custom **velocity response** implementation with controls to shape how velocity affects **amplitude**, **tone**, **start position**, **attack** and **decay**
- AHD amplitude envelope with velocity-sensitive attack and decay stages and user-defined HOLD stage length (with the possibility of turning the AHD envelope into AD)
- **Euclidean rhythm generator** with controls for **step rate**, **length**, **hits** and **shift/rotation**
- **Random velocity generator** (jitter) with controls for **depth** and **ceiling**
- **Euclidean accent generator** with controls for accent **quantity**, **intensity** and **shift**
- **Euclidean panner** with controls for **hits**, **spread** and **side** (L/R)  
- Start and End position trimmers to crop the loaded samples active regions
- Invididual track level control
- `run` and `reset` methods for external transport actions
- Mute tracks externally with the `mute $TRACK_NR` method
- Ability to set the clock's BPM externally 
- Main mix stereo outlets
- Indvidual outlets for each track stereo output pairs
- Track #7 and #8 euclidean sequencer direct outlets
- Full state persistence (arrays, knobs, etc.)


## Getting Started

### Requirements

GCD-8 depends on the [ELSE](https://github.com/porres/pd-else) and [cyclone](https://github.com/porres/pd-cyclone) libraries.

**Plugdata users:** nothing to install. Plugdata already includes both.

**Pure Data users:** install **ELSE v1.0-rc13** and **cyclone** via *Help → Find Externals*, then add both folders to *Preferences → Path*.

> [!WARNING]
> Use ELSE **v1.0-rc13**, not v1.0-rc14. GCD-8 was developed in Plugdata, which ships rc13 at the time of writing. rc14 introduced an undocumented breaking change to the `[button ]` object, which GCD-8 uses extensively. Stick with rc13 until a newer ELSE version is released.

### Quick start

Open `gcd8.quickstart.pd`. It has `[gcd8~]` already wired to the audio output, with comments explaining each outlet.

### Using GCD-8 in your own patches

Add the `abstractions/` folder to Pure Data's or Plugdata's search paths. You can then use the full instrument (`[gcd8~ ] `) in any patch, as well as its single-track sampler (`[gcd8.sampler~ ]`) and sequencer (`[gcd8.seq ]`) abstractions.

### Using GCD-8 in your DAW

Open `gcd8.daw.pd` in a Plugdata DAW plugin instance. Each track gets its own stereo output, and GCD-8 follows the DAW's tempo and transport.

> [!WARNING]
> Tested in Reaper only. Clock sync works reliably while the Plugdata window is closed or in the background, but interacting with it during playback or recording can make GCD-8 drift out of sync. Avoid interacting with the plugin window mid-take. This will be addressed in a future version.

## Usage Guide

### Configuring the Master Clock
![master clock section screenshot](images/master_clock.png)


The **RATE** knob controls the clock speed in BPM when the clock is set to internal mode (**INT.**).

If the clock is set to external mode (**EXT.**), the clock rate is set by the second inlet of the abstraction, so the **RATE** knob is effectively ignored.

>[!TIP]
>The master clock implementation is based on `[else/clock ]`, which makes it very easy to create slave clocks that are guaranteed to be synced to the master. For this reason, on the right side of the BPM display, the clock ID is shown. This makes it possible to create other clocks in the same patch as gcd-8 that are synced to GCD-8's master clock by instanting them with the `-s` flag followed by the clock ID: 
>```
>[else/clock -s 1003-clk]
>```


### Controlling the Sequencer State
![transport section screenshot](images/transport.png)

To start advancing the internal euclidean sequencers, press the **RUN** button. The button background color wlil turn to red while the sequencers are running. 

To stop the sequencers, press the **RUN** button again. 

>[!NOTE]
>It is also possible to start/stop the sequencer using the first inlet: `1` to start, `0` to stop 

The **RESET** button resets all internal sequencers back to step 0. This is particularly useful for multi-track recording in separate takes inside a DAW while guaranteeing that the new take starts 

>[!NOTE]
>**The internal sequencers do not automatically reset back to step 0** when the sequencer is started or stopped. The **RESET** button is particularly useful when you want to guarantee that all the sequences start from the beginning (for example, when layering tracks in separate recording takes inside a DAW), 


### Adjusting the Main Mix Output Level
![main output section screenshot](images/main_output.png)

The **LEVEL** knob acts as a level trimmer for the main mix output (coming out of inlets 0 and 1). Thishis doesn't impact the levels coming out of the individual track outlets.


### Selecting Tracks for Editing
![track strips screenshot](images/track_strips.png)

By default, the track editor displays the sampler and sequencer controls for track #1. To edit another track, **click on its respective numbered button**. 

The currently selected track's strip has its border and background conveniently highlighted. 


### Loading and Auditioning Samples
![sampler section screenshot](images/loading_samples.png)

On the right side of the sampler's waveform display (the green graph), there are 4 buttons:
- **LOAD**: load an audio file into the currently selected track's sampler. 
- **TRIGGER**: play the sample at full velocity
- **STOP**: stop playback (useful for longer samples)
- **CLEAR**: clear the loaded sample

The **START POSITION** and **END POSITION** sliders lets you trim the sample's active region. 

The **LEVEL** knob acts as a level trimmer for the selected track. By default, it is set to max. (unity). 


### Programming the Base Pattern
![base pattern section screenshot](images/base_pattern.png)

Here we say that this section controls the track's main euclidean sequencer. The **LENGTH**, **HITS** and **SHIFT** knobs are the usual euclidean parameters (do we explain ?). The **STEP RATE** radio button selects between 4 step resolutions (resolutions? rates?): 1/16, bl abl a

### Dynamics
![dynamics section screenshot](images/dynamics.png)

- Brief intro to this section (this is responsible for making our rhythmic pattern more expressive. With the default settings, this is essentially bypassed, so all hits are played with max velocity by defaut. )

- The jitter controls makes each hit be played at a random velcoity with its maximum value determined by the **CEILING** knob. The greater the **DEPTH** knob, the greater the range of velocity values while jittering. 

- The accent controls determine which hits from the base pattern are accented. Internally, this uses another euclidean sequencer that is slaved(?) to the main one: 
    - Instead of advancing on every step, it only advances whenever there is a HIT on the base pattern
    - The length of the euclidean sequence is always the same as the number of base pattern HITS. 
    - So , while the BASE PATTERN sequencer answers the question "From this sequence of X steps where Y are hits, which ones are hits?", this sequencer answers the question "From these hits, which ones should be accented?"
- The **QUANTITY** knob determines hor many HITS (from the base pattern) are accented. The **INTENSITY** knob determines how much too boost the velocity when a hit is accented. 
- The SHIFT knob shifts the accent sequence by 1 step. 
- Maybe we should say that the JITTER and ACCENTS sub-sections work closely together. For example: with a high INTENSITY and a low jitter CEILING, the accents are very pronounced. If jittering DEPTH is set to 0 (so no jittering), this essentially means that there are no accents aswell (because our accents essentially BOOST the generated velocities while ittering - confim this please)
- We should also note that in order for the accents to (???), we should decrease value of jitter CEILING (research this so you understan dwhat im talking about)


### Tuning the Sampler's Velocity Response
![velocity response section screenshot](images/velocity_response.png)

- The VELCOITY RESPONSE section under the sampler allows for finetuning of how different velocities affect different sonic characteristics of the sample(?). 
- All knobs under this section **except the envelope HOLD knob** are "depths". This means that on minimum position, depth = 0, so there is no velocity response for that parameter/characteristic(?). As we increase these knobs, the greater impact is felt in thei respective what(?) for different(?) velocities 
- The **AMP.** knob determines how velocity affects the amplitude level of the audio playback. If set to maximum -> exponential? Velocity is decreased by V² ? (is this true?)
- The **TONE** determines the minimum low-pass filter cutoff frequency. The greater this value, the duller lower velocities sound in relation tohigher velocities. 
- The **START** knob determines how much to skip bl abl a
- The **ATTACK** knob...
- The **DECAY** knob..
- The **HOLD** knob : this is a special case . It is not directly affected by velocity. It determines the length of the HOLD section of the AHD amplitude envelope. At minimum position: AD envelope. Maximum position: AHD envelope where the HOLD stage can take up to x%?? (confirm this) 

A few note sthat we somehow maybe should include: 
- The way we finetune the vleocity repsonse depends entirely on the loaded sample's characteristics. Some samples benefit from certain controls , while others bl abla. 
- Maybe examples like: for very short and dry samples (e.g., a wooden stick hit?), **AMP.** and maybe a bit of **TONE** is usually enough. For samples with a longer ringing tail (??), the START knob in conjunction with the TONE and DECAY are very useful, particularly if the sample has a very strong transient (on lower velocities, it would sound very weird if we left the transient intact, so we can lverage the **START** knob to skip some parts of.. and maybe apply a bit of **ATTACK** to smoothen out the transient on lower velocities)
- Maybe we can also note that tweaking the HOLD knob while adjusting the START and END sliders above the VELCOITY RESPONSE section can used isolate a certain part of a longer audio samples (not one shot) and make it percussive

### Panning
![panning section screenshot](images/panning.png)
- Like the Accents, this is another euclidean sequencer internally that is slaved to the base pattern one (length = base pattern hits, only advances on every hit), but the question is answers is: "From these hits, which ones should be played on my preferred side?" Preferred??
- The **HITS** knob determines how many hits are from the base pattern are tilted/biased/??? or "should be panned"? to the side selected using the **SIDE** radio button. 
- The **SPREAD** knob determines the how much (...) If set to min. (default) <-- this yield no effect. As we increase this knob's alue, the "lefter L sounds and righter the R sounds". At full position, a hit is either played full L or full R. <--- also mention equal power panning!!! 

### Muting/Unmuting Tracks
![muted tracks screenshot](images/muting_tracks.png)

The button on top of each track strip acts as a mute/unmute toggler. When a track is **muted**, the button is grey. Otherwise, it is red.

It is also possible to toggle the mute/unmute status of a specific track with the **1st inlet's** `mute` **method** (e.g., `[mute 2 (` to mute/unmute track 2).

>[!Tip]
>If you have a hardware MIDI controller/keyboard, you can easily assign certain buttons/keys to mute/unmute specific tracks. This makes GCD-8 very "jammable". 

>[!NOTE]
>All the signal outlets (main mix + individiual tracks) are **post-mute**.


### Inlets
| Inlet | Accepted Values / Methods |
| ----- | ----------- |
| 0     | float: `0` to start the sequencer, `1` to stop it   |
|       | `ext`: set the master clock to external (**EXT.**) mode  |
|       | `mute <f>`: mute/unmute a specific track (e.g., `[mute 2 (` to mute/unmute track 2) |
| 1     | float: clock rate (BPM). This is ignored if the clock is set to internal (**INT.**) mode  |


### Outlets
| Outlet | Description |
| ------ | ----------- |
| 0-1    | Main mix L/R outputs (post-fader, post-mutes)           |
| 2-17   | Individual track L/R output pairs (post-fader, post-mute)            |
| 18-21  | Track #7 sequencer direct outs (hit(bang), velocity(0.0-1.0), accent(bang), rest(bang))            |
| 22-25  | Track #8 sequencer direct outs (hit(bang), velocity(0.0-1.0), accent(bang), rest(bang))            |

## License

This project is licensed under the GNU General Public License v3.0. See [LICENSE](LICENSE) for details.