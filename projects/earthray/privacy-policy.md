---
layout: default
title: EarthRay — Privacy Policy
---

# EarthRay — Privacy Policy

**Effective date:** April 29, 2026

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
- **Performance of a contract** (Art. 6(1)(b)) — Relaying your chosen display name and ray state to the other participants of a session you joined is necessary to provide the multiplayer experience you requested.
- **Legitimate interest** (Art. 6(1)(f)) — Processing device orientation and other session data is necessary for core app functionality (real-time AR ray visualization), and for protecting the relay server against abuse (rate limiting, anti-flooding).

## What We Do NOT Collect

No analytics. No crash reporting. No advertising or tracking SDKs. No IDFA. No cookies. No contacts. No photos. No health data. No browsing history. No purchase data.

## Third-Party Services

- **Cloud hosting provider** — used to host the EarthRay relay server, which synchronizes session state between participants in real time. The relay holds session data only in memory; it has no database, does not log user state, and does not retain anything after disconnect. Depending on the deployment region, session data may be processed in countries outside the European Economic Area. Where this is the case, transfers are protected by the EU–US Data Privacy Framework and the European Commission's Standard Contractual Clauses (SCCs).
- **Expo Updates (EAS)** — used for over-the-air app updates. No personal data is transmitted to Expo.

## Data Sharing

Fuzzed location and ray data are shared only with users in the same session (requires a room code) and with the cloud hosting provider of the relay server. No data is sold to third parties. No advertising. No profiling.

## Data Retention

The relay server holds session data only in memory for the duration of an active session. On disconnect, the server removes the user from the room after a brief grace period (~10 seconds, to tolerate momentary network blips) and broadcasts the departure to remaining participants. When the last user leaves a room, the room itself is deleted from memory after ~30 seconds of idleness. No data is written to disk, no database is used, and server restarts clear all state. There are no persistent user accounts. Local device storage (AsyncStorage) holds only session preferences — your chosen display name, the last room code you used, a boolean flag indicating whether you were previously connected (used to restore the join screen on reopen), and the current remote-config snapshot served by the relay.

## Children

EarthRay is intended for general audiences. The app does not knowingly collect personal information from children under 13. Children may use the app under the supervision of a parent or legal guardian, who is responsible for ensuring use is appropriate.

## Your Rights (GDPR)

The developer is based in the EU (Netherlands). Under GDPR, you have the right to:

- Access, correct, or delete your personal data
- Restrict or object to processing
- Lodge a complaint with the Dutch Data Protection Authority (Autoriteit Persoonsgegevens) at [autoriteitpersoonsgegevens.nl](https://autoriteitpersoonsgegevens.nl)

In practice, all session data is already auto-deleted on disconnect. For any requests, contact the developer.

## International Users

EarthRay is available worldwide. The data practices described above apply to all users regardless of location. Specifically:

- **EU / EEA residents** have the GDPR rights listed above. The Dutch DPA (Autoriteit Persoonsgegevens) is the lead supervisory authority.
- **UK residents** have equivalent rights under the UK GDPR; the supervisory authority is the Information Commissioner's Office (ICO) at [ico.org.uk](https://ico.org.uk).
- **California residents** have rights under the CCPA / CPRA, including the right to know, delete, and opt out of "sales" of personal information. EarthRay does not sell personal information, does not share it for cross-context behavioral advertising, and does not use sensitive personal information for inferring characteristics.
- **Other jurisdictions** — where local data protection law grants additional rights, those rights are honored. Contact the developer to exercise them.

## Changes to This Policy

If this policy is updated, the effective date at the top will be revised. Continued use of the app after changes constitutes acceptance.

## Contact

For privacy inquiries, contact:

**Anton Skrynnik**
[antonjuls@gmail.com](mailto:antonjuls@gmail.com)
