---
title: Termojet
description: Mobile companion app for HVAC and heating professionals — browse a catalog of manifolds, pumps, hydraulic separators and other boiler-room components, design installation schemes, validate connections, and export the result as a PDF.
tags: [React Native, Redux Toolkit, Firebase, i18n, iOS, Android]
---

Termojet is a mobile companion app for HVAC and heating professionals, built for [Termojet](https://termojet.com.ua/) — a Ukrainian heating-equipment brand. It bundles a searchable catalog of boiler-room equipment — manifolds, pumps, hydraulic separators, valves and the rest of an HVAC installation — a visual scheme editor for laying out the system, connection validation that catches incompatible wiring before it leaves the office, and one-tap PDF export so installers can hand a clean spec sheet to clients on site.

## Highlights

- **Equipment catalog** spanning manifolds, pumps, hydraulic separators, valves and other HVAC / boiler-room components, with rich filtering and detail views, sourced from a Firebase-backed dataset
- **Visual scheme editor** — drag, connect, and annotate components on a pinch-to-zoom canvas
- **Connection validation** flags incompatible device combinations as the scheme is built
- **PDF export** of the assembled scheme, with optional company / location / contact fields filled in locally on the device
- **Six languages**: English, Ukrainian, Polish, Romanian, French, and Czech
- **No accounts required** — Firebase Anonymous Auth gates Firestore access, no email or password collected
- **Offline-friendly** — Redux + redux-persist keep the catalog and in-progress schemes available without a connection
- **Cross-platform** native builds for iOS and Android

## Tech Stack

- React Native 0.78 (bare workflow, iOS + Android)
- Redux Toolkit + redux-persist
- Firebase (Auth, Firestore, Remote Config, Analytics, Crashlytics, Storage) via `@react-native-firebase/*` modular API
- React Navigation (stack)
- i18next + react-i18next
- Formik + Yup for forms
- `expo-print` + `expo-sharing` for PDF generation
- Fastlane for release automation

## Screenshots

<div class="screenshots">
  <img src="{{ '/assets/images/termojet/1.jpg' | relative_url }}" alt="Termojet screenshot 1" loading="lazy" />
  <img src="{{ '/assets/images/termojet/2.png' | relative_url }}" alt="Termojet screenshot 2" loading="lazy" />
  <img src="{{ '/assets/images/termojet/3.png' | relative_url }}" alt="Termojet screenshot 3" loading="lazy" />
  <img src="{{ '/assets/images/termojet/4.png' | relative_url }}" alt="Termojet screenshot 4" loading="lazy" />
</div>

## Available on

- [App Store](https://apps.apple.com/us/app/termojet/id1569103051)
- [Google Play](https://play.google.com/store/apps/details?id=com.termojet&hl=en)
