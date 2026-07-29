---
layout: default
title: EarthRay — Project Specification
---

# EarthRay — Project Specification

## Concept

EarthRay is two things in one.

**A social toy.** Connect with someone anywhere on the planet, see them as a coloured sphere in your AR camera, and watch their ray sweep through space as they move their phone. _"They're pointing somewhere — and I can see it move, right now"_ makes a person on the other side of the world feel physically present in your room.

**A science experiment.** The same setup, between two distant users, lets you re-derive Earth's circumference with nothing but two phones — the same measurement Eratosthenes made in 240 BC, now live. To point at a friend in New York from Amsterdam, you have to tilt your phone _down_: the planet is between you. The farther apart you stand, the steeper the tilt. That angle is the curvature of the Earth, made physically obvious.

Both layers are simultaneously true. The math underneath is real — WGS84, ECEF, sensor fusion — but the experience is _"there's my friend, and I can almost feel them through this glass."_

<img src="{{ '/assets/images/earthray/earthray-concept.svg' | relative_url }}" alt="EarthRay concept — ray from User A passes through Earth to User B" style="width:100%; max-width:600px; margin: 1.5rem 0;">

---

## What You Experience

When two or more users join the same session, the app renders three synchronized views in real time — the same instant for everyone, regardless of distance.

### The Globe

A 3D globe centered on your own (GPS-fuzzed) position, showing every connected user as a marker on the surface, plus their rays projecting outward into space. Useful for _seeing the geometry_ — distance, direction, who's pointing where, and how the rays carve through the planet.

### Your AR Camera

Your rear camera feed, with your own ray rendered as a beam emerging from the lens, perpendicular to the screen, in the direction the camera is pointing — like a laser pointer. It always exits _forward_; if your friend is on the other side of the Earth, the beam passes through the planet and continues out the back.

### Other Users in AR

The same camera view, with every other connected user appearing as a coloured sphere placed in your AR space at the geometrically correct angle and distance, based on their GPS position relative to yours. Their rays render too, entering your space from wherever they're pointing. When they move their phone, the ray sweeps in real time. _"There they are."_

### Sun and Moon

The Sun and Moon are also rendered on the globe and in the AR scene, at astronomically correct positions for the current time. A small grace note: when you point upward at the actual sun in the sky, the in-app sun is right there.

---

## The Eratosthenes Experiment

When two users connect, the app displays the angle between their _surface normals_ — the vectors pointing straight up out of the ground at each location. Combined with the great-circle distance between them (Haversine), this gives the circumference of the Earth:

```
circumference = (distance_AB / angle_between_normals) × 360°
```

This is the same calculation Eratosthenes performed in 240 BC, when he compared the shadow of a stick in Alexandria to the shadow at Syene 800 km south and computed Earth's circumference to within a few percent. With two smartphones, modern GPS, and the same formula, you get ~40,075 km — the modern accepted value. The Earth is round; you can prove it from your sofa.

---

## How It Works

Each phone runs the same loop, ~5 times per second:

1. **Read sensors.** GPS gives latitude, longitude, altitude. The IMU (accelerometer, gyroscope, magnetometer) gives orientation in 3D space.
2. **Convert to a shared frame.** GPS coordinates are projected onto the WGS84 ellipsoid model of the Earth and converted to Earth-Centered, Earth-Fixed (ECEF) Cartesian coordinates. Device orientation is mapped from the phone's local frame into ECEF as well.
3. **Build the ray.** A unit vector is computed pointing outward from the rear camera in ECEF coordinates — that's _"the ray."_
4. **Broadcast.** Position, orientation, and ray vector are sent to a relay server over WebSocket. Coordinates are _fuzzed by ~30 km_ before transmission — your precise location never leaves the device.
5. **Receive peers.** The server forwards everyone else's state. Each phone uses its own GPS-derived position to convert peer rays from ECEF into its own local AR space, and renders them.

The same loop, on every connected phone, every 200 ms. No phone is the source of truth. Every device computes its own world independently, from a shared, low-precision broadcast.

---

## Technical Architecture

### Stack

```
React Native + Expo (managed workflow)
├── expo-location              GPS → latitude / longitude / altitude
├── expo-sensors               DeviceMotion → Euler angles
├── expo-camera                live camera feed (AR background)
├── react-three-fiber          3D rendering (globe + AR scene)
├── drei                       Three.js helpers
├── Zustand                    state management
├── i18next                    internationalization
├── react-native-reanimated    gesture animation
├── react-native-gesture-handler  pan/pinch gestures
├── burnt                      toast notifications
└── TypeScript                 throughout

Self-hosted relay server: Node.js + ws, in-memory only,
anonymous JWT auth, no database, no persistence.
```

### Why Sensor-Based AR (Not ARKit / ARCore)

EarthRay uses **sensor-based AR** — IMU + GPS + math — not vision-based AR (SLAM). ARKit and ARCore scan the environment to detect surfaces and anchor virtual objects to physical ones. That is exactly what we _don't_ want here: rays are anchored to compass direction in _global space_, not to the table in front of you. Sensor-based AR also runs identically across iOS and Android, with no platform-specific anchor handling.

### Data Flow

<img src="{{ '/assets/images/earthray/data-flow.svg' | relative_url }}" alt="EarthRay data flow — sensors through the relay server to peer rendering" style="width:100%; max-width:640px; margin: 1.5rem 0;">

### Relay Protocol

WebSocket against a self-hosted Node.js relay. All messages are JSON; rooms live in memory only and disappear when the last peer disconnects.

**Join** (once per session):

```typescript
interface JoinMessage {
  type: "join";
  sessionId: string; // 6-char room code
  displayName: string;
  color: string; // hex for this user's ray
}
```

The server returns a short-lived anonymous JWT and the current peer list.

**Publish** (~5 Hz, 200 ms interval):

```typescript
interface PublishMessage {
  type: "publish";
  state: {
    lat: number; // GPS, fuzzed ~30 km before transmission
    lon: number;
    alt: number;
    ecef: { x: number; y: number; z: number }; // ECEF position
    ray: { x: number; y: number; z: number }; // unit vector, ECEF, from rear camera
    normal: { x: number; y: number; z: number }; // surface normal at this user, ECEF
  };
}
```

**Broadcast** (server → all peers, frame name `userUpdate`):

```typescript
interface UserState {
  userId: string; // assigned by relay at join
  name: string; // carried from join
  color: string; // carried from join
  timestamp: number; // server time, ms since epoch
  // ...same fields as PublishMessage.state
}
```

Each publish frame is roughly 120 bytes. At 5 Hz, a 5-person room produces ~0.6 KB/s upstream and ~2.4 KB/s downstream per user.

---

## Mathematics

The formulas the app implements, organised by purpose. Worked numerical examples are omitted; the equations stand on their own. Together with the architecture above, this is enough to verify correctness.

### WGS84 Constants

```
a    = 6,378,137.0 m              semi-major axis
b    = 6,356,752.314245 m         semi-minor axis
e²   = 1 − (b²/a²) ≈ 0.00669438   first eccentricity squared
R    = 6,371,000 m                mean spherical radius
N(φ) = a / √(1 − e²·sin²(φ))      prime-vertical radius of curvature
```

### Position: GPS (LLA) → ECEF

```
X = (N(φ) + h)·cos(φ)·cos(λ)
Y = (N(φ) + h)·cos(φ)·sin(λ)
Z = (N(φ)·(1 − e²) + h)·sin(φ)
```

with φ = latitude (rad), λ = longitude (rad), h = altitude (m).

### Local Frame: ECEF → ENU

ENU (East-North-Up) is each user's local Cartesian frame. For an observer at (φ, λ):

```
       ┌ −sin(λ)         cos(λ)         0      ┐
R_ENU =│ −sin(φ)cos(λ)  −sin(φ)sin(λ)  cos(φ) │
       └  cos(φ)cos(λ)   cos(φ)sin(λ)   sin(φ) ┘
```

Transform a vector v from ECEF to ENU: `v_ENU = R_ENU · v_ECEF`. The inverse (ENU → ECEF) is `R_ENU^T`.

### Direction A → B (azimuth, elevation)

```
d̂      = (P_B − P_A) / |P_B − P_A|       // unit vector, ECEF
d_ENU   = R_ENU_A · d̂                    // local frame at A
azimuth   = atan2(d_E, d_N)
elevation = atan2(d_U, √(d_E² + d_N²))
```

If `elevation < 0`, the ray points below A's horizon — through the Earth.

### Device Orientation: Euler → ENU Ray

`expo-sensors` returns Euler angles `(α, β, γ)` in ZXY intrinsic order. Platform offset:

- **iOS** (CMAttitude, NWU convention): `α = α_raw + π/2`
- **Android** (W3C, already ENU): `α = α_raw`

The rear-camera forward vector (`R · [0, 0, −1]`) in ENU:

```
ray.E = −(cos(α)·sin(γ) + sin(α)·sin(β)·cos(γ))
ray.N =   cos(α)·sin(β)·cos(γ) − sin(α)·sin(γ)
ray.U = − cos(β)·cos(γ)
```

Camera-right (`R·[1,0,0]`) and camera-up (`R·[0,1,0]`) are extracted similarly and used for AR scene orientation.

### Surface Normal

The unit vector pointing straight up out of the ellipsoid at (φ, λ), in ECEF:

```
n = [cos(φ)cos(λ),  cos(φ)sin(λ),  sin(φ)]
```

<img src="{{ '/assets/images/earthray/surface-normals.svg' | relative_url }}" alt="Surface normals at different locations on Earth's surface" style="width:100%; max-width:500px; margin: 1.5rem 0;">

### Ray Exit Point (intersection with sphere of radius R)

```
b      = 2·dot(P_origin, d̂)
c      = |P_origin|² − R²
t_exit = (−b + √(b² − 4c)) / 2
P_exit = P_origin + t_exit · d̂
```

### Eratosthenes (Earth's circumference from two phones)

Already given in _The Eratosthenes Experiment_ above. For reference:

```
θ           = acos(dot(n_A, n_B))                          angle between surface normals
distance_AB = 2·R·atan2(√a, √(1−a))                        haversine
   where a  = sin²(Δφ/2) + cos(φ_A)·cos(φ_B)·sin²(Δλ/2)

circumference = (distance_AB / θ) × 360°                   expected: ~40,075 km
```

---

## Glossary

**WGS84** — World Geodetic System 1984. The standard model GPS uses for Earth's shape: an ellipsoid with semi-major axis 6,378,137 m and semi-minor axis 6,356,752.3 m.

**ECEF** — Earth-Centered, Earth-Fixed. A Cartesian coordinate system with origin at Earth's center. X-axis points to the intersection of the equator and the prime meridian; Z-axis to the North Pole; Y completes a right-handed system.

**ENU** — East-North-Up. The local Cartesian frame of an observer on the surface. Unique to each surface point — ENU in Tokyo is a different basis from ENU in Amsterdam.

**Surface normal** — The unit vector perpendicular to Earth's surface at a given point. On the WGS84 ellipsoid: `n = [cos(φ)cos(λ), cos(φ)sin(λ), sin(φ)]`.

**Antipode** — The point on Earth's surface diametrically opposite a given point. For (φ, λ): `(−φ, λ ± 180°)`.

**Ray** — In EarthRay terminology: a directed half-line originating at the rear camera of a phone, perpendicular to the screen plane, extending in the direction the camera is pointing.

---

_EarthRay. All rights reserved. Anton Skrynnik (antonjuls), 2026._
