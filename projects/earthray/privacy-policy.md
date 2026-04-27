---
layout: default
title: EarthRay — Privacy Policy
---

# EarthRay — Privacy Policy

**Effective date:** April 23, 2026

## Who We Are

EarthRay is developed by Anton Skrynnik (antonjuls), an independent developer based in the Netherlands.

## Data We Collect and Why

| Data                                | Purpose                                                     | Storage                                                                                                                                                        |
| ----------------------------------- | ----------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Precise location (GPS)              | Core app functionality — ray calculation, globe positioning | **Used locally only.** Location is fuzzed by ~30 km before being sent to the relay server or other users. Precise coordinates are never transmitted or stored. |
| Device orientation (motion sensors) | Determines phone pointing direction for AR ray              | Real-time local processing only, never stored or transmitted                                                                                                   |
| Camera feed                         | AR overlay display                                          | Local display only — never recorded, transmitted, or stored                                                                                                    |
| Display name (user-chosen)          | Shown to other session participants                         | Relay server, in-memory only, active session — auto-deleted on disconnect                                                                                      |

## Authentication

EarthRay uses anonymous session-only authentication. On joining a room, the relay server issues a random anonymous user ID and a short-lived session token (self-issued JWT, 1-hour expiry). No email, password, phone number, or real identity is collected. The anonymous ID and token are held only in device memory during the session and are not persisted after the session ends.

## Legal Basis for Processing (GDPR)

- **Consent** (Art. 6(1)(a)) — Location access requires explicit device permission before any data is processed.
- **Legitimate interest** (Art. 6(1)(f)) — Processing device orientation and session data is necessary for core app functionality (real-time AR ray visualization).

## What We Do NOT Collect

No analytics. No crash reporting. No advertising or tracking SDKs. No IDFA. No cookies. No contacts. No photos. No health data. No browsing history. No purchase data.

## Third-Party Services

- **Fly.io** — used to host the EarthRay relay server, which synchronizes session state between participants in real time. The relay holds session data only in memory; it has no database, does not log user state, and does not retain anything after disconnect. The relay server is currently hosted in Fly.io's Ashburn (US East) region. Fly.io operates under the EU–US Data Privacy Framework and Standard Contractual Clauses.
- **Expo Updates (EAS)** — used for over-the-air app updates. No personal data is transmitted to Expo.

## Data Sharing

Fuzzed location and ray data are shared only with users in the same session (requires a room code) and with Fly.io as the hosting provider of the relay server. No data is sold to third parties. No advertising. No profiling.

## Data Retention

The relay server holds session data only in memory for the duration of an active session. On disconnect, the server removes the user from the room after a brief grace period (~10 seconds, to tolerate momentary network blips) and broadcasts the departure to remaining participants. When the last user leaves a room, the room itself is deleted from memory after ~30 seconds of idleness. No data is written to disk, no database is used, and server restarts clear all state. There are no persistent user accounts. Local device storage (AsyncStorage) holds only session preferences such as room code and display name.

## Children

EarthRay is not directed at children under 13. No data is knowingly collected from children.

## Your Rights (GDPR)

The developer is based in the EU (Netherlands). Under GDPR, you have the right to:

- Access, correct, or delete your personal data
- Restrict or object to processing
- Lodge a complaint with the Dutch Data Protection Authority (Autoriteit Persoonsgegevens) at [autoriteitpersoonsgegevens.nl](https://autoriteitpersoonsgegevens.nl)

In practice, all session data is already auto-deleted on disconnect. For any requests, contact the developer.

## Changes to This Policy

If this policy is updated, the effective date at the top will be revised. Continued use of the app after changes constitutes acceptance.

## Contact

For privacy inquiries, contact: **[antonjuls@gmail.com](mailto:antonjuls@gmail.com)**
