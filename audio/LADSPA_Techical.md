# Introduction

This is a technical look at `ladspa.h`, a header file which mandates
how LADSPA plugins are created and handled. To not make this doc
too large, any custom datatype or variable in the header is refernced
by its line number, given as "(ln: LINENUM)".

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

# Assisting datatypes

There are plenty of custom datatypes in `ladspa.h` made using `typedef`.
We should understand the assisting ones before we dive into the
plugin struct.

## LADSPA_Data

`typedef float LADSPA_Data;` (ln: 84) is the most basic datatype which
is passed from and to the plugin. It communicates **audio samples**
and **control values**.

## LADSPA_Properties

`typedef int LADSPA_Properties;` (ln: 94) is used to communicate the
optional properties of the LADSPA plugin. These are ORed, just like
`LADSPA_PortRangeHintDescriptor`. A plugin can only have 3 properties:

1. `LADSPA_PROPERTY_REALTIME` (ln: 99)
2. `LADSPA_PROPERTY_INPLACE_BROKEN` (ln: 106)
3. `LADSPA_PROPERTY_HARD_RT_CAPABLE` (ln: 132)

## LADSPA_PortDescriptor

`typedef int LADSPA_PortDescriptor;` (ln: 152) is used to communicate
what type of port we're working with. A port can have 4 properties:

- `LADSPA_PORT_INPUT` (ln: 155)
- `LADSPA_PORT_OUTPUT` (ln: 158)
- `LADSPA_PORT_CONTROL` (ln: 162)
- `LADSPA_PORT_AUDIO` (ln: 166)

## LADSPA_PortRangeHintDescriptor

`typedef int LADSPA_PortRangeHintDescriptor;` (ln: 200) is used to
communicate the nature of the plugin ports to the plugin host. The
hints are ORed toghether and placed in the port range hint descriptor.

- `LADSPA_HINT_BOUNDED_BELOW` (ln: 208)
- `LADSPA_HINT_BOUNDED_ABOVE` (ln: 216)
- `LADSPA_HINT_TOGGLED` (ln: 224)
- `LADSPA_HINT_SAMPLE_RATE` (ln: 232)
- `LADSPA_HINT_LOGARITHMIC` (ln: 237)
- `LADSPA_HINT_INTEGER` (ln: 245)
- `LADSPA_HINT_DEFAULT_MASK` (ln: 258)
- `LADSPA_HINT_DEFAULT_NONE` (ln: 261)
- `LADSPA_HINT_DEFAULT_MINIMUM` (ln: 265)
- `LADSPA_HINT_DEFAULT_LOW` (ln: 272)
- `LADSPA_HINT_DEFAULT_MIDDLE` (ln: 279)
- `LADSPA_HINT_DEFAULT_HIGH` (ln: 286)
- `LADSPA_HINT_DEFAULT_MAXIMUM` (ln: 290)
- `LADSPA_HINT_DEFAULT_0` (ln: 295)
- `LADSPA_HINT_DEFAULT_1` (ln: 300)
- `LADSPA_HINT_DEFAULT_100` (ln: 303)
- `LADSPA_HINT_DEFAULT_440` (ln: 308)

This descriptor is used in [LADSPA_PortRangeHint](#LADSPA_PortRangeHint).

## LADSPA_Handle

`typedef void * LADSPA_Handle;` (ln: 363) stores a plugin instance.
It isn't accessed directly, instead being used from the functions
of the plugin descriptor (`LADSPA_Descriptor`).

## LADSPA_PortRangeHint

```c
typedef struct _LADSPA_PortRangeHint {
  LADSPA_PortRangeHintDescriptor HintDescriptor;
  LADSPA_Data LowerBound;
  LADSPA_Data UpperBound;
} LADSPA_PortRangeHint;
```

The struct consist of the port bounds, and a
[LADSPA_PortRangeHintDescriptor](#LADSPA_PortRangeHintDescriptor).

# LADSPA_Descriptor

This is the actual essence of the plugin. Here, the entirety of how
the plugin behaves in various scenarios is described.

```c
typedef struct _LADSPA_Descriptor {

  unsigned long UniqueID;

  LADSPA_Properties Properties;

  const char* Label;
  const char* Name;
  const char* Maker;
  const char* Copyright;

  unsigned long PortCount;
  const LADSPA_PortDescriptor* PortDescriptors;
  const char* const* PortNames;
  const LADSPA_PortRangeHint * PortRangeHints;

  void * ImplementationData;

  LADSPA_Handle (*instantiate) (
    const struct _LADSPA_Descriptor * Descriptor,
    unsigned long SampleRate
  );

   void (*connect_port) (
     LADSPA_Handle Instance,
     unsigned long Port,
     LADSPA_Data * DataLocation
   );

  void (*activate) ( LADSPA_Handle Instance );

  void (*run) (
    LADSPA_Handle Instance,
    unsigned long SampleCount
  );

  void (*run_adding) (
    LADSPA_Handle Instance,
    unsigned long SampleCount
  );

  void (*set_run_adding_gain) (
    LADSPA_Handle Instance,
    LADSPA_Data Gain
  );

  void (*deactivate) ( LADSPA_Handle Instance );

  void (*cleanup) ( LADSPA_Handle Instance );

} LADSPA_Descriptor;
```

It's has a lot of elements, to be sure, but most have been covered
in [INSERT DOC TITLE HERE](link here).
<!-- TODO -->
