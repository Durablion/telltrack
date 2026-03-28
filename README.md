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


## Permissions

- Location (Always) — required for background navigation
- Background modes: `location`, `audio`






