# Treasure Planet Music for Kerbal Space Program

A cinematic **Treasure Planet-inspired soundtrack package for Kerbal Space Program**, built around the **MusicSwitcher** mod.

The soundtrack dynamically changes according to what your spacecraft is doing: assembling a vessel, sitting at the KSC, launching, reaching space, rendezvousing with another spacecraft, surviving re-entry, or touching down on another world.

## Features

- Dynamic music controlled by KSP flight state
- Dedicated Main Menu soundtrack
- Custom VAB/SPH construction playlist
- KSC ambient music
- Full launch sequence
- Atmospheric-to-space music transition
- Deep-space soundtrack
- Rendezvous and targeting music
- Re-entry/emergency soundtrack
- Distant-world landing music
- Smooth MusicSwitcher crossfades
- KSP-safe OGG filenames and GameDatabase paths

## Soundtrack

| Situation | Music |
|---|---|
| Main Menu | **12 Years Later** |
| VAB / SPH | **Off To the Spaceport** / **Ben** |
| Kerbal Space Center | **Rooftop** |
| Normal atmospheric flight | **Rooftop** |
| Initial Flight state | **The Launch** → **Launch Ending** |
| Prelaunch | **The Launch** → **Launch Ending** |
| Prelaunch hold | **Rooftop** |
| Entering vacuum | **Always Know Where You Are** |
| Deep-space flight | **Silver Leaves** |
| Rendezvous / close targeting | **The Map** |
| Re-entry / emergency | **Jim Saves the Crew** |
| Landing on another world | **Silver Comforts Jim** |

## Launch Sequence

The launch soundtrack is triggered in two ways.

### Initial Flight

Whenever MusicSwitcher enters its initial Flight state:

```text
init_flight
    ↓
The Launch
    ↓
Launch Ending
    ↓
Normal flight routing
```

This allows the cinematic opening to work even when a vessel is initially loaded somewhere other than the launchpad.

### Prelaunch

When the normal flight router detects that the vessel is in KSP's `prelaunch` situation:

```text
flight-router
    ↓
The Launch
    ↓
Launch Ending
    ↓
prelaunch-hold
    ↓
Rooftop
```

The `prelaunch-hold` state prevents the launch sequence from endlessly restarting while the spacecraft remains on the pad.

Once the vessel leaves `prelaunch`, normal flight music resumes.

## Flight Music Logic

### KSC and Atmosphere — Rooftop

`Rooftop` acts as the relaxed home-base soundtrack.

It plays at the Kerbal Space Center and acts as the normal atmospheric-flight music when no higher-priority event is occurring.

### Atmosphere to Space — Always Know Where You Are

When the vessel transitions into:

- Suborbital flight
- Orbit
- An escape trajectory

the soundtrack changes to **Always Know Where You Are**.

This serves as the musical transition from atmosphere to the vacuum of space.

The track plays once before the system moves into the sustained deep-space state.

### Deep Space — Silver Leaves

After the initial space-transition track finishes, **Silver Leaves** becomes the normal vacuum-flight soundtrack.

It covers long periods of:

- Orbital flight
- Interplanetary cruising
- Escape trajectories
- General exploration of the Kerbol system

### Targeting and Rendezvous — The Map

**The Map** activates during close approaches to selected targets.

#### Vessel / Docking Port

Triggered when a targeted vessel or docking port is within:

```text
100 km
```

#### Celestial Body

Triggered when a targeted celestial body is within:

```text
5,000 km
```

Moving outside the applicable range or clearing the target returns the soundtrack to normal deep-space music.

## Re-entry and Emergency

**Jim Saves the Crew** provides the high-tension soundtrack for dangerous atmospheric flight.

The emergency state activates when either of these conditions is met:

### Re-entry

```text
Atmospheric flight
Surface velocity > 700 m/s
Throttle <= 5%
```

This is intended primarily for unpowered atmospheric re-entry.

### Extreme Atmospheric Speed

```text
Atmospheric flight
Surface velocity > 1,700 m/s
```

This triggers regardless of throttle.

### Emergency Exit

The soundtrack returns to normal when the craft:

- Slows below approximately **600 m/s**
- Leaves atmospheric flight
- Reaches space again
- Lands safely

## Surface Landing — Silver Comforts Jim

Landing or splashing down on a world outside Kerbin's sphere of influence activates:

**Silver Comforts Jim**

This provides a quieter emotional ending after a long journey.

The state remains active while the spacecraft is landed or splashed.

Liftoff returns control to the normal flight-routing system.

## Requirements

- **Kerbal Space Program 1**
- **MusicSwitcher 0.3.x**
- **ModuleManager 4.2.3**

## Installation

Download or clone the package and place the folder inside your KSP `GameData` directory.

The resulting layout should look like:

```text
Kerbal Space Program/
└── GameData/
    └── TreasurePlanetMusic/
        ├── TreasurePlanetMusic.cfg
        ├── README.txt
        ├── manifest.json
        └── Music/
            ├── 12_years_later.ogg
            ├── always_know_where_you_are.ogg
            ├── ben.ogg
            ├── jim_saves_the_crew.ogg
            ├── launch_ending.ogg
            ├── off_to_the_spaceport.ogg
            ├── rooftop.ogg
            ├── silver_comforts_jim.ogg
            ├── silver_leaves.ogg
            ├── the_launch.ogg
            └── the_map.ogg
```

Make sure **MusicSwitcher** and **ModuleManager** are also installed.

## Important: Main MusicSwitcher Config

This package is a **main MusicSwitcher configuration**.

MusicSwitcher supports only one active main `music_switcher` configuration.

Remove or disable older versions such as:

```text
LaunchMusic/
KerbalGalleonMusic/
TreasurePlanetMusic/
```

before installing a newer version of this package.

Do not leave multiple versions of `TreasurePlanetMusic.cfg` active at the same time.

## Audio Paths

MusicSwitcher uses GameDatabase paths relative to `GameData`.

For example:

```cfg
track = TreasurePlanetMusic/Music/the_launch
```

not:

```cfg
track = TreasurePlanetMusic/Music/the_launch.ogg
```

The extension is deliberately omitted.

All packaged soundtrack filenames use lowercase ASCII characters and underscores to reduce the chance of KSP/Unity asset-loading problems.

## MusicSwitcher State Overview

The major states are approximately:

```text
init_main_menu
    └── 12 Years Later

init_editor
    └── Off To the Spaceport / Ben

init_space_center
    └── Rooftop

init_flight
    └── The Launch
            ↓
       launch-ending
            ↓
       flight-router
            ├── Rooftop
            ├── vacuum-arrival
            │       └── Always Know Where You Are
            │               ↓
            │          deep-space
            │               └── Silver Leaves
            │
            ├── targeting-rendezvous
            │       └── The Map
            │
            ├── reentry-emergency
            │       └── Jim Saves the Crew
            │
            └── surface-landing
                    └── Silver Comforts Jim
```

Prelaunch can also route back into `init_flight` to replay the launch sequence.

## Fading

All custom music states use MusicSwitcher's:

```cfg
fade_simple{}
```

This provides smooth fades when changing between soundtrack states.

## Troubleshooting

### MusicSwitcher says it cannot load a track

Check that the file exists in:

```text
GameData/TreasurePlanetMusic/Music/
```

and confirm that the CFG path does **not** include `.ogg`.

For example:

```cfg
track = TreasurePlanetMusic/Music/silver_leaves
```

### The wrong music plays after installing an update

Remove previous MusicSwitcher soundtrack configurations before installing the current version.

KSP may otherwise load more than one configuration.

### The launch music repeats endlessly on the pad

The current configuration includes a dedicated `prelaunch-hold` state specifically to prevent this.

After **The Launch** and **Launch Ending** finish, **Rooftop** plays until liftoff.

### Switching vessels does not always restart the introduction

MusicSwitcher stores its current state on individual vessels.

The initial launch soundtrack therefore runs whenever the vessel enters MusicSwitcher's `init_flight` state, while vessels with an already-saved MusicSwitcher state may resume that saved state.

## Configuration

The main configuration is:

```text
GameData/TreasurePlanetMusic/TreasurePlanetMusic.cfg
```

Because the soundtrack is controlled entirely through MusicSwitcher CFG states, most behavior can be adjusted without recompiling a DLL.

This includes:

- Rendezvous distance
- Emergency velocity thresholds
- Track choices
- Playlist order
- Looping behavior
- State transitions
- Fade behavior

## Credits

### Music

Music used by this configuration originates from Disney's **Treasure Planet** soundtrack.

Original score by **James Newton Howard**.

**Always Know Where You Are** performed by **John Rzeznik**.

### KSP Integration

Configuration designed for the **MusicSwitcher** framework for Kerbal Space Program.

### Kerbal Space Program

Kerbal Space Program was originally developed by **Squad**.

Kerbal Space Program and related trademarks belong to their respective owners.

## Copyright Notice

This project is an unofficial fan-made KSP configuration and is not affiliated with or endorsed by Disney, Walt Disney Records, Take-Two Interactive, Private Division, Squad, or the developers of MusicSwitcher.

The configuration files themselves may be distributed separately from copyrighted soundtrack audio.

If publishing this project publicly, make sure you have the necessary rights or permission to redistribute any included music files.

## License

The MusicSwitcher configuration and supporting project files may be released under a software/content license chosen by the repository owner.

Copyrighted soundtrack recordings are **not** covered by that license and remain the property of their respective copyright holders.
