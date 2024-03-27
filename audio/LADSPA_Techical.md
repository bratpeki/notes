# Introduction

This is a technical look at `ladspa.h`, a header file which mandates
how LADSPA plugins are created and handled.

## Basic terms

The are a few basic terms we should understand before analyzing the
actual header.

### Audio samples

Audio sample are are the smallest bit of amplitude data that describes
the sound passed in and out of the plugin. You know when you're looking at
a waveform and see that it goes up and down? Every point you see is an audio
sample, basically. You're passing that to your plugin. It manipulates it,
and hands you back the manipulated audio sample.

### Descriptor

A Descriptor is a datatype which stores multiple smaller, related
bits of data.

### Ports

Ports are the fundemental channels through which data is passed in and
out of the plugin. The three types of ports are:

1. Audio input ports
2. Audio output ports
3. Control value ports

Control values are simply the parameters you can give your plugin (ex.
the threshold of a limiter plugin). They have specified upper and lower
limits, as well as default values.

# Primitive typedefs

There are plenty of custom datatypes in `ladspa.h` made using `typedef`.
We should understand these before we dive into more complex structure
datatypes.

## LADSPA_Data

`typedef float LADSPA_Data;` (ln: 84) is the most basic datatype which
is passed from and to the plugin. It communicates **audio samples**
and **control values**.
