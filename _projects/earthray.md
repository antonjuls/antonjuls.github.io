---
title: EarthRay
description: Real-time multiplayer AR app that lets users visualize rays through the Earth using GPS, device orientation, and 3D globe rendering.
role: "Personal project — solo developer: design, mobile app, and relay backend."
tags: [React Native, Expo, TypeScript, Three.js, WebSocket, AR]
---

EarthRay is a real-time multiplayer AR mobile app. Users point their phone in any direction; a ray projects from the rear camera through the Earth. Other connected users see that ray entering their AR space from the geometrically correct direction.

<p class="cta-link"><a href="{{ '/projects/earthray/specification/' | relative_url }}">Read the full specification &rarr;</a></p>

## Available on

- App Store <span class="badge-soon">Coming soon</span>
- Google Play <span class="badge-soon">Coming soon</span>

## How It Works

GPS positions each user on a WGS84 ellipsoid model of the Earth. Device orientation — accelerometer, gyroscope, and magnetometer — determines the direction each phone is pointing. Rays are computed in ECEF (Earth-Centered, Earth-Fixed) coordinates and rendered in AR via Three.js. A self-hosted WebSocket relay server syncs positions and ray directions between all users in a session — in-memory only, no database, session data is auto-deleted on disconnect.

**Privacy note:** Precise GPS coordinates are used only locally on the device. Before any data is sent to the relay server or other users, the location is fuzzed by ~30 km — so your exact position is never shared. Crash reports and anonymous usage statistics never include location, room codes or names, and statistics can be turned off in Settings.

When a user in Amsterdam points their phone downward toward New York, the ray passes through the Earth and exits on the other side. A user in New York sees that ray entering their AR space from below, at the correct geometric angle.

## Key Features

- **3D globe view** showing all connected users and their rays in real time
- **AR camera view** with rays rendered in physically correct directions
- **Multiplayer via room codes** — no accounts needed, join instantly
- **Sun and Moon** rendered at astronomically correct positions on the globe

## Tech Stack

- React Native + Expo
- TypeScript
- react-three-fiber (Three.js)
- Self-hosted WebSocket relay server (Node.js, in-memory rooms, anonymous JWT auth)
- expo-sensors, expo-camera, expo-location
- Sentry (crash reports) and Aptabase (anonymous usage statistics), both EU-hosted

## Legal

- [Privacy Policy]({{ '/projects/earthray/privacy-policy/' | relative_url }})
- [Terms and Conditions]({{ '/projects/earthray/terms/' | relative_url }})
