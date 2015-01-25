Mutated-Mutables
================

Various enhancements, experiments and outright hacks of Mutable Instruments firmware code.

This repository is a copy of the Mutable Instruments GitHub repository at https://github.com/pichenettes/eurorack

So far, the only modified code is the Bees-in-Trees enhancements to Braids. The following features (and some anti-features) have all been implemented in Bees-in-Trees Version 3t, the source code for which is in the braids/ directory here. 

Acknowledgements
================
First and foremost, huge thanks are due to Olivier Gillet of Mutable Instruments for creating Braids and his other wonderful Eurorack synth modules - in terms of depth and breadth of creativity, elegance and excellence of implementation and execution, they are head-and-shoulders above other Eurorack synth modules. But equally huge thanks are due to Olivier for having the vision, courage and faith to release the designs and source code for his modules under open-source licenses. Without that, the modifications which are documented here would not have been possible.

Many thanks also to [Sneak-Thief](http://sneak-thief.com) in Berlin, first of all for providing the impetus for me to start hacking the Braids code, and then for providing lots of really useful feedback on the design of the Bees-in-Trees modifications, and finally for actively testing the code and discovering several bugs (and documenting how to reproduce them!). Thanks also to _weliveincities_ on the Mutable Instruments forum for testing each version and providing feedback.

Bees-in-Trees v3t enhancements
=============================

* Bees-in-Trees is based on the official Braids v1.7 source code, and has had the sync buffer and DAC timing bug fixes ported to it, thus the core oscillator code is identical to the current Braids v1.7 code. However, many changes to modulation options and other aspects have been made, as detailed below. 
* Instead of a single internal envelope, there are now two internal modulators, MOD1 and MOD2. The MOD1 and MOD2 menu settings allow each internal modulator to be turned off (OFF), put in in LFO mode (LFO), or in one of two envelope modes (ENV- and ENV+). ENV- and ENV+ are negative and positive envelopes respectively - ENV+ is like a traditional attack-decay envelope, ENV- is the  inverse.
* Each internal modulator has a rate setting, RAT1 and RAT2 respectively, with ranges from 0 to 127. When in envelope mode, lower rate values produce shorter envelope segment (attack and decay) durations, and higher rate values produce longer envelope segment durations i.e. slower envelopes. When in LFO mode, lower rate values produce slower LFO speeds (lower LFO frequency), and higher rate values produce faster speeds (higher frequencies), if the RINV (rate inversion) setting is ON (which is the default). However, if you turn RINV off, then higher RAT1 or RAT2 settings will produce slower LFO speeds. This can be useful if you are controlling one envelope and one LFO with an external voltage - see below.
* Each internal modulator has four depth controls (M1→T, M1→C, M1→L and M1→F, and M2→T, M2→C, M2→L and M2→F), which set the depth of modulation for each of timbre, color, level (amplitude) and frequency (pitch), respectively, between values of zero to 250. Each modulation destination receives a weighted sum of the modulator values. For example, if M1→T = 30 and the instantaneous valus of modulator 1 is, say, 100, and M2→T = 60 and the instantaneous value of modulator 2 is 200, then the timbre parameter will receive a value of (30 * 100) + (60 * 200) = 15000. Obviously the values for modulator 1 and modulator 2 change each time the envelopes/LFO are rendered, which is about 4000 times per second (I think). The timbre and color potentiometers and/or the timbre and color CV inputs act as offsets - the weighted sum of modulator values is added to these offsets.
* The M1→2 settings controls the degree of frequency modulation of modulator 2 by modulator 1. Yes, LFO 1 can frequency modulate LFO 2! This also works in envelope mode.
* The ratio between the attack and decay segments of each envelope, or the rising part of the waveform and the falling part in LFO mode, can be set by menu choices labelled ⇑⇓1 and ⇑⇓2. These provide value choices ranging between 0.02 and 50.0. These values refer to the ratio between the duration of the attack segment of the attack-decay (AD) envelope, or the rising segment of the LFO waveform, and the duration of the decay segment or falling segment, respectively. Thus a value of 0.10 means that the attack part of the envelope is a tenth of the duration of the decay part. Likewise, at a setting of 0.10 the LFO waveform is asymmetrical, with the rising portion only one-tenth as long as the falling portion. Obviously a ratio setting of 1.00 produces a symmetrical waveform, and an envelope in which attack and decay are of equal duration. 
* Each of the two internal modulators has its own pair of shape settings (⇑SH1 and ⇓SH1, and ⇑SH2 and ⇓SH2). These set the shape of the curve used for the attack/rising and decay/falling parts of the envelope/LFO waveform respectively, for each of modulator 1 and modulator two. Thus, an envelope can have different shapes for the attack and decay portions, and an LFO waveform can not only be asymmetrical, but have a different curvature or shape on the raising and falling arms of its cycle. The following curve shapes are available:
  * EXPO is an exponential curve, as used in the envelopes in the official Braids firmware;
  * LINR is a linear curve i.e. a straight line (and thus in LFO mode produces sawtooth, triangle or ramp waveforms, depending on the ⇑⇓1 or ⇑⇓2 ratio settings);
  * WIGL is a wiggly curve;
  * SINE is a sine wave (well, almost a sine wave - it re-uses an existing look-up table in the Braids code which is close to a sine wave);
  * SQRE is a square-ish curve - a bandwidth-limited square wave with quite rounded shoulders, in fact; 
  * BOWF is a logarithmic curve with a flat top - it is actually an inverted version of the bowing envelope for the BOWD oscillator mode in Braids, hence the name; 
  * RNDE is the same as EXPO except that the target level for the top of the envelope or LFO waveform varies randomly on each envelope or LFO cycle; 
  * RANDL and RNDS are the same as RNDE, except using linear and square-ish curves as described above; 
  * RNDM sets a fixed random level which is flat for the entire envelope segment or LFO half-wave - thus it acts like a traditional clocked sample-and-hold sampling a random voltage.
* The META menu setting has been replaced by an FMCV setting, which determines to what use the FM control voltage input is put. The available choices are: 
  * FREQ, which means the FM input does FM;
  * META, which is the same as META mode on in the official firmware - voltage on the FM input scans through the oscillator modes;
  * RATE, in which voltage on the FM input sets the duration of the envelope segments, or the frequency of the LFOs - thus providing voltage-controlled envelopes and/or LFOs, with the FM voltage affecting the duration/speed of both internal modulators;
  * RAT1 is the same except the FM voltage only affects modulator 1;
  * RAT2 is also the same but the FM voltage only affects modulator 2. Note that for the rate settings, the voltage on the FM input is added to the RAT1 and RAT2 values for each each modulator, thus a base LFO speed or envelope duration can be set using RAT1 and RAT2, and that can then be modified by voltage on the FM input.
  * BIT⇓ provides voltage control over bit crushing, as set by the BITS menu.
* LFO range has been enabled in the range (RANG) menu. This LFO range was always present in the Braids source code, but its selection was disabled. The LFO range seems to go down to about 1Hz or so - thus it doesn't make Braids into a proper LFO, but it is low enough for many LFO modulation duties. Braids certainly produces many more interesting LFO waveforms than your average voltage-controlled LFO, And of course the two internal LFOs can still modulate Braids when its set to LFO - thus you can use Braids as two voltage-controlled LFOs (MOD1 and MOD2) inside your voltage-controlled LFO (the main Braids VCO when in LFO range)! 
* DRFT (VCO drift) is now a settable value, from zero (OFF) to 15. Fifteen produces fairly noisy grunge, but settings of around 4 or 5 can add character to some oscillator modes.
* TSRC, TDLY, OCTV, QNTZ, BITS, BRIG, CAL. and CV testing menu choices are unchanged and function exactly as they do in the official Braids firmware.
* However, immediately following QNTZ (pitch quantisation), there is a new menu selection called QVIB, which stands for "quantise vibrato". It defaults to OFF - that is, vibrato from the internal modulators (or from the external FM input) is applied after pitch quantisation has been performed, if pitch quantisation is enabled (via the QNTZ setting). IF QVIB is set to ON, however, then quantisation will be performed after pitch modulation from the internal modulators has been added. Thus, a triangle pitch (frequency) modulation, set by, say, the M1→F level, will result rising and falling chromatic arpeggio effects, more or less. It can be quite effective when the RNDM modulator shape is used, for example.
* M1SY and M2SY (modulator 1 sync, and modulator 2 sync) determine whether a trigger (either external or auto) will reset the phase of modulator 1 or modulator 2, respectively. They default to ON.
* OSYN (oscillator sync) determines whether oscillator phase synchronisation is enabled or not. It defaults to OFF. When enabled, (external or auto) triggers may cause audible clicks in some oscillator modes. Disabling it prevents those clicks, at the expense of not resetting the oscillator phase. However, the official Braids firmware disables oscillator sync whenever the internal envelope was used, in any case, probably for this reason.

Anti-enhancements
=================
These changes were required in order to free up space in the firmware storage for the enhancements described above.

* A paschal oophorectomy has been performed: the Easter egg oscillator model has been removed and the ability to trigger Easter egg mode has been disabled. One could say that the code for it has been Pynchoned off. 
* The marquee feature has been removed.
* The QPSK oscillator model has been removed.
* The sample rate (RATE) setting has been removed. Braids is locked at the maximum 96K sample rate instead. That isn't a problem from a CPU load perspective, because the sample rate reduction method didn't mean that the Braids processor was working less hard, just that it was discarding every second sample, or all but every third sample, or all but every fourth sample etc. However, the BITS setting is still there and still works, so you can still do bit-depth reduction for lo-fi grungy sounds, if that is your thing, and DRFT can now be cranked up to levels 15 times greater than before, if you really wish to. 
* VCO tune flattening (FLAT) and signature waveshapping (SIGN) have been removed.

TO-DO and Roadmap
=================
* Port the fixes which Olivier has developed for the "sync buffer" problem which appears to exist in the Braids v1.7rc code. Bees-in-Trees is based on the v1.7rc code and thus has inherited the problems - although I cannot seem to reproduce or trigger them on testing - but others can. 
* Port additional code which Olivier plans to add to Braids v1.8 (which is what v1.7 will finally be released as, he tells me) which checks the layout of the stored Braids settings and the value of a "magic byte" at the end of the settings data structure which indicates whether the settings are for official Braids firmware or for modified firmware such as Bees-in-Trees. These checks are intended to avoid problems and unexpected behaviour if end users use the audio firmware update procedure to try out modified firmware like Bees-in-Trees, and then revert to the official firmware (or v-v). I won't be making audio update (WAV) files for Bees-in-Trees available until this checking code is in place and thoroughly tested. If you are using the serial FTDI interface or the miniJTAG/SWD interface on Braids to update your firmware, then this is not a concern because you can always do a full-chip erase to recover your Braids. But using just the audio bootloader, it is currently theoretically possible to get your Braids into an unrecoverable state (in the absence of FTDI or JTAG/SWD programmers) by swapping between Bees-in-Trees and the official Braids firmware. The additional checking code will prevent that from ever happening.
* Some of the parameters in Bees-in-Trees have ranges of 0 to 255, and some 0 to 127. I plan to rationalise that and make them all 0 to 127, or all 0 to 255, at least in the user interface. However, some use steps of 10, others use steps of 1 to provide very fine resolution. Those steps may need to be tweaked to minimise encoder twisting, while retaining adequately fine control over critical parameters (pitch modulation and LFO rates in particular).
* It would be great to add a constrain-to-scales scales capability, similar to the one in Yarns or in the Shruthi and Ambika, to the pitch quantisation facility. That will involve various look-up tables, and there may not be room - the firmware is almost at maximum size as it is.
* Alternatively, it might be possible to add a simple step sequencer to Braids, like the ones in the Anushri and in Yarns. However, there may not be enough room, and I am very reluctant to remove any more features or oscillator models from Bees-in-Trees to  accomodate it. Braids-with-built-in-sequencer might need to be a separate set of modifications resulting in different firmware yet again.
  
Encoder direction
=================
Some crazy people have built DIY versions of Braids, and have used Bourns encoders. These operate in the opposite direction to those used by factory-built Braids modules. In order to accomodate this, #define BACKWARDS_ENCODER is set at the top of the encoder.h source code file. If you are compiling the source code yourself to load on a factory-built Braids, then you should ensure that this #define is commented out (place // in front of it on the same line). Failure to do that will mean that the encoder on your Braids will run backwards!

Discussion
==========
All these enhancements seem to work fine, but more extensive testing is required. They are intended for advanced users only, and assume thorough familiarity with the way Braids operates. The modulators can interact with each other, by design, in complex and interesting ways (their values are summed for each destination BTW, and the resulting sum is clipped - thus with the right offsets and modulation depths, you can achieve half-wave clipping effects etc), and this  substantially extends the range of sounds that can be coaxed out of a Braids, without using any other modules at all. Add a single external LFO or sequencer modulating the FM input and all sorts of really amazing things are possible. As such, Bees-in-Trees may be particularly useful in small systems where external modulation sources are few, but since it does not remove or subtract any important or commonly-used existing Braids features or capabilities, it may be useful even in large systems.

Feedback and suggestions are welcome and appreciated - please email tim.churches@gmail.com