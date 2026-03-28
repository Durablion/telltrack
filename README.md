# TellTrack

An iOS GPS trail navigation app with real-time voice guidance and anonymous cloud track upload.

## Overview

TellTrack records a GPS path and guides you back to the start using voice directions. It calculates your position relative to the recorded path, detects turns, and tells you how to get back on track if you stray. Recorded tracks are uploaded anonymously to a cloud server for quality assurance and statistics.

## Features

- **Voice guidance** — announces turn directions and off-track warnings at configurable intervals
- **Real-time path tracking** — calculates perpendicular distance to the nearest trail segment
- **map** — view the route as a polyline with Standard tiles
- **Navigation stats** — distance to end
- **Battery & accuracy graphs** — live scrolling graphs during recording (updates every 1 min / GPS fix)
- **Demo track** — built-in demo GPX (matti_down_rtk.gpx) to try guiding without recording

## Requirements

- iOS 18.0+
- Xcode 8+
- CocoaPods


## Voice Guidance Modes

The app combines movement state and trail position to decide what to announce:

| Situation | Announcement |
|---|---|
| GPS unavailable | "Location services are off" |
| Before trail start | "Turn X degrees right/left, it is Y to start." |
| Past trail end | "Turn X degrees right/left, you are Y past the finish point." |
| On trail — wrong way (moving) | "TURN, you go the wrong way. The track is X away." |
| On trail — standing still | "Turn X degrees right/left, the track is X away. It is X meters to finish." |
| On trail — moving — off track | "The track is X meters to the right/left." |
| On trail — moving — on track | "on track" |
| At start or finish (within 5m) | "You are at the startpoint / finishpoint" |

## Permissions

- Location (Always) — required for background navigation
- Background modes: `location`, `audio`






