# LabGuideSession8

# 🔹 Part 1/5 — Project Setup (Unity 6.2 • URP • Meta XR SDK only • Quest 2/3)

## ✅ To-Do Checklist (with why)

### 1) Create the project

* ⬜ **1.1** Unity Hub → **New → 3D (URP)** → name `VR_Raycast_Lab`

  * • URP is the most stable + performant path on Quest.

### 2) Switch platform & compression

* ⬜ **2.1** File → **Build Settings → Android → Switch Platform**

  * • Quest runs Android; switching now avoids later reimports.
* ⬜ **2.2** In the same window set **Texture Compression = ASTC**

  * • Best memory/quality trade-off for mobile VR.

### 3) Player Settings (Project Settings → Player → Other Settings)

* ⬜ **3.1** **Package Name** `com.company.vr_lab`

  * • Required for install/signing.
* ⬜ **3.2** **Minimum API Level ≥ 29 (Android 10)**, **Target = latest installed**

  * • Matches current Meta requirements.
* ⬜ **3.3** **Scripting Backend = IL2CPP**, **ARM64** (only)

  * • Required for store + best perf.
* ⬜ **3.4** **Graphics API = Vulkan** (uncheck GLES3 unless you have a reason)

  * • Usually faster on Quest 2/3 with modern Meta SDKs.

### 4) Install Meta XR (keep a single runtime)

* ⬜ **4.1** Window → **Package Manager**

  * ✅ Install **XR Plug-in Management**
  * ✅ Install **Meta XR (Oculus) plug-in provider** (a.k.a. Oculus)
  * ✅ Import **Meta XR All-in-One SDK** (latest; v65+)
* ⬜ **4.2** Project Settings → **XR Plug-in Management → Android**

  * ✅ **Enable Meta XR**
  * ❌ **Do NOT enable OpenXR**
  * • Avoid mixed runtimes / warnings.

### 5) Project Validation (Meta auto-fix)

* ⬜ **5.1** Edit → Project Settings → **XR Plug-in Management → Project Validation** → **Fix All**

  * • Applies recommended flags (permissions/input backends, etc.).

### 6) URP performance baseline

* ⬜ **6.1** Select the **URP Asset** (e.g., *Mobile_RPAsset*)

  * **6.1.1** **HDR = OFF**
  * **6.1.2** **MSAA = 4x**
  * **6.1.3** **Shadows**: 512 res / 20–25 m / 2 cascades
  * **6.1.4** Disable heavy PostFX (Bloom/Motion Blur/Vignette)
* ⬜ **6.2** Select **URP Renderer** (e.g., *Mobile_Renderer*)

  * **6.2.1** **Post-processing = OFF**
  * **6.2.2** **Transparent Receive Shadows = OFF**
  * **6.2.3** **Renderer Features = (empty)**
  * • Clean 72–90 FPS baseline on Quest 2.

### 7) Meta runtime tuning (provider)

* ⬜ **7.1** **Stereo Rendering = Single-Pass Instanced**

  * • Two eyes in one pass = big GPU savings.
* ⬜ **7.2 (optional)** **Foveated Rendering = Conservative**

  * • Free perf; watch peripheral blur.

### 8) URP “pink fix” guard (one-time)

* ⬜ **8.1** Edit → Render Pipeline → URP → **Upgrade Project Materials to URP**

  * • Converts legacy/BiRP shaders that would render pink.
* ⬜ **8.2** Project Settings → **Graphics → Scriptable Render Pipeline Settings = your URP Asset**

  * • Ensures URP is actually active.

---

# 🔹 Part 2/5 — Scene, Rig, Rays, Cube, and World-Space UI

## ✅ To-Do Checklist (with why)

### 1) Foldering & scene

* ⬜ **1.1** Create folders: `Assets/Scenes`, `Assets/Scripts`, `Assets/Prefabs`, `Assets/Materials`.
* ⬜ **1.2** File → **New Scene** → Save as `Assets/Scenes/01_Raycast_Objects_UI.unity`
* ⬜ **1.3** Delete **Main Camera**

  * • The rig supplies the tracked VR camera.

### 2) Rig & managers (Meta Interaction SDK pattern)

* ⬜ **2.1** Create Empty **`XRRoot`** (0,0,0) — keep VR hierarchy clean.
* ⬜ **2.2** Project search **OVRCameraRig** → drag under `XRRoot`.
* ⬜ **2.3** Add **OVRManager** (to `XRRoot` or the rig root).
* ⬜ **2.4** Under the rig’s **TrackingSpace**, add **OVRControllers** (Left/Right).
* ⬜ **2.5** Create Empty **`XRInteractionManagerGO`** → **Add Component → XR Interaction Manager** (Meta ISDK).
* ⬜ **2.6** GameObject → UI → **Event System**

  * **2.6.1** Select it → **Add Component → Pointable Canvas Module**
  * • Bridges XR pointer to Unity UI.

### 3) Controller rays

* ⬜ **3.1** Select **LeftController** → **Add Component → RayInteractor**

  * **3.1.1** Also add **XR Interactor Line Visual** (or use the provided visual child if present).
  * **3.1.2** **Max Ray Length ≈ 6 m**, **Hide When No Interactable = ON**
* ⬜ **3.2** Repeat for **RightController**.

> (Optional) If your prefab exposes an **Interactor Group** slot for “Best Hand/Controller”, add the RayInteractor there so it participates in the group logic.

### 4) Ground & the first 3D target (learn the pattern)

* ⬜ **4.1** 3D Object → **Plane** at (0,0,0)
* ⬜ **4.2** 3D Object → **Cube** (name **`RayObject`**)

  * **4.2.1** Scale (0.2,0.2,0.2), Position (0,0.5,2)
* ⬜ **4.3** Make it ray-interactable

  * **4.3.1** Add **Rigidbody** (Use Gravity ON; Is Kinematic may be ON while selected)
  * **4.3.2** Add **Grabbable** (drag its Rigidbody)
  * **4.3.3** Add **ColliderSurface** (assign the BoxCollider)
  * **4.3.4** Add **RayInteractable**

    * **Pointable Element = Grabbable**, **Surface = ColliderSurface**
  * **4.3.5** Add a **MovementProvider** and assign it in **RayInteractable → Movement Provider**

    * *MoveFromTargetProvider* (follows the ray hit) or *MoveTowardsTargetProvider* (pulls toward controller)

### 5) World-Space UI (ray-clickable)

* ⬜ **5.1** UI → **Canvas** (name **`UI_Canvas`**)

  * **5.1.1** **Render Mode = World Space**
  * **5.1.2** Position (0,1.5,2.2), **Scale (0.001,0.001,0.001)**
* ⬜ **5.2** Inside `UI_Canvas` → UI → **Button (TextMeshPro)** (name **`RaycastButton`**)

  * **5.2.1** Label “Click Me”
* ⬜ **5.3** Ensure **Event System** has **Pointable Canvas Module**
* ⬜ **5.4 (recommended)** Layer separation

  * **5.4.1** Create Layer **UI_VR**, assign to `UI_Canvas`
  * **5.4.2** Each **RayInteractor → Interaction Mask** includes **Default** + **UI_VR**
  * • Prevents ambiguous hits when UI overlaps 3D.

### 6) Sanity snapshot (Hierarchy should resemble)

```
XRRoot
└── OVRCameraRig
    └── TrackingSpace
        ├── OVRControllers
        │   ├── LeftController (RayInteractor + Line Visual)
        │   └── RightController (RayInteractor + Line Visual)
        └── ...
XRInteractionManagerGO (XR Interaction Manager)
EventSystem (Pointable Canvas Module)
Plane
RayObject (Rigidbody, Grabbable, ColliderSurface, RayInteractable, MovementProvider)
UI_Canvas (World Space, Button TMP)
```

---

# 🔹 Part 3/5 — Light Switch (ray “Select” → toggle a Point Light)

## ✅ To-Do Checklist (with why)

### 1) Scene objects

* ⬜ **1.1** Light → **Point Light** (name **`RoomLight`**), pos (0,3,0), **Enabled = OFF**

  * • You’ll clearly see on/off when toggled.
* ⬜ **1.2** 3D Object → **Cube** (name **`LightSwitch`**), Scale (0.1,0.1,0.05), pos (0.5,1.5,2.0)

  * • A simple “button” mesh target.

### 2) Make the switch ray-selectable

* ⬜ **2.1** Ensure **BoxCollider** exists (Is Trigger **OFF**)
* ⬜ **2.2** Add **ColliderSurface** (assign BoxCollider)
* ⬜ **2.3** Add **Grabbable** (Rigidbody optional for static switch)
* ⬜ **2.4** Add **RayInteractable**

  * **Pointable Element = Grabbable**, **Surface = ColliderSurface**
  * • We only need click/select; no movement provider.

### 3) Script the toggle

* ⬜ **3.1** Create `Assets/Scripts/LightSwitchToggle.cs` and paste:

```csharp
using UnityEngine;
using Oculus.Interaction; // PointerEvent, RayInteractable, PointerEventType

public class LightSwitchToggle : MonoBehaviour
{
    [SerializeField] private Light _targetLight;
    private RayInteractable _ray;

    private void Awake()
    {
        _ray = GetComponent<RayInteractable>();
        if (_ray == null) Debug.LogError("RayInteractable required on LightSwitch.");
    }

    private void OnEnable()  { if (_ray != null) _ray.WhenPointerEventRaised += OnPointer; }
    private void OnDisable() { if (_ray != null) _ray.WhenPointerEventRaised -= OnPointer; }

    private void OnPointer(PointerEvent evt)
    {
        if (evt.Type == PointerEventType.Select && _targetLight != null)
            _targetLight.enabled = !_targetLight.enabled;
    }
}
```

* ⬜ **3.2** Add this component to **`LightSwitch`**, assign **`RoomLight`** in Inspector.
* ⬜ **3.3** Playtest: aim ray at switch → press trigger → light toggles.

---

# 🔹 Part 4/5 — Basketball Mini-Game (grab via ray, throw velocity, score UI)

## ✅ To-Do Checklist (with why)

### 1) Hoop (collision + trigger)

* ⬜ **1.1** Create Empty **`BasketballHoop`** at (0,3,5)
* ⬜ **1.2** Children:

  * **1.2.1** `Backboard` (Cube) with BoxCollider (solid)
  * **1.2.2** `Rim` (thin Torus/Cylinder ring) with colliders (solid)
  * **1.2.3** `HoopTrigger` (thin Box/Cylinder under rim) → **Is Trigger = ON**, **Tag = HoopTrigger**
* ⬜ **1.3 (physics tunneling guard)**

  * On **Basketball** (later), set **Rigidbody → Collision Detection = Continuous Dynamic**
  * On rim/backboard colliders, **Continuous Speculative**
  * • Reduces “passing through” at high speeds.

### 2) Ball (ray-grabbable)

* ⬜ **2.1** 3D Object → **Sphere** (name **`Basketball`**), Scale (0.22,0.22,0.22), pos (0,1.3,1.0)
* ⬜ **2.2** Add **Rigidbody** (Mass 1, Use Gravity ON, Collision Detection **Continuous Dynamic**)
* ⬜ **2.3** Add **Grabbable** (drag Rigidbody)
* ⬜ **2.4** Add **ColliderSurface** (assign SphereCollider)
* ⬜ **2.5** Add **RayInteractable** (Pointable=Grabbable, Surface=ColliderSurface)
* ⬜ **2.6** Add **MovementProvider** (*MoveFromTargetProvider* or *MoveTowardsTargetProvider*), assign in RayInteractable

  * • While selected, the movement provider drives pose; on release, physics takes over.

### 3) Score UI

* ⬜ **3.1** In `UI_Canvas` add **Text (TextMeshPro)** (name **`ScoreText`**) → initial text **“Score: 0”**

### 4) Scripts

**4.1** `Assets/Scripts/ScoreManager.cs` (attach to Empty **`GameManager`**, assign `ScoreText`)

```csharp
using UnityEngine;
using TMPro;

public class ScoreManager : MonoBehaviour
{
    [SerializeField] private TextMeshProUGUI _scoreText;
    private int _score;

    private void OnEnable()  { BasketballThrow.OnScore += Add; }
    private void OnDisable() { BasketballThrow.OnScore -= Add; }

    private void Start() { UpdateUI(); }
    private void Add()   { _score++; UpdateUI(); }

    private void UpdateUI()
    {
        if (_scoreText != null) _scoreText.text = $"Score: {_score}";
    }
}
```

**4.2** `Assets/Scripts/BasketballThrow.cs` (attach to **`Basketball`**, assign **Spawn Point**)

```csharp
using System.Collections.Generic;
using UnityEngine;
using Oculus.Interaction; // RayInteractable, PointerEvent, PointerEventType

[RequireComponent(typeof(Rigidbody))]
[RequireComponent(typeof(RayInteractable))]
public class BasketballThrow : MonoBehaviour
{
    [Header("Throw Tuning")]
    [SerializeField] private float _throwForceScale = 1.2f; // overall scalar
    [SerializeField] private int   _velocitySamples = 6;    // frames to average
    [SerializeField] private Transform _spawnPoint;         // respawn point
    [SerializeField] private float _fallResetY = -5f;       // auto-reset

    private Rigidbody _rb;
    private RayInteractable _ray;
    private bool _held;
    private readonly Queue<(Vector3 p, float t)> _trace = new();

    public static event System.Action OnScore;

    private void Awake()
    {
        _rb  = GetComponent<Rigidbody>();
        _ray = GetComponent<RayInteractable>();
        if (_ray == null) Debug.LogError("RayInteractable required on Basketball.");
    }

    private void OnEnable()  { if (_ray != null) _ray.WhenPointerEventRaised += OnPointer; }
    private void OnDisable() { if (_ray != null) _ray.WhenPointerEventRaised -= OnPointer; }

    private void Update()
    {
        if (_held)
        {
            _trace.Enqueue((transform.position, Time.time));
            while (_trace.Count > _velocitySamples) _trace.Dequeue();
        }
        if (!_held && transform.position.y < _fallResetY) ResetBall();
    }

    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("HoopTrigger"))
        {
            OnScore?.Invoke();
            ResetBall();
        }
    }

    private void OnPointer(PointerEvent evt)
    {
        if (evt.Type == PointerEventType.Select)
        {
            _held = true;
            _rb.isKinematic = true; // movement via MovementProvider while held
            _trace.Clear();
            _trace.Enqueue((transform.position, Time.time));
        }
        else if (evt.Type == PointerEventType.Unselect)
        {
            Vector3 v = EstimateVelocity();
            _held = false;
            _rb.isKinematic = false;
            _rb.velocity = Vector3.zero;
            _rb.angularVelocity = Vector3.zero;
            _rb.AddForce(v * _throwForceScale, ForceMode.VelocityChange);
            _trace.Clear();
        }
    }

    private Vector3 EstimateVelocity()
    {
        if (_trace.Count < 2) return Vector3.zero;
        Vector3 firstP = default, lastP = default;
        float firstT = 0f, lastT = 0f;
        int i = 0;
        foreach (var (p, t) in _trace)
        {
            if (i == 0) { firstP = p; firstT = t; }
            lastP = p; lastT = t; i++;
        }
        float dt = Mathf.Max(1e-4f, lastT - firstT);
        return (lastP - firstP) / dt;
    }

    public void ResetBall()
    {
        if (_spawnPoint != null) transform.position = _spawnPoint.position;
        _rb.velocity = Vector3.zero;
        _rb.angularVelocity = Vector3.zero;
        _rb.isKinematic = false;
    }
}
```

### 5) Wire & test

* ⬜ **5.1** Create Empty **`BasketballSpawnPoint`** at (0,1.3,1.0); assign in `BasketballThrow`
* ⬜ **5.2** Ensure `HoopTrigger` **Tag = HoopTrigger** and **Is Trigger = ON**
* ⬜ **5.3** Add `ScoreManager` to **`GameManager`**, assign **`ScoreText`**
* ⬜ **5.4** Play:

  * **5.4.1** Aim ray → **Select** the ball → move with ray (provider)
  * **5.4.2** **Unselect** with a flick → ball flies (velocity applied)
  * **5.4.3** Through **HoopTrigger** → **Score** increments, ball **respawns**

---

# 🔹 Part 5/5 — Optional Filters, QA Checks, Build & Profiling

## A) Per-controller masking (left=3D, right=UI)

* ⬜ **A.1** On **LeftController** RayInteractor → **Add Component: Tag Set Filter**

  * Required Tag = `3D`, Exclude = `UI`
* ⬜ **A.2** On **RightController** RayInteractor → Tag Set Filter

  * Required Tag = `UI`, Exclude = `3D`
* ⬜ **A.3** On `RayObject`’s interaction child → **Add Tag Set = `3D`**
* ⬜ **A.4** On `UI_Canvas`’s interaction child → **Add Tag Set = `UI`**
* ⬜ **A.5** In each RayInteractor → **Optionals → Interactable Filters** → add the respective **Tag Set Filter**

  * • Clean separation: left ray = 3D only, right ray = UI only.

## B) Quick QA checklist (in-editor)

* ⬜ **B.1** Rays show only when aiming at interactables (**Hide When No Interactable = ON**).
* ⬜ **B.2** `RayObject` follows the chosen **MovementProvider** while selected.
* ⬜ **B.3** UI button clicks via ray; **Pointable Canvas Module** present.
* ⬜ **B.4** Light toggles on **Select**; `RoomLight` assigned.
* ⬜ **B.5** Ball releases with believable velocity; tweak **_throwForceScale** (1.0–1.4) & **_velocitySamples** (4–8).
* ⬜ **B.6** Score increments on hoop trigger entry; respawn works.
* ⬜ **B.7** Physics tunneling minimized (ball **Continuous Dynamic**, rims **Speculative**).

## C) Common fixes

* **No ray visible** → RayInteractor on controller? **Max Length** ≥ distance? Aiming at valid target?
* **UI not clickable** → Canvas **World Space**, **Pointable Canvas Module** present; Canvas **Scale ~0.001**; Ray **Interaction Mask** includes UI layer.
* **Cube inert** → On cube: **Grabbable + ColliderSurface + RayInteractable + MovementProvider (assigned)**.
* **Switch no toggle** → `LightSwitchToggle` on switch, `RoomLight` assigned, **RayInteractable** present.
* **Ball “falls through”** → Collision Detection settings as in **1.3**; ensure colliders are not triggers (except `HoopTrigger`).
* **Spam logs about microgestures** → Disable Microgestures features in Meta samples if unused (harmless).

## D) Build to Quest (ADB/Link)

* ⬜ **D.1** Player Settings → Company/Product, Splash off, Multithreaded Rendering ON
* ⬜ **D.2** Quality/Graphics → URP Asset assigned
* ⬜ **D.3** Build Settings → **Add Open Scenes** → **Build & Run** (headset in **Developer Mode**, Link/Air Link/USB)
* ⬜ **D.4 (optional)** Profiler/Frame Debugger → verify CPU/GPU times; expect stable 72–90 FPS in this simple scene.

---

## 📋 One-Page Copy/Paste Mini-Checklist

* **Platform**: Android • **XR**: Meta XR only • **URP**: HDR Off, MSAA 4x • **Stereo**: Single-Pass Instanced
* **Scene**: Plane, `RayObject` (Rigid+Grabbable+ColliderSurface+RayInteractable+MovementProvider), `UI_Canvas` (World Space + Button TMP)
* **Rig**: OVRCameraRig (+ OVRManager), **RayInteractor** on L/R controllers, **XR Interaction Manager**, **EventSystem + Pointable Canvas Module**
* **Light**: `RoomLight` OFF, `LightSwitch` (ColliderSurface + Grabbable + RayInteractable + `LightSwitchToggle`)
* **Basketball**: Sphere (Rigid Continuous Dynamic + Grabbable + ColliderSurface + RayInteractable + MovementProvider + `BasketballThrow`), `BasketballSpawnPoint`
* **Hoop**: solids for backboard/rim + **`HoopTrigger` (Is Trigger ON, Tag=HoopTrigger)**
* **UI**: `ScoreText` TMP + `ScoreManager`

---
