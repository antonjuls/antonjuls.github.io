---
layout: default
title: EarthRay — Project Specification
---

# EarthRay — Project Specification v1.1

**Author:** Anton Skrynnik (antonjuls) — Independent developer, Netherlands

**Status:** Prototype (functional, tested on iOS + Android)

**Goal:** Explore Earth's geometry through real-time AR ray visualization, shared between users anywhere on the planet

---

## Concept

EarthRay is a real-time multiplayer mobile application in which users at different geographic locations simultaneously visualize:

1. **A 3D globe** showing their own position and the approximate positions of all other connected users (fuzzed by ~30 km — precise GPS is never shared)
2. **An AR camera view** in which each user sees a colored ray originating from the rear camera of their phone, projecting outward — through the Earth if necessary — toward wherever the phone is pointing
3. **The rays of other users** rendered in their own AR space, entering from the correct physical direction as determined by GPS and device orientation

The ray always originates from the rear camera lens, perpendicular to the phone's screen, in the exact direction the camera is pointing — like a laser pointer. When a user in Amsterdam points their phone downward, their ray passes through the Earth and exits on the other side. A user in New York sees that ray entering their AR space from below, at the geometrically correct angle.

<img src="{{ '/assets/images/earthray/earthray-concept.svg' | relative_url }}" alt="EarthRay concept — ray from User A passes through Earth to User B" style="width:100%; max-width:600px; margin: 1.5rem 0;">

---

## What The App Is

EarthRay is an **interactive, multiplayer AR toy**. Point your phone somewhere, see a ray go there, and see where your friend is pointing theirs — across the room or across the planet.

The app respects real physics — GPS coordinates, device orientation, and geodesic geometry are all calculated correctly. A natural side effect: any two users on a spherical Earth are always **below each other's horizon** — both must tilt their phones downward to point at each other. The farther apart they are, the steeper the angle. Only when very close together does the other user approach the horizon line.

### Eratosthenes Readout

When two users are connected, the app displays the angle between their surface normals. Combined with the distance between them (Haversine formula), this gives the circumference of the Earth — the same calculation Eratosthenes did in 240 BC with a stick and a shadow, now with two smartphones.

```
Circumference = (arc_distance / angle_between_normals) × 360°
```

---

## Glossary

**WGS84** — World Geodetic System 1984. Standard model of Earth's shape used by GPS. Earth modeled as ellipsoid: semi-major axis a = 6,378,137.0 m, semi-minor axis b = 6,356,752.3 m.

**ECEF** — Earth-Centered, Earth-Fixed. Cartesian coordinate system with origin at Earth's center. X-axis points to intersection of equator and prime meridian, Z-axis points to North Pole, Y-axis completes right-handed system.

**ENU** — East-North-Up. Local coordinate system of an observer. Unique to each surface point — ENU in Tokyo and Amsterdam are different systems.

**LLA** — Latitude, Longitude, Altitude. Output of GPS: latitude φ (−90° to +90°), longitude λ (−180° to +180°), altitude h (meters above ellipsoid).

**Antipode** — The point on Earth's surface diametrically opposite a given point. Computed as (−φ, λ ± 180°).

**Quaternion** — A way to represent 3D rotation without gimbal lock. q = (w, x, y, z) where w is scalar part, (x,y,z) is vector part.

**IMU** — Inertial Measurement Unit. Sensor suite: accelerometer, gyroscope, magnetometer. Together they yield full device orientation in space.

**Surface Normal** — Vector perpendicular to the surface at a given point. On the WGS84 ellipsoid: n = [cos(φ)cos(λ), cos(φ)sin(λ), sin(φ)].

<img src="{{ '/assets/images/earthray/surface-normals.svg' | relative_url }}" alt="Surface normals at different locations on Earth's surface" style="width:100%; max-width:500px; margin: 1.5rem 0;">

**Ray** — A directed half-line originating from the rear camera of a phone, perpendicular to the screen plane, extending in the direction the camera is pointing.

---

## Mathematics

### Block 1 — GPS (LLA) → ECEF

**WGS84 constants:**

```
a  = 6,378,137.0 m           (semi-major axis)
b  = 6,356,752.314245 m      (semi-minor axis)
e² = 1 − (b²/a²)
   = 0.00669437999014        (first eccentricity squared)
```

**Radius of curvature in the prime vertical N(φ):**

```
N(φ) = a / √(1 − e²·sin²(φ))
```

**LLA → ECEF:**

```
X = (N(φ) + h) · cos(φ) · cos(λ)
Y = (N(φ) + h) · cos(φ) · sin(λ)
Z = (N(φ)·(1 − e²) + h) · sin(φ)
```

where φ = latitude in radians, λ = longitude in radians, h = altitude in meters.

**ECEF → LLA (Bowring's iterative method):**

```
lon = atan2(y, x)
p   = sqrt(x² + y²)
phi_0 = atan2(z, p·(1 − e²))          (initial estimate)

Iterate 4×:
  N(phi) = a / sqrt(1 − e²·sin²(phi))
  phi    = atan2(z + e²·N·sin(phi), p)

alt  = p/cos(phi) − N                  (away from poles)
     = z/sin(phi) − N·(1−e²)           (near poles)
```

**Example — Amsterdam (52.3676°N, 4.9041°E, h=0):**

```
N(φ) ≈ 6,388,111 m
X ≈  3,881,596 m
Y ≈    334,000 m
Z ≈  5,051,070 m
```

---

### Block 2 — ECEF → ENU

**ENU basis vectors in ECEF for observer at (φ, λ):**

```
ê_E = [−sin(λ),              cos(λ),             0      ]
ê_N = [−sin(φ)·cos(λ),      −sin(φ)·sin(λ),      cos(φ) ]
ê_U = [ cos(φ)·cos(λ),       cos(φ)·sin(λ),      sin(φ) ]
```

**Rotation matrix ECEF → ENU:**

```
       ┌ −sin(λ)          cos(λ)          0      ┐
R_ENU =│ −sin(φ)cos(λ)   −sin(φ)sin(λ)   cos(φ) │
       └  cos(φ)cos(λ)    cos(φ)sin(λ)   sin(φ) ┘
```

**Transform vector from ECEF to ENU:**

```
v_E = −vx·sin(λ)        + vy·cos(λ)
v_N = −vx·sin(φ)cos(λ)  − vy·sin(φ)sin(λ)  + vz·cos(φ)
v_U =  vx·cos(φ)cos(λ)  + vy·cos(φ)sin(λ)  + vz·sin(φ)
```

---

### Block 3 — Direction Vector A→B

To find the direction User A must point to "hit" User B:

```
d_ECEF = P_B − P_A = (Xb−Xa, Yb−Ya, Zb−Za)

d̂_ECEF = d_ECEF / |d_ECEF|        (normalize)

d_ENU = R_ENU_A · d̂_ECEF          (transform to A's local frame)

Azimuth   = atan2(d_E, d_N)        → degrees, range [0°, 360°)
Elevation = atan2(d_U, √(d_E²+d_N²)) → degrees, range [−90°, +90°]
```

If Elevation < 0, the ray goes below the horizon (through the Earth).

**Example — Amsterdam → New York (40.7128°N, 74.0060°W):**

```
Azimuth   ≈ 292°   (northwest)
Elevation ≈ −23°   (below horizon — through Earth)
```

**Example — Amsterdam → Tokyo (35.6762°N, 139.6503°E):**

```
Azimuth   ≈  33°   (northeast)
Elevation ≈ −18°   (below horizon — through Earth)
```

---

### Block 4 — Device Orientation: IMU → Rotation Matrix

The platform's native sensor fusion (CoreMotion on iOS, SensorManager on Android) combines accelerometer, gyroscope, and magnetometer into a fused orientation.

`expo-sensors` DeviceMotion API returns `rotation: { alpha, beta, gamma }` — Euler angles in radians. The rotation matrix is built in ZXY intrinsic order:

```
R = Rz(α) × Rx(β) × Ry(γ)
```

**Platform-specific alpha correction:**

- **iOS (CMAttitude):** NWU convention, offset by +π/2 to align with ENU: `α = rawAlpha + π/2`
- **Android (SensorManager):** W3C DeviceOrientation convention, already ENU: `α = rawAlpha`

**Basis vector extraction from R:**

```
ray_ENU         = R · [0, 0, −1] = −column3    (rear camera forward)
cameraRight_ENU = R · [1, 0, 0]  = column1
cameraUp_ENU    = R · [0, 1, 0]  = column2
```

Expanded (same formulas for both platforms after alpha correction):

```
ray.x   = −(cos(α)·sin(γ) + sin(α)·sin(β)·cos(γ))
ray.y   =  cos(α)·sin(β)·cos(γ) − sin(α)·sin(γ)
ray.z   = −cos(β)·cos(γ)

cameraRight.x = cos(α)·cos(γ) − sin(α)·sin(β)·sin(γ)
cameraRight.y = sin(α)·cos(γ) + cos(α)·sin(β)·sin(γ)
cameraRight.z = −cos(β)·sin(γ)

cameraUp.x = −sin(α)·cos(β)
cameraUp.y =  cos(α)·cos(β)
cameraUp.z =  sin(β)
```

---

### Block 5 — Orientation Vectors: ENU → ECEF

Three vectors extracted from the rotation matrix are transformed from ENU to ECEF via R_ENU^T:

```
v_ECEF_x = −v_E·sin(λ) − v_N·sin(φ)cos(λ) + v_U·cos(φ)cos(λ)
v_ECEF_y =  v_E·cos(λ) − v_N·sin(φ)sin(λ) + v_U·cos(φ)sin(λ)
v_ECEF_z =               v_N·cos(φ)        + v_U·sin(φ)
```

**ray_ECEF** is broadcast to all other users in the session via the relay server (WebSocket). **cameraRight_ECEF** and **cameraUp_ECEF** are used locally for AR scene camera orientation.

---

### Block 6 — Ray Exit Point

**Parametric ray equation:**

```
P(t) = P_origin + t · d̂
```

**Intersection with sphere R = 6,371,000 m:**

```
a = 1
b = 2 · dot(P_origin, d̂)
c = |P_origin|² − R²

discriminant = b² − 4c
t_exit = (−b + √discriminant) / 2     (positive root = forward direction)

P_exit_ECEF = P_origin + t_exit · d̂
```

**Convert P_exit back to LLA:**

```
lon_exit = atan2(P_exit_y, P_exit_x)
p        = √(P_exit_x² + P_exit_y²)
lat_exit = atan2(P_exit_z, p · (1 − e²))
```

---

### Block 7 — Full Ray Geometry

The ray is a **free half-line** originating from the user's phone rear camera. Key points computed along the ray:

```
P_start   = P_origin                       (user's position on surface)
P_exit    = P_origin + t_exit · d̂         (where ray exits Earth)
P_beyond  = P_origin + RAY_LENGTH · d̂     (RAY_LENGTH ≈ 14,016 km)

Closest point to Earth's center (geometric, not an anchor):
P_closest = P_origin + t_c · d̂   where t_c = −dot(P_origin, d̂)
```

Rendered as a polyline `[P_start, P_exit, P_beyond]` with color gradient. Segment inside Earth rendered semi-transparent.

---

### Block 8 — Rendering Another User's Ray (AR View)

User B receives `{ecef, ray, normal, color}` from User A via the relay server.

**Camera orientation (updated each frame):**

```
1. Read ECEF orientation vectors: ray, cameraRight, cameraUp
2. Apply EMA smoothing (factor 0.45) to reduce jitter
3. Gram-Schmidt re-orthogonalization:
   ray' = normalize(smoothed_ray)
   cameraRight' = normalize(cameraRight - dot(cameraRight, ray')·ray')
   cameraUp' = cross(cameraRight', ray')
4. Build Three.js camera matrix:
   Column 0 (X/right) = cameraRight'
   Column 1 (Y/up)    = cameraUp'
   Column 2 (Z)       = -ray' (camera looks along -Z in Three.js)
```

**Remote user placement:**

```
1. Compute direction: d = normalize(P_remote - P_local)
2. Apply EMA smoothing to direction
3. Place marker sphere at d × SCENE_RADIUS (10 units)
4. Extend ray line from marker in remote user's ray direction
5. Three.js camera projection handles all screen-space mapping
```

---

### Block 9 — Angle Between Normals (Eratosthenes)

**Surface normals in ECEF:**

```
n_A = [cos(φ_A)cos(λ_A),  cos(φ_A)sin(λ_A),  sin(φ_A)]
n_B = [cos(φ_B)cos(λ_B),  cos(φ_B)sin(λ_B),  sin(φ_B)]
```

**Angle between normals:**

```
cos(θ) = dot(n_A, n_B) = cos(φ_A)cos(φ_B)cos(λ_A−λ_B) + sin(φ_A)sin(φ_B)
θ = acos(dot(n_A, n_B))
```

**Haversine distance:**

```
Δφ = φ_B − φ_A
Δλ = λ_B − λ_A

a = sin²(Δφ/2) + cos(φ_A)·cos(φ_B)·sin²(Δλ/2)
c = 2·atan2(√a, √(1−a))

distance = R · c    (R = 6,371,000 m)
```

**Earth circumference from two phones:**

```
circumference = (distance / θ) × 360°
Expected result: ~40,075 km
```

---

### Block 10 — Earth's Center in AR Space

Earth's center in ECEF = [0, 0, 0]. Approximately 6,371 km below every user.

```
center_relative = −P_user_ECEF
center_ENU      = R_ENU · center_relative

center_ENU.U ≈ −6,371,000 m    (always below)
```

Rendered as a billboard quad with a radial gradient shader — bright red-orange core fading to transparent edge. Placed at SCENE_RADIUS distance in the nadir direction.

---

### Block 11 — Relay Protocol

Real-time sync runs over WebSocket against a self-hosted relay server (Node.js, in-memory rooms, no database). All messages are JSON; a binary protocol is a post-launch optimization.

**Join** (once per session):

```typescript
interface JoinMessage {
  type: "join";
  sessionId: string; // 6-char room code
  displayName: string;
  color: string; // hex for this user's ray
}
```

On successful join, the server issues a short-lived anonymous JWT and returns the current peer list. Name and color are sent **once at join** and do not repeat in every update.

**Publish** (~5 Hz, 200 ms interval):

```typescript
interface PublishMessage {
  type: "publish";
  state: {
    // GPS position (fuzzed ~30 km before transmission)
    lat: number;
    lon: number;
    alt: number;

    // ECEF position
    ecef: { x: number; y: number; z: number };

    // Ray direction in ECEF (unit vector from rear camera)
    ray: { x: number; y: number; z: number };

    // Surface normal in ECEF
    normal: { x: number; y: number; z: number };
  };
}
```

**Broadcast to peers** (`userUpdate` frame from server):

```typescript
interface UserState {
  userId: string; // assigned by relay at join
  name: string; // carried from join
  color: string; // carried from join
  timestamp: number; // server time, ms since epoch

  lat: number;
  lon: number;
  alt: number;
  ecef: { x: number; y: number; z: number };
  ray: { x: number; y: number; z: number };
  normal: { x: number; y: number; z: number };
}
```

Bandwidth per publish frame: ~120 bytes. At 5 Hz, a 5-person room produces roughly 0.6 KB/s upstream per user and ~2.4 KB/s downstream per user (peers).

---

### Block 12 — Solar & Lunar Position

**Sun position (simplified algorithm, ~1° accuracy):**

Days since J2000.0 epoch:

```
d = (timestamp_ms − Date.UTC(2000, 0, 1, 12, 0, 0)) / 86,400,000
```

Solar coordinates:

```
M = (357.5291 + 0.98560028·d) mod 360°    (mean anomaly)
C = 1.9148·sin(M) + 0.02·sin(2M) + 0.0003·sin(3M)
λ = (M + C + 180 + 102.9372) mod 360°     (ecliptic longitude)
ε = 23.4393°                               (obliquity of ecliptic)
```

Equatorial coordinates:

```
RA  = atan2(cos(ε)·sin(λ), cos(λ))
Dec = asin(sin(ε)·sin(λ))
```

Greenwich Mean Sidereal Time:

```
GMST = (280.1470 + 360.9856235·d) mod 360°
```

Direction in ECEF:

```
HA = GMST − RA
x = cos(Dec)·cos(HA)
y = cos(Dec)·sin(HA)
z = sin(Dec)
```

**Moon position (simplified, ~2° accuracy):**

```
L = (218.316 + 13.176396·d) mod 360°    (mean longitude)
M = (134.963 + 13.064993·d) mod 360°    (mean anomaly)
F = (93.272 + 13.22935·d) mod 360°      (mean distance)

λ = L + 6.289·sin(M)
β = 5.128·sin(F)
```

Then same ecliptic → equatorial → ECEF pipeline as sun.

**Moon phase:**

```
phase = acos(dot(sunDir, moonDir))    (0 = new moon, π = full moon)
```

---

## Technical Architecture

### Stack

```
React Native + Expo (managed workflow)
├── expo-location              GPS → LLA
├── expo-sensors               DeviceMotion → Euler angles
├── expo-camera                live camera feed (AR background)
├── react-three-fiber          3D rendering (globe + AR scene)
├── drei                       Three.js helpers
├── WebSocket relay server     real-time sync, anonymous JWT auth, remote config
│   (self-hosted Node.js + `ws`, in-memory only)
├── Zustand                    state management
├── i18next                    internationalization
├── react-native-reanimated    gesture animation
├── react-native-gesture-handler pan/pinch gestures
├── burnt                      toast notifications
└── TypeScript                 all code
```

### Why Sensor-Based AR (Not ARKit/ARCore)

This project uses **sensor-based AR** (IMU + GPS + math), not **vision-based AR** (SLAM). ARKit/ARCore scan the environment to detect surfaces and anchor virtual objects to physical surfaces. That is exactly what we do NOT want — rays are anchored to compass direction in global space, not to the physical room.

### Data Flow

<img src="{{ '/assets/images/earthray/data-flow.svg' | relative_url }}" alt="EarthRay data flow — sensors through the relay server to peer rendering" style="width:100%; max-width:640px; margin: 1.5rem 0;">

---

## Quick Reference Formulas

```
LLA → ECEF:
  N    = a / √(1 − e²sin²φ)
  X    = (N+h)cosφcosλ
  Y    = (N+h)cosφsinλ
  Z    = (N(1−e²)+h)sinφ

ECEF → ENU:
  E    = −Xsinλ + Ycosλ
  N    = −Xsinφcosλ − Ysinφsinλ + Zcosφ
  U    =  Xcosφcosλ + Ycosφsinλ + Zsinφ

Direction A→B:
  d    = normalize(P_B − P_A)
  d_ENU = R_ENU_A · d
  Az   = atan2(d_E, d_N)
  El   = atan2(d_U, √(d_E²+d_N²))

Ray exit point:
  b    = 2·dot(P_origin, d̂)
  c    = |P_origin|² − R²
  t    = (−b + √(b²−4c)) / 2
  P_exit = P_origin + t·d̂

Eratosthenes:
  θ    = acos(dot(n_A, n_B))
  circ = (haversine_distance / θ) × 360°

Orientation (Euler → ENU):
  iOS: α = rawAlpha + π/2    Android: α = rawAlpha
  ray.x   = −(cosα·sinγ + sinα·sinβ·cosγ)
  ray.y   =  cosα·sinβ·cosγ − sinα·sinγ
  ray.z   = −cosβ·cosγ

Constants:
  a    = 6,378,137.0 m
  b    = 6,356,752.314245 m
  e²   = 0.00669437999014
  R    = 6,371,000 m  (mean spherical radius)
```

---

_EarthRay — Specification v1.1. All rights reserved. Anton Skrynnik (antonjuls), 2026._
