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

EarthRay uses Firebase Anonymous Authentication. A random anonymous user ID is generated for the session — no email, password, phone number, or real identity is collected. The anonymous ID is not persisted after the session ends.

## Legal Basis for Processing (GDPR)

- **Consent** (Art. 6(1)(a)) — Location access requires explicit device permission before any data is processed.
- **Legitimate interest** (Art. 6(1)(f)) — Processing device orientation and session data is necessary for core app functionality (real-time AR ray visualization).

## What We Do NOT Collect

No analytics. No crash reporting. No advertising or tracking SDKs. No IDFA. No cookies. No contacts. No photos. No health data. No browsing history. No purchase data.

## Third-Party Services

- **Firebase Realtime Database & Authentication** (Google) — used for multiplayer session sync and anonymous authentication. Firebase servers may be located outside the EU (including the US). Google operates under the EU–US Data Privacy Framework and Standard Contractual Clauses.
- **Expo Updates (EAS)** — used for over-the-air app updates. No personal data is transmitted to Expo.

## Data Sharing

Fuzzed location and ray data are shared only with users in the same session (requires a room code) and with Google as a data processor (Firebase). No data is sold to third parties. No advertising. No profiling.

## Data Retention

All session data is deleted automatically on disconnect via Firebase `onDisconnect` handlers. There are no persistent user accounts. Local device storage (AsyncStorage) holds only session preferences such as room code and display name.

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

For privacy inquiries, contact: **antonjuls@gmail.com**
