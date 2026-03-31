---
layout: default
title: EarthRay — Privacy Policy
---

# EarthRay — Privacy Policy

**Effective date:** March 31, 2026

## Who We Are

EarthRay is developed by Anton Skrynnik (antonjuls), an independent developer based in the Netherlands.

## Data We Collect and Why

| Data                                | Purpose                                                     | Storage                                                      |
| ----------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| Precise location (GPS)              | Core app functionality — ray calculation, globe positioning | **Used locally only.** Location is fuzzed by ~30 km before being sent to Firebase or other users. Precise coordinates are never transmitted or stored. |
| Device orientation (motion sensors) | Determines phone pointing direction for AR ray              | Real-time local processing only, never stored or transmitted |
| Camera feed                         | AR overlay display                                          | Local display only — never recorded, transmitted, or stored  |
| Display name (user-chosen)          | Shown to other session participants                         | Firebase, active session only — auto-deleted on disconnect   |

## Authentication

EarthRay uses Firebase Anonymous Authentication. No email, password, phone number, or real identity is collected.

## What We Do NOT Collect

No analytics. No crash reporting. No advertising or tracking SDKs. No IDFA. No cookies. No contacts. No photos. No health data. No browsing history. No purchase data.

## Third-Party Services

- **Firebase Realtime Database & Authentication** (Google) — used for multiplayer session sync and anonymous authentication
- **Expo Updates (EAS)** — used for over-the-air app updates

## Data Sharing

Location and ray data are shared only with users in the same session (requires a room code). No data is sold to third parties. No advertising. No profiling.

## Data Retention

All session data is deleted automatically on disconnect via Firebase `onDisconnect` handlers. There are no persistent user accounts. Local device storage (AsyncStorage) holds only session preferences such as room code and display name.

## Children

EarthRay is not directed at children under 13. No data is knowingly collected from children.

## GDPR

The developer is based in the EU (Netherlands). Users may request data deletion by contacting the developer, though session data is already auto-deleted on disconnect.

## Contact

For privacy inquiries, contact: **antonjuls@gmail.com**
