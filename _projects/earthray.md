---
title: EarthRay
description: Real-time multiplayer AR app that lets users visualize rays through the Earth using GPS, device orientation, and 3D globe rendering.
tags: [React Native, Expo, TypeScript, Three.js, Firebase, AR]
---

EarthRay is a real-time multiplayer AR mobile app. Users point their phone in any direction; a ray projects from the rear camera through the Earth. Other connected users see that ray entering their AR space from the geometrically correct direction.

## How It Works

GPS positions each user on a WGS84 ellipsoid model of the Earth. Device orientation — accelerometer, gyroscope, and magnetometer — determines the direction each phone is pointing. Rays are computed in ECEF (Earth-Centered, Earth-Fixed) coordinates and rendered in AR via Three.js. Firebase Realtime Database syncs positions and ray directions between all users in a session.

**Privacy note:** Precise GPS coordinates are used only locally on the device. Before any data is sent to Firebase or other users, the location is fuzzed by ~30 km — so your exact position is never shared.

When a user in Amsterdam points their phone downward toward New York, the ray passes through the Earth and exits on the other side. A user in New York sees that ray entering their AR space from below, at the correct geometric angle.

## Key Features

- **3D globe view** showing all connected users and their rays in real time
- **AR camera view** with rays rendered in physically correct directions
- **Multiplayer via room codes** — no accounts needed, join instantly
- **Eratosthenes readout** — measures Earth's circumference using the angle between two users' surface normals (the same method Eratosthenes used in 240 BC, now with smartphones)
- **Sun and Moon** rendered at astronomically correct positions on the globe

## Tech Stack

- React Native + Expo
- TypeScript
- react-three-fiber (Three.js)
- Firebase Realtime Database + Anonymous Auth
- expo-sensors, expo-camera, expo-location

---

[Privacy Policy](privacy-policy/)
