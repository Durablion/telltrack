# TellTrack

An iOS GPS trail navigation app with real-time voice guidance and anonymous cloud track upload.

## Overview

TellTrack records a GPS path and guides you back to the start using voice directions. It calculates your position relative to the recorded path, detects turns, and tells you how to get back on track if you stray. Recorded tracks are uploaded anonymously to a cloud server for quality assurance and statistics.

## Features

- **Voice guidance** — announces turn directions and off-track warnings at configurable intervals
- **GPX file support** — import, browse, and manage GPX trails; export your tracked route
- **Real-time path tracking** — calculates perpendicular distance to the nearest trail segment
- **Turn detection** — identifies sharp turns along the route and warns in advance
- **Bidirectional navigation** — toggle to reverse the trail direction
- **Interactive map** — view the route as a polyline with Standard, Satellite, or Hybrid tiles
- **Navigation stats** — distance to start/end, elapsed time, distance traveled, and more
- **Configurable alerts** — adjust voice interval, off-track distance, and waypoint alert distance
- **Battery & accuracy graphs** — live scrolling graphs during recording (updates every 1 min / GPS fix)
- **Anonymous track upload** — recorded tracks uploaded to `track.shippy.no`; no personal data sent
- **Offline queue** — tracks queued locally and retried automatically on network restore
- **Demo track** — built-in demo GPX (matti_down_rtk.gpx) to try guiding without recording

## Requirements

- iOS 18.0+
- Xcode 8+
- CocoaPods

## Setup

```bash
pod install
open TellTrack.xcworkspace
```

## Architecture

| Layer | Description |
|---|---|
| `RecordViewController` | Main screen; starts GPS recording, shows info box, battery/accuracy graphs |
| `NavigateBackViewController` | Guides user back along recorded track; map overlay with voice |
| `MiniGraphView` | Reusable scrolling line graph (stretch-to-fit, fill-under-curve) |
| `TrackUploader` | GPX builder, anonymous upload, UUID deduplication, offline retry queue |
| `AppMenuViewController` | Cogwheel menu: Demo-track, EULA, About |
| `EULAViewController` | Scrollable EULA, English + Norwegian (auto-detected by device language) |
| `AboutViewController` | App info page |
| `FollowPathViewController` | Legacy navigation screen; location updates, voice guidance, timer |
| `ClosestCoord` | Core path-following algorithm; calculates distance to path and heading deviation |
| `GlobalVariables` | Path loading, Douglas-Peucker simplification, turn detection, shared state |
| `SelectPathTableViewController` | GPX file browser with import/delete/rename |
| `MapViewController` | MKMapView with polyline overlays and waypoint annotations |
| `SettingsViewController` | Sliders for voice interval, off-track distance, waypoint alert distance |
| `InfoViewController` | Read-only navigation statistics display |

## Voice Guidance Modes

The app combines movement state and trail position to decide what to announce:

| Situation | Announcement |
|---|---|
| GPS unavailable | "I can't find the position. Are the location services turned on?" |
| Before trail start | "Turn X degrees right/left, it is Y to start." |
| Past trail end | "Turn X degrees right/left, you are Y past the finish point." |
| On trail — wrong way (moving) | "TURN, you go the wrong way. The track is X away." |
| On trail — standing still | "Turn X degrees right/left, the track is X away. It is X meters to finish." |
| On trail — moving — off track | "The track is X meters to the right/left." |
| On trail — moving — on track | "on track" |
| Near a waypoint (prepended) | "You are by, [name]," |
| At start or finish (within 5m) | "You are at the startpoint / finishpoint" |

Speech is suppressed when standing still after the message has already been said once.

## Key Algorithms

- **Douglas-Peucker** line simplification to reduce path points before navigation
- **Haversine / bearing** calculations for GPS distance and direction
- **Heron's formula** for perpendicular distance from user to path segment
- **Wrong-way detection** via consecutive distance-delta comparisons

## Dependencies

- [iOS-GPX-Framework](https://github.com/merlos/iOS-GPX-Framework) (v0.0.2) — GPX parsing
- TBXML (v1.5) — XML parsing

## Localization

English, Norwegian (nb), Japanese, Simplified Chinese, Hindi, Polish, Spanish (Mexico)

## Permissions

- Location (Always) — required for background navigation
- Background modes: `location`, `audio`

## Backend Server

A lightweight Flask API on a Raspberry Pi, exposed via Cloudflare Tunnel at `https://track.shippy.no`.

- Anonymous track upload (no device name, no user identity)
- UUID-based deduplication — no duplicate tracks even on retry
- Web viewer: device list, Leaflet map, swipe-to-delete, GPX download
- Statistics page: uploads per day, per device, longest track
- Runs as `systemd` services (`trackserver`, `cloudflared`) — auto-starts on Pi reboot

See `server/README.md` for setup and API reference.

## RTK Base Station

A companion RTK GNSS base station script (`Claudecode/rtk/base.py`) drives a Quectel LG290P module:

- Survey-in mode (60s min, 2.0m accuracy threshold)
- NTRIP caster on port 2101, mountpoint: BASE
- Broadcasts RTCM3.3 to rover clients
- Runs as `systemd` service (`rtkbase`) — auto-starts on Pi reboot
