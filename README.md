# Lab Guide Session 8
# Lab Guide Session 8

---

## 🟣 **Session Homework — Complete All Session Tasks**

> **All sections marked “*session task*” are now your homework!**  
> To make these stand out, they are highlighted in **purple** below.  
> **Complete every 🟣 Session Homework section as your assignment.**  
> (Other checklist items remain as reference, but homework submission = all purple tasks below.)

---

# 🔹 Part 3/5 — Light Switch Button (toggle Spotlight) <span style="color:purple;font-weight:bold;">🟣 Session Homework</span>

## ✅ Goal

Click a world-space **Button** with the ray to **toggle a Spot Light**.

### 1) Add the light

* 🟣 **1.1** **Hierarchy:** Create → Light → **Spot Light** (name **`RoomSpot`**)

  * **Inspector (Light):** uncheck **enabled** (start OFF), tune **Range/Angle/Intensity**.

### 2) Add the script

* 🟣 **2.1** **Project:** `Assets/Scripts/LightSwitchController.cs`

```csharp
using UnityEngine;

public class LightSwitchController : MonoBehaviour
{
    public GameObject lightObject; // assign the Spot Light GameObject

    public void ToggleLight()
    {
        if (lightObject) lightObject.SetActive(!lightObject.activeSelf);
    }
}
```

### 3) Wire in the Inspector

* 🟣 **3.1** **Hierarchy:** Create Empty → **`SceneManager`** (root)

  * **Inspector (SceneManager):** **Add Component → LightSwitchController**
  * **LightSwitchController → Light Object**: drag **`RoomSpot`** (GameObject)
* 🟣 **3.2** **Hierarchy:** `UI_Canvas/RaycastButton`

  * **Inspector (Button):** **OnClick() → +** → drag **`SceneManager`** → choose **LightSwitchController.ToggleLight()**
* 🟣 **3.3** **Parent/child change?** None.

---

# 🔹 Part 4/5 — Basketball Hoop Scoring (UI score increases on goal) <span style="color:purple;font-weight:bold;">🟣 Session Homework</span>

## ✅ Goal

Grab/throw a **basketball**; when it passes through the **hoop**, the **Score** UI increments.

### 1) Backboard + rim (with child trigger)

* 🟣 **1.1** **Hierarchy:** 3D Object → **Cube** (name **`Backboard`**)

  * **Inspector (Transform):** **Scale (1,1.2,0.05)**, **Position (0,1.6,3)**
* 🟣 **1.2** **Hierarchy:** 3D Object → **Torus** *(or thin Cylinder ring)* (name **`Rim`**)

  * **Inspector (Transform):** **Position (0,1.4,2.8)**, radius ≈ **0.23–0.25**
  * **Hierarchy:** drag **`Rim`** onto **`Backboard`** → **child** `Backboard/Rim`
  * **Inspector (Rim):** **Add Component → MeshCollider** (Convex OFF, not a trigger)
* 🟣 **1.3** **Hierarchy:** 3D Object → **Cylinder** (name **`ScoreTrigger`**)

  * **Hierarchy:** drag under **`Backboard/Rim`** → **child** `Backboard/Rim/ScoreTrigger`
  * **Inspector (Transform):** **Scale (~0.42, 0.05, 0.42)**; place **just below** rim plane
  * **Inspector (Collider):** **Cylinder Collider**, **Is Trigger = ON**

### 2) Basketball (grab + physics)

* 🟣 **2.1** **Hierarchy:** 3D Object → **Sphere** (name **`Basketball`**)

  * **Inspector (Transform):** **Scale (0.24,0.24,0.24)**
* 🟣 **2.2** **Inspector (Basketball):**

  * **Add Component → Rigidbody** (Mass **0.62**, Drag **0.05**, Angular Drag **0.05**)
  * (Optional) Assign **Physic Material** (Bounciness ~**0.6**, Combine **Maximum**) to SphereCollider
  * **Add Component → Grabbable** → drag **Basketball (Rigidbody)** into **Grabbable → Rigidbody**
  * **Add Component → ColliderSurface** → drag **Basketball (SphereCollider)**
  * **Add Component → GrabInteractable** *(or Hand Grab Interactable)*
* 🟣 **2.3** **Tag setup**

  * **Edit → Project Settings → Tags and Layers → Tags → + →** add **`Basketball`**
  * **Inspector (Basketball → Tag):** set **Basketball**

> **Wizard alternative:** Right-click `Basketball` → **Interaction SDK → Add Grab Interaction**
> • **Creates a parent** (e.g., `ISDK Hand Grab Interaction`) → sphere becomes its **child**; most components live on parent.

### 3) Score UI

* 🟣 **3.1** **Hierarchy:** UI → **Canvas** (name **`ScoreCanvas`**)

  * **Inspector (Canvas):** **World Space**, **Position (-0.9,1.8,2.2)**, **Scale (0.001,0.001,0.001)**
* 🟣 **3.2** **Hierarchy:** `ScoreCanvas` → UI → **Text (TextMeshPro)** (name **`ScoreText`**)

  * **Inspector (Text):** “Score: 0”

### 4) Scripts

* 🟣 **4.1** **Project:** `Assets/Scripts/ScoreManager.cs`

```csharp
using UnityEngine;
using TMPro;

public class ScoreManager : MonoBehaviour
{
    public TMP_Text scoreText;
    public int pointsPerGoal = 1;
    private int _score;

    void Start() => UpdateUI();

    public void AddScore(int amount)
    {
        _score += amount;
        UpdateUI();
    }

    public void ResetScore()
    {
        _score = 0;
        UpdateUI();
    }

    private void UpdateUI()
    {
        if (scoreText) scoreText.text = $"Score: {_score}";
    }
}
```

* 🟣 **4.2** **Project:** `Assets/Scripts/HoopTrigger.cs`

```csharp
using UnityEngine;

[RequireComponent(typeof(Collider))]
public class HoopTrigger : MonoBehaviour
{
    public ScoreManager scoreManager;
    public string ballTag = "Basketball";

    private void Reset()
    {
        var c = GetComponent<Collider>();
        if (c) c.isTrigger = true;
    }

    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag(ballTag) && scoreManager)
            scoreManager.AddScore(scoreManager.pointsPerGoal);
    }
}
```

### 5) Wiring (Inspector targets)

* 🟣 **5.1** **Hierarchy:** **`SceneManager`** → **Inspector:** **Add Component → ScoreManager**

  * **ScoreManager → Score Text**: drag **`ScoreCanvas/ScoreText`**
* 🟣 **5.2** **Hierarchy:** `Backboard/Rim/ScoreTrigger` → **Inspector:** **Add Component → HoopTrigger**

  * **HoopTrigger → Score Manager**: drag **`SceneManager`**

---

# 🔹 Part 5/5 — Playtest, (Optional) Masking, Build & Troubleshooting <span style="color:purple;font-weight:bold;">🟣 Session Homework</span>

## 🟣 **A) Playtest**

* 🟣 **A.1** Enter **Play** with Quest Link/AirLink. Rays appear only on valid targets (Hide When No Interactable = ON).
* 🟣 **A.2** Click **`UI_Canvas/RaycastButton`** → `RoomSpot` toggles.
* 🟣 **A.3** Throw **Basketball** through **`Backboard/Rim/ScoreTrigger`** → **Score** increments.

## 🟣 **B) (Optional) Masking: Left = 3D only, Right = UI only**

* 🟣 **B.1** **LeftController** (object under **OVRController**): **Add Component → Tag Set Filter**

  * **Required Tag = 3DObject**, **Exclude Tag = UICanvas**
* 🟣 **B.2** **3D interactable host** (manual=`RayObject`; wizard=its **parent**): **Add Component → Tag Set → Tag = 3DObject**
* 🟣 **B.3** **RightController**: **Add Component → Tag Set Filter**

  * **Required Tag = UICanvas**, **Exclude Tag = 3DObject**
* 🟣 **B.4** **Canvas interactable host** (manual=`UI_Canvas`; wizard=its helper): **Add Component → Tag Set → Tag = UICanvas**
* 🟣 **B.5** **Reference filters inside each RayInteractor**

  * **LeftController/ControllerInteractors/Controller Ray Interactor → Inspector → Interactable Filters → +** → drag **LeftController (Tag Set Filter)**
  * **RightController/ControllerInteractors/Controller Ray Interactor → …** → drag **RightController (Tag Set Filter)**

## 🟣 **C) Troubleshooting**

* 🟣 **No ray** → Check Ray Interactor component, line visual, and that you’re pointing at interactables.
* 🟣 **UI not clickable** → Canvas **World Space**, `EventSystem` has **Pointable Canvas Module**, `UI_Canvas` has **Tracked Device Graphic Raycaster**, scale ~0.001.
* 🟣 **3D not reacting** → On the **host**: **RayInteractable**, **Grabbable**, **ColliderSurface**, **MovementProvider** set and assigned in **RayInteractable**.
* 🟣 **Light button** → Button.OnClick → **SceneManager → LightSwitchController.ToggleLight()**; script has **RoomSpot** assigned.
* 🟣 **No score** → `ScoreTrigger` **Is Trigger = ON**, **HoopTrigger.ScoreManager** set, ball **Tag = Basketball**, trigger slightly **below** rim.

## 🟣 **D) Build for Quest**

* 🟣 **D.1** Build Settings → **Android**
* 🟣 **D.2** **Development Build** (first tests)
* 🟣 **D.3** **Build & Run** to headset

---

# 🔹 Part 6/6 — Controller Selection Masks (Tag Set Filter: Left = 3D only, Right = UI only) <span style="color:purple;font-weight:bold;">🟣 Session Homework</span>

> **Goal:** Make the **Left Controller** ray **select only 3D objects** and **ignore UI**; make the **Right Controller** ray **select only UI (Canvas)** and **ignore 3D**—exactly as shown in the video.  
> — **This is required homework.**

## 🟣 **1) Add the Tag Set Filter to each controller**

* 🟣 **1.1 — Left Controller (filter for 3D only)**

  * **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/LeftController`
  * **Inspector (LeftController):** **Add Component → Tag Set Filter**

    * **Required Tag =** `3DObject`
    * **Exclude Tag =** `UICanvas`
  * **Parent/child change?** None; you added a component to **LeftController**.

* 🟣 **1.2 — Right Controller (filter for UI only)**

  * **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/RightController`
  * **Inspector (RightController):** **Add Component → Tag Set Filter**

    * **Required Tag =** `UICanvas`
    * **Exclude Tag =** `3DObject`
  * **Parent/child change?** None; you added a component to **RightController**.

---

## 🟣 **2) Tag each interactable host (the objects your rays should hit)**

* 🟣 **2.1 — 3D Object host (for the cube)**

  * **Hierarchy Path (wizard):** `ISDK Ray Grab Interaction` (the **parent** created by the wizard)
  * **Hierarchy Path (manual):** your **`RayObject`** cube (same GO that has **RayInteractable**)
  * **Inspector (host GO):** **Add Component → Tag Set**

    * **Tag =** `3DObject`
  * **Parent/child:** unchanged—component added to the **host**.

* 🟣 **2.2 — UI Canvas host (for the world-space UI)**

  * **Hierarchy Path (manual UI):** `UI_Canvas` (the Canvas itself)
  * **Hierarchy Path (wizard UI):** the **Canvas helper** created by “Add Ray Interaction to Canvas” (if present; tag that helper or the Canvas—either works as long as the **RayInteractable** is present)
  * **Inspector (host GO):** **Add Component → Tag Set**

    * **Tag =** `UICanvas`
  * **Parent/child:** unchanged—component added to the **host**.

---

## 🟣 **3) Tell each Controller Ray Interactor which Filter to use**

* 🟣 **3.1 — Left Ray Interactor uses the LeftController’s filter**

  * **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/LeftController/ControllerInteractors/Controller Ray Interactor`
  * **Inspector (Controller Ray Interactor):** find **Optionals → Interactable Filters (list)**

    * Click **+**, then **drag `LeftController`** (the object that holds the **Tag Set Filter**) **into the new slot**.
  * **Parent/child:** unchanged—just filling a list field on the interactor.

* 🟣 **3.2 — Right Ray Interactor uses the RightController’s filter**

  * **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/RightController/ControllerInteractors/Controller Ray Interactor`
  * **Inspector (Controller Ray Interactor):** **Optionals → Interactable Filters → +**

    * **Drag `RightController`** (the object with the **Tag Set Filter**) **into the slot**.
  * **Parent/child:** unchanged—just filling a list field on the interactor.

---

## 🟣 **4) Quick playtest (what you should see)**

* 🟣 **Left Controller** ray **selects the cube** (tagged `3DObject`) and **passes through the UI** (tagged `UICanvas`).
* 🟣 **Right Controller** ray **selects the UI Canvas** and **ignores the cube**.
* 🟣 **If it doesn’t work:** see troubleshooting below—most issues are a missed **reference** (Step 3) or a **tag typo** (Step 2).

---

## 🟣 **Troubleshooting (exact spots to check)**

* 🟣 **Left ray still hits UI** → On **LeftController**, verify **Tag Set Filter** is **Required=3DObject**, **Exclude=UICanvas**. On the **Left Controller Ray Interactor**, confirm the **Interactable Filters** list includes the filter reference.
* 🟣 **Right ray still hits 3D** → On **RightController**, verify **Required=UICanvas**, **Exclude=3DObject**; on the **Right Controller Ray Interactor**, the **Interactable Filters** list includes the filter reference.
* 🟣 **Nothing is selectable** → Ensure the **interactable hosts** actually have **Tag Set** with **matching strings** (`3DObject` on the cube’s host; `UICanvas` on the Canvas host).
* 🟣 **Wizard confusion** → If you used the **Ray Grab wizard** on the cube, remember the **host** is the **wizard parent** (`ISDK Ray Grab Interaction`), not the mesh child. Put **Tag Set** on that parent.

---

> 🟣 **Homework Submission:**  
> Submit screenshots and/or video of ALL purple-highlighted steps above, demonstrating working ray UI, light toggle, basketball scoring, controller selection masks, and troubleshooting if needed.

---

# 🔹 Part 1/5 — Project Setup (Unity 6.2 • URP • Meta XR AIO SDK v65+ • Quest 2/3)

## ✅ To-Do Checklist (with why)

### 1) Create the project

* ⬜ **1.1** Unity Hub → **New → 3D (URP)** → name `VR_Raycast_Lab`

  * • URP is the most stable + performant path on Quest.

### 2) Switch platform & compression

* ⬜ **2.1** File → **Build Settings → Android → Switch Platform**

  * • Quest runs Android; switching now avoids later reimports.
* ⬜ **2.2** In the same window set **Texture Compression = ASTC**

  * • Best memory/quality for mobile VR.

### 3) Player Settings (Project Settings → Player → Other Settings)

* ⬜ **3.1** **Package Name** `com.company.vr_lab`

  * • Required for install/signing.
* ⬜ **3.2** **Minimum API Level ≥ 29**, **Target = latest installed**

  * • Matches current Meta requirements.
* ⬜ **3.3** **Scripting Backend = IL2CPP**, **ARM64** (only)

  * • Store-compliant + fastest.
* ⬜ **3.4** **Graphics API = Vulkan** (uncheck GLES3 unless needed)

  * • Usually faster on Quest 2/3.

### 4) Install Meta XR (single runtime only)

* ⬜ **4.1** Window → **Package Manager**

  * ✅ **XR Plug-in Management**
  * ✅ **Oculus (Meta XR) plug-in provider**
  * ✅ **Meta XR All-in-One SDK** (v65+)
* ⬜ **4.2** Project Settings → **XR Plug-in Management → Android**

  * ✅ **Enable Meta XR**
  * ❌ **Disable OpenXR**
  * • Avoid mixed runtimes / warnings.

### 5) Project Validation (auto-fix)

* ⬜ **5.1** Project Settings → **XR Plug-in Management → Project Validation** → **Fix All**

  * • Applies recommended flags (permissions, input backends, etc.).

### 6) URP performance baseline

* ⬜ **6.1** Select **URP Asset** (e.g., *Mobile_RPAsset*)

  * • **HDR = OFF**, **MSAA = 4×**, Shadows: **512 / 20–25 m / 2 cascades**, heavy PostFX **OFF**.
* ⬜ **6.2** Select **URP Renderer** (e.g., *Mobile_Renderer*)

  * • **Post-processing = OFF**, **Transparent Receive Shadows = OFF**, **Renderer Features = (empty)**.

### 7) Meta runtime tuning

* ⬜ **7.1** **Stereo Rendering = Single-Pass Instanced**

  * • Big GPU savings.
* ⬜ **7.2 (optional)** **Foveated Rendering = Conservative**

  * • Free perf; slight peripheral blur.

### 8) URP “pink fix” guard (one-time)

* ⬜ **8.1** Edit → Render Pipeline → URP → **Upgrade Project Materials to URP**

  * • Fixes legacy/BiRP pink materials.
* ⬜ **8.2** Project Settings → **Graphics → Scriptable Render Pipeline Settings = your URP Asset**

  * • Ensures URP is actually active.

---

# 🔹 Part 2/5 — Scene, Rig, Rays, Cube & World-Space UI

## ✅ To-Do Checklist (video transcript flow)

### 1) Foldering & scene

* ⬜ **1.1** Create folders: `Assets/Scenes`, `Assets/Scripts`, `Assets/Prefabs`, `Assets/Materials`.
* ⬜ **1.2** File → **New Scene** → save as `Assets/Scenes/01_Raycast_Objects_UI.unity`
* ⬜ **1.3** **Hierarchy:** select **Main Camera** → **Delete**

  * • The rig provides the tracked VR camera.

### 2) Add rig exactly as in the video (Project search path)

* ⬜ **2.1** **Project (Search):** type **OVRCameraRig** → drag **OVRCameraRig** into **Hierarchy (root)**

  * • **Parent/child:** adds `OVRCameraRig` at root.
* ⬜ **2.2** **Project (Search):** type **OVRInteraction** → drag **OVRInteraction** **as a child of** `OVRCameraRig`

  * • **Parent/child:** becomes `OVRCameraRig/OVRInteraction`.
  * • **Inspector (OVRInteraction):** no changes needed now.
* ⬜ **2.3** **Project (Search):** type **OVRController** → drag **OVRController** **inside** `OVRCameraRig/OVRInteraction`

  * • **Parent/child:** ends as `OVRCameraRig/OVRInteraction/OVRController`.
  * • This prefab provides **LeftController/RightController** sub-objects (and their **ControllerInteractors** children) so you can attach Ray Interactors next.
* ⬜ **2.4** **Connect your headset** (Link/Air Link) → **Play** to verify: you should see controllers.

> **Optional (Advanced) – Rig & managers (Meta Interaction SDK pattern)**
> Use this only if you want a hand-crafted hierarchy:
> ⬜ Create **`XRRoot`** (empty at 0,0,0) → move **OVRCameraRig** under it.
> ⬜ Add **OVRManager** on `XRRoot`.
> ⬜ Create **`XRInteractionManagerGO`** → **Add Component → XR Interaction Manager**.
> ⬜ (Alternative to prefab) Create your own **OVRControllers/LeftController/RightController** empties under `OVRCameraRig/TrackingSpace` to host interactors.
> • This mirrors our previous “advanced” setup and is great for large projects; not required for this lab.

### 3) Add Ray Interactors (exact places from the transcript)

* ⬜ **3.1** **Project (Search):** type **Controller Ray Interactor** (or **Ray Interactor** if that’s the prefab name in your SDK)
* ⬜ **3.2** **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/LeftController/ControllerInteractors`

  * **Drag** the **Controller Ray Interactor** **prefab** into this **ControllerInteractors** object.
  * **Inspector (on the newly added Controller Ray Interactor):**

    * **Max Ray Length = 6**
    * **Hide When No Interactable = ON** *(if the property is under a “Visuals/ControllerRay” child, select that child and toggle there)*
* ⬜ **3.3** **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/RightController/ControllerInteractors`

  * Repeat the same drag & Inspector settings.

### 4) Ground & first 3D target (manual, clear Inspector)

* ⬜ **4.1** **Hierarchy:** 3D Object → **Plane** (name **`Floor`**) at (0,0,0)
* ⬜ **4.2** **Hierarchy:** 3D Object → **Cube** (name **`RayObject`**)

  * **Inspector (Transform):** **Scale (0.2,0.2,0.2)**, **Position (0,0.5,2)**
* ⬜ **4.3** **Inspector (RayObject):**

  * **Add Component → Rigidbody** (Use Gravity ON, Interpolate)
  * **Add Component → Grabbable** → drag **RayObject (Rigidbody)** into **Grabbable → Rigidbody**
  * **Add Component → ColliderSurface** → drag **RayObject (BoxCollider)** into **Collider**
  * **Add Component → RayInteractable**

    * **Pointable Element = Grabbable**, **Surface = ColliderSurface**
  * **Add Component → MoveTowardsTargetProvider** *(or MoveFromTargetProvider)*

    * In **RayInteractable → Movement Provider**: assign the provider
* ⬜ **4.4 (Wizard alternative):** Right-click **`RayObject` → Interaction SDK → Add Ray Grab Interaction**

  * • **Creates a new parent** (e.g., `ISDK Ray Grab Interaction`) and moves your cube **as child** under it.
  * • Most interaction components live on the **new parent**; configure on the parent.

### 5) World-Space UI (ray-clickable)

* ⬜ **5.1** **Hierarchy:** UI → **Canvas** (name **`UI_Canvas`**)

  * **Inspector (Canvas):** **Render Mode = World Space**
  * **Inspector (RectTransform):** **Position (0,1.5,2.2)**, **Scale (0.001,0.001,0.001)**
* ⬜ **5.2** **Hierarchy:** `UI_Canvas` → UI → **Button (TextMeshPro)** (name **`RaycastButton`**)

  * **Inspector (Text):** “Click Me”
* ⬜ **5.3** **EventSystem / Canvas module**

  * If missing, the **Canvas wizard** will add it: Right-click **`UI_Canvas` → Interaction SDK → Add Ray Interaction to Canvas** → click **Fix** (adds **Pointable Canvas Module** on `EventSystem`) → **Create**.
  * **Parent/child:** wizard may add a **helper child** under `UI_Canvas`.

---

# 🔹 Patch — Ray Movement Providers (supports all Meta options ) this for advanced 

## ✅ To-Do Checklist (with why)

### 1) Prepare the Ray-interactable object (match the video flow)

* ⬜ **1.1** In **Hierarchy**, right-click your **Cube** ⇒ **Interaction SDK → Add Ray Grab Interaction**

  * • The wizard spawns a new GameObject named **`ISDK Ray Grab Interaction`** (it appears **under/near your Cube** depending on project setup; select **that** new object).
  * Why: this object holds the **RayInteractable** and related pieces the ray needs to manipulate the cube (same as in the transcript).

* ⬜ **1.2 (Inspector: on `ISDK Ray Grab Interaction`)** Confirm it has:

  * **RayInteractable** (component)
  * A **ColliderSurface** (surface field set to the cube’s collider)
  * A **Rigidbody** (on the cube itself)
  * Why: these are required by Meta’s ray system. ([Meta Developers][2])

> If you built the ray interactable **manually** (from Meta docs), your **RayInteractable** may live **on the Cube** and the **ColliderSurface** may be on a child named **`Collider`**. In that case, do the next steps on the **Cube** instead of `ISDK Ray Grab Interaction`. The wiring is identical. ([Meta Developers][2])

---

### 2) Add **all** movement providers (so you can switch in the Inspector)

* ⬜ **2.1 (Inspector: on `ISDK Ray Grab Interaction` or on the `Cube` if you built manually)** click **Add Component** and add these (you can add only the ones you plan to use, but the selector supports all):

  * **MoveFromTargetProvider**
  * **MoveTowardsTargetProvider**
  * **AutoMoveTowardsTargetProvider**
  * **MoveAtSourceProvider**
  * **FollowTargetProvider**
  * **ObjectPullProvider**
    Why: these are the officially supported movement provider options in the Interaction SDK. ([Meta Developers][1])

> Tip: keep them all **disabled** initially; the selector script will enable exactly one.

---

### 3) Install the **selector** script (Inspector-friendly dropdown)

**Create** `Assets/Scripts/RayMovementProviderSelector.cs`, then paste:

```csharp
using UnityEngine;
using Oculus.Interaction; // RayInteractable, IMovementProvider and providers

[DisallowMultipleComponent]
public class RayMovementProviderSelector : MonoBehaviour
{
    public enum ProviderType
    {
        MoveFromTarget,
        MoveTowardsTarget,
        AutoMoveTowardsTarget,
        MoveAtSource,
        FollowTarget,
        ObjectPull
    }

    [Header("What to use right now")]
    public ProviderType ActiveProvider = ProviderType.MoveFromTarget;

    [Header("Targets")]
    public RayInteractable Ray; // Assign the RayInteractable on this object (or parent)

    [Header("Available Providers on this GameObject")]
    public MoveFromTargetProvider MoveFromTarget;
    public MoveTowardsTargetProvider MoveTowardsTarget;
    public AutoMoveTowardsTargetProvider AutoMoveTowardsTarget;
    public MoveAtSourceProvider MoveAtSource;
    public FollowTargetProvider FollowTarget;
    public ObjectPullProvider ObjectPull;

    void Reset()
    {
        if (!Ray) Ray = GetComponent<RayInteractable>();
        // Auto-find providers on the same GameObject
        if (!MoveFromTarget)       MoveFromTarget       = GetComponent<MoveFromTargetProvider>();
        if (!MoveTowardsTarget)    MoveTowardsTarget    = GetComponent<MoveTowardsTargetProvider>();
        if (!AutoMoveTowardsTarget)AutoMoveTowardsTarget= GetComponent<AutoMoveTowardsTargetProvider>();
        if (!MoveAtSource)         MoveAtSource         = GetComponent<MoveAtSourceProvider>();
        if (!FollowTarget)         FollowTarget         = GetComponent<FollowTargetProvider>();
        if (!ObjectPull)           ObjectPull           = GetComponent<ObjectPullProvider>();
    }

    void OnValidate() { Apply(); }
    void Awake()      { Apply(); }

    void Apply()
    {
        if (!Ray) return;

        // Disable all providers first
        EnableAll(false);

        IMovementProvider picked = null;
        switch (ActiveProvider)
        {
            case ProviderType.MoveFromTarget:
                if (MoveFromTarget) { MoveFromTarget.enabled = true; picked = MoveFromTarget; }
                break;
            case ProviderType.MoveTowardsTarget:
                if (MoveTowardsTarget) { MoveTowardsTarget.enabled = true; picked = MoveTowardsTarget; }
                break;
            case ProviderType.AutoMoveTowardsTarget:
                if (AutoMoveTowardsTarget) { AutoMoveTowardsTarget.enabled = true; picked = AutoMoveTowardsTarget; }
                break;
            case ProviderType.MoveAtSource:
                if (MoveAtSource) { MoveAtSource.enabled = true; picked = MoveAtSource; }
                break;
            case ProviderType.FollowTarget:
                if (FollowTarget) { FollowTarget.enabled = true; picked = FollowTarget; }
                break;
            case ProviderType.ObjectPull:
                if (ObjectPull) { ObjectPull.enabled = true; picked = ObjectPull; }
                break;
        }

        // Assign via the official injection API (works across SDK versions)
        if (picked != null)
        {
            Ray.InjectOptionalMovementProvider(picked);
        }
    }

    void EnableAll(bool enabled)
    {
        if (MoveFromTarget)        MoveFromTarget.enabled = enabled;
        if (MoveTowardsTarget)     MoveTowardsTarget.enabled = enabled;
        if (AutoMoveTowardsTarget) AutoMoveTowardsTarget.enabled = enabled;
        if (MoveAtSource)          MoveAtSource.enabled = enabled;
        if (FollowTarget)          FollowTarget.enabled = enabled;
        if (ObjectPull)            ObjectPull.enabled = enabled;
    }
}
```

*Why this is correct:* `RayInteractable` exposes the official **`InjectOptionalMovementProvider(IMovementProvider)`** method for programmatic assignment; using it avoids relying on private fields and matches Meta’s DI pattern. ([Meta Developers][3])

---

### 4) Wire it in the **Inspector** (exact objects)

* ⬜ **4.1 (Inspector: on `ISDK Ray Grab Interaction` or `Cube`)** **Add Component → Ray Movement Provider Selector**.

* ⬜ **4.2 (Inspector: same object)** Drag the local **RayInteractable** into **Ray**.

* ⬜ **4.3 (Inspector: same object)** Drag each provider you added into the matching fields (**MoveFromTarget**, **MoveTowardsTarget**, …).

  * Parent/child note: these providers should live on the **same GameObject** as **RayInteractable** (the object you ran the wizard on). Keep the **Rigidbody** on the **Cube** (the physical thing).

* ⬜ **4.4 (Inspector: same object)** Pick **ActiveProvider** from the dropdown:

  * Try **MoveTowardsTarget** to match the transcript’s “pull towards controller” behavior.

* ⬜ **4.5** Enter **Play** (with Link/Air Link). Point, grab, and feel the difference. Flip the dropdown while in **Play** to A/B test behaviors.

---

### 5) Quick reference — what each provider does

(Names straight from Meta’s docs; behavior summarized for learners.)

* **Move From Target** — object maintains the offset relative to the ray hit point as you move.
* **Move Towards Target** — object moves toward your controller when selected (what the video demo switches to).
* **Auto Move Towards Target** — similar to above, but the motion is auto-driven after selection.
* **Move At Source** — manipulates at the source location (useful for in-place edits).
* **Follow Target** — keeps following a moving target pose while selected.
* **Object Pull** — emphasizes a “pull” behavior from distance.
  (See Meta “Movement Providers” for canonical details & visuals.) ([Meta Developers][1])

---

## 🧭 Where exactly to click (recap, matching your flow)

1. **Project search**

   * Search **`OVRCameraRig`** → drop into scene.
   * Search **`OVRInteraction`** → make it a **child** of `OVRCameraRig`.
   * Search **`OVRControllers`** → make it a **child** of `OVRInteraction`.
     (This is your streamlined setup taken from the video; the “Rig & Managers (Meta Interaction SDK pattern)” section remains in the guide as **optional/advanced**.)

2. **Add rays**

   * In **Hierarchy**: `OVRCameraRig > OVRInteraction > OVRControllers > LeftController > ControllerInteractors`
     – drop **ControllerRayInteractor** here.
   * Repeat for **RightController**.
   * (Inspector on **ControllerInteractors**) add each ray to **Best Hover Interactor Group**. ([Meta Developers][2])

3. **Make a ray target**

   * Create **Cube** → run **Interaction SDK → Add Ray Grab Interaction** (right-click the Cube).
   * Select the newly created **`ISDK Ray Grab Interaction`** object for all movement-provider steps above.

---

## 🧪 Smoke test (exact checks)

* Point at the cube → **hover cursor** appears.
* Pull trigger → with **Move From Target**, the cube tracks from its hit point; with **Move Towards Target**, it moves toward the controller.
* Switch **ActiveProvider** in the **Inspector** → behavior updates immediately.

---


[1]: https://developers.meta.com/horizon/documentation/unity/unity-isdk-movement-providers/ "Meta Developers"
[2]: https://developers.meta.com/horizon/documentation/unity/unity-isdk-create-ray-interactions/ "Meta Developers"
[3]: https://developers.meta.com/horizon/reference/interaction/v78/class_oculus_interaction_ray_interactable/ "Meta Developers"


---




# 🔹 Part 3/5 — Light Switch Button (toggle Spotlight) *session task cube trigger and UI button*

## ✅ Goal

Click a world-space **Button** with the ray to **toggle a Spot Light**.

### 1) Add the light

* ⬜ **1.1** **Hierarchy:** Create → Light → **Spot Light** (name **`RoomSpot`**)

  * **Inspector (Light):** uncheck **enabled** (start OFF), tune **Range/Angle/Intensity**.

### 2) Add the script

* ⬜ **2.1** **Project:** `Assets/Scripts/LightSwitchController.cs`

```csharp
using UnityEngine;

public class LightSwitchController : MonoBehaviour
{
    public GameObject lightObject; // assign the Spot Light GameObject

    public void ToggleLight()
    {
        if (lightObject) lightObject.SetActive(!lightObject.activeSelf);
    }
}
```

### 3) Wire in the Inspector

* ⬜ **3.1** **Hierarchy:** Create Empty → **`SceneManager`** (root)

  * **Inspector (SceneManager):** **Add Component → LightSwitchController**
  * **LightSwitchController → Light Object**: drag **`RoomSpot`** (GameObject)
* ⬜ **3.2** **Hierarchy:** `UI_Canvas/RaycastButton`

  * **Inspector (Button):** **OnClick() → +** → drag **`SceneManager`** → choose **LightSwitchController.ToggleLight()**
* ⬜ **3.3** **Parent/child change?** None.

---

# 🔹 Part 4/5 — Basketball Hoop Scoring (UI score increases on goal)

## ✅ Goal

Grab/throw a **basketball**; when it passes through the **hoop**, the **Score** UI increments.

### 1) Backboard + rim (with child trigger)

* ⬜ **1.1** **Hierarchy:** 3D Object → **Cube** (name **`Backboard`**)

  * **Inspector (Transform):** **Scale (1,1.2,0.05)**, **Position (0,1.6,3)**
* ⬜ **1.2** **Hierarchy:** 3D Object → **Torus** *(or thin Cylinder ring)* (name **`Rim`**)

  * **Inspector (Transform):** **Position (0,1.4,2.8)**, radius ≈ **0.23–0.25**
  * **Hierarchy:** drag **`Rim`** onto **`Backboard`** → **child** `Backboard/Rim`
  * **Inspector (Rim):** **Add Component → MeshCollider** (Convex OFF, not a trigger)
* ⬜ **1.3** **Hierarchy:** 3D Object → **Cylinder** (name **`ScoreTrigger`**)

  * **Hierarchy:** drag under **`Backboard/Rim`** → **child** `Backboard/Rim/ScoreTrigger`
  * **Inspector (Transform):** **Scale (~0.42, 0.05, 0.42)**; place **just below** rim plane
  * **Inspector (Collider):** **Cylinder Collider**, **Is Trigger = ON**

### 2) Basketball (grab + physics)

* ⬜ **2.1** **Hierarchy:** 3D Object → **Sphere** (name **`Basketball`**)

  * **Inspector (Transform):** **Scale (0.24,0.24,0.24)**
* ⬜ **2.2** **Inspector (Basketball):**

  * **Add Component → Rigidbody** (Mass **0.62**, Drag **0.05**, Angular Drag **0.05**)
  * (Optional) Assign **Physic Material** (Bounciness ~**0.6**, Combine **Maximum**) to SphereCollider
  * **Add Component → Grabbable** → drag **Basketball (Rigidbody)** into **Grabbable → Rigidbody**
  * **Add Component → ColliderSurface** → drag **Basketball (SphereCollider)**
  * **Add Component → GrabInteractable** *(or Hand Grab Interactable)*
* ⬜ **2.3** **Tag setup**

  * **Edit → Project Settings → Tags and Layers → Tags → + →** add **`Basketball`**
  * **Inspector (Basketball → Tag):** set **Basketball**

> **Wizard alternative:** Right-click `Basketball` → **Interaction SDK → Add Grab Interaction**
> • **Creates a parent** (e.g., `ISDK Hand Grab Interaction`) → sphere becomes its **child**; most components live on parent.

### 3) Score UI

* ⬜ **3.1** **Hierarchy:** UI → **Canvas** (name **`ScoreCanvas`**)

  * **Inspector (Canvas):** **World Space**, **Position (-0.9,1.8,2.2)**, **Scale (0.001,0.001,0.001)**
* ⬜ **3.2** **Hierarchy:** `ScoreCanvas` → UI → **Text (TextMeshPro)** (name **`ScoreText`**)

  * **Inspector (Text):** “Score: 0”

### 4) Scripts

* ⬜ **4.1** **Project:** `Assets/Scripts/ScoreManager.cs`

```csharp
using UnityEngine;
using TMPro;

public class ScoreManager : MonoBehaviour
{
    public TMP_Text scoreText;
    public int pointsPerGoal = 1;
    private int _score;

    void Start() => UpdateUI();

    public void AddScore(int amount)
    {
        _score += amount;
        UpdateUI();
    }

    public void ResetScore()
    {
        _score = 0;
        UpdateUI();
    }

    private void UpdateUI()
    {
        if (scoreText) scoreText.text = $"Score: {_score}";
    }
}
```

* ⬜ **4.2** **Project:** `Assets/Scripts/HoopTrigger.cs`

```csharp
using UnityEngine;

[RequireComponent(typeof(Collider))]
public class HoopTrigger : MonoBehaviour
{
    public ScoreManager scoreManager;
    public string ballTag = "Basketball";

    private void Reset()
    {
        var c = GetComponent<Collider>();
        if (c) c.isTrigger = true;
    }

    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag(ballTag) && scoreManager)
            scoreManager.AddScore(scoreManager.pointsPerGoal);
    }
}
```

### 5) Wiring (Inspector targets)

* ⬜ **5.1** **Hierarchy:** **`SceneManager`** → **Inspector:** **Add Component → ScoreManager**

  * **ScoreManager → Score Text**: drag **`ScoreCanvas/ScoreText`**
* ⬜ **5.2** **Hierarchy:** `Backboard/Rim/ScoreTrigger` → **Inspector:** **Add Component → HoopTrigger**

  * **HoopTrigger → Score Manager**: drag **`SceneManager`**

---

# 🔹 Part 5/5 — Playtest, (Optional) Masking, Build & Troubleshooting  *Session task*

## A) Playtest

* ⬜ **A.1** Enter **Play** with Quest Link/AirLink. Rays appear only on valid targets (Hide When No Interactable = ON).
* ⬜ **A.2** Click **`UI_Canvas/RaycastButton`** → `RoomSpot` toggles.
* ⬜ **A.3** Throw **Basketball** through **`Backboard/Rim/ScoreTrigger`** → **Score** increments.

## B) (Optional) Masking: Left = 3D only, Right = UI only

* ⬜ **B.1** **LeftController** (object under **OVRController**): **Add Component → Tag Set Filter**

  * **Required Tag = 3DObject**, **Exclude Tag = UICanvas**
* ⬜ **B.2** **3D interactable host** (manual=`RayObject`; wizard=its **parent**): **Add Component → Tag Set → Tag = 3DObject**
* ⬜ **B.3** **RightController**: **Add Component → Tag Set Filter**

  * **Required Tag = UICanvas**, **Exclude Tag = 3DObject**
* ⬜ **B.4** **Canvas interactable host** (manual=`UI_Canvas`; wizard=its helper): **Add Component → Tag Set → Tag = UICanvas**
* ⬜ **B.5** **Reference filters inside each RayInteractor**

  * **LeftController/ControllerInteractors/Controller Ray Interactor → Inspector → Interactable Filters → +** → drag **LeftController (Tag Set Filter)**
  * **RightController/ControllerInteractors/Controller Ray Interactor → …** → drag **RightController (Tag Set Filter)**

## C) Troubleshooting

* ⬜ **No ray** → Check Ray Interactor component, line visual, and that you’re pointing at interactables.
* ⬜ **UI not clickable** → Canvas **World Space**, `EventSystem` has **Pointable Canvas Module**, `UI_Canvas` has **Tracked Device Graphic Raycaster**, scale ~0.001.
* ⬜ **3D not reacting** → On the **host**: **RayInteractable**, **Grabbable**, **ColliderSurface**, **MovementProvider** set and assigned in **RayInteractable**.
* ⬜ **Light button** → Button.OnClick → **SceneManager → LightSwitchController.ToggleLight()**; script has **RoomSpot** assigned.
* ⬜ **No score** → `ScoreTrigger` **Is Trigger = ON**, **HoopTrigger.ScoreManager** set, ball **Tag = Basketball**, trigger slightly **below** rim.

## D) Build for Quest

* ⬜ **D.1** Build Settings → **Android**
* ⬜ **D.2** **Development Build** (first tests)
* ⬜ **D.3** **Build & Run** to headset

---

### Parent/Child Quick Facts (for this lab)

* ⬜ **Main rig path (video)**: `OVRCameraRig` (root) → **child** `OVRInteraction` → **child** `OVRController` (with Left/RightController inside).
* ⬜ **Ray Grab Wizard** on a mesh → **creates parent** (e.g., `ISDK Ray Grab Interaction`) → your mesh becomes its **child**.
* ⬜ **Canvas Wizard** → often **creates helper child** under **`UI_Canvas`**.
* ⬜ **Manual component path** → no hierarchy changes; you add components to the **selected** object.
* ⬜ **Tag Set Filter** lives on **Left/RightController**; **Tag Set** lives on the **interactable host**; you then **reference the Filter inside each Controller Ray Interactor**.






---

# 🔹 Part 6/6 — Controller Selection Masks (Tag Set Filter: Left = 3D only, Right = UI only) *Session Task*

> **Goal:** Make the **Left Controller** ray **select only 3D objects** and **ignore UI**; make the **Right Controller** ray **select only UI (Canvas)** and **ignore 3D**—exactly as shown in the video.

## ✅ To-Do Checklist (with precise Hierarchy/Inspector targets)

### 1) Add the **Tag Set Filter** to each controller

* ⬜ **1.1 — Left Controller (filter for 3D only)**

  * **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/LeftController`
  * **Inspector (LeftController):** **Add Component → Tag Set Filter**

    * **Required Tag =** `3DObject`
    * **Exclude Tag =** `UICanvas`
  * **Parent/child change?** None; you added a component to **LeftController**.

* ⬜ **1.2 — Right Controller (filter for UI only)**

  * **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/RightController`
  * **Inspector (RightController):** **Add Component → Tag Set Filter**

    * **Required Tag =** `UICanvas`
    * **Exclude Tag =** `3DObject`
  * **Parent/child change?** None; you added a component to **RightController**.

---

### 2) Tag each **interactable host** (the objects your rays should hit)

> You’ll put a **Tag Set** on the object that actually holds the **RayInteractable**.
> • If you used the **wizard** on your cube, your interactable host is the **wizard-created parent** (e.g., `ISDK Ray Grab Interaction`) and your visible mesh is its **child**.
> • If you built **manually**, the interactable host is likely your **Cube** itself.

* ⬜ **2.1 — 3D Object host (for the cube)**

  * **Hierarchy Path (wizard):** `ISDK Ray Grab Interaction` (the **parent** created by the wizard)
  * **Hierarchy Path (manual):** your **`RayObject`** cube (same GO that has **RayInteractable**)
  * **Inspector (host GO):** **Add Component → Tag Set**

    * **Tag =** `3DObject`
  * **Parent/child:** unchanged—component added to the **host**.

* ⬜ **2.2 — UI Canvas host (for the world-space UI)**

  * **Hierarchy Path (manual UI):** `UI_Canvas` (the Canvas itself)
  * **Hierarchy Path (wizard UI):** the **Canvas helper** created by “Add Ray Interaction to Canvas” (if present; tag that helper or the Canvas—either works as long as the **RayInteractable** is on this host)
  * **Inspector (host GO):** **Add Component → Tag Set**

    * **Tag =** `UICanvas`
  * **Parent/child:** unchanged—component added to the **host**.

---

### 3) Tell each **Controller Ray Interactor** which **Filter** to use

> This is the step people miss in the video. You must **reference** the Tag Set Filter **inside** each **Ray Interactor** so the filter actually runs.

* ⬜ **3.1 — Left Ray Interactor uses the LeftController’s filter**

  * **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/LeftController/ControllerInteractors/Controller Ray Interactor`
  * **Inspector (Controller Ray Interactor):** find **Optionals → Interactable Filters (list)**

    * Click **+**, then **drag `LeftController`** (the object that holds the **Tag Set Filter**) **into the new slot**.
  * **Parent/child:** unchanged—just filling a list field on the interactor.

* ⬜ **3.2 — Right Ray Interactor uses the RightController’s filter**

  * **Hierarchy Path:** `OVRCameraRig/OVRInteraction/OVRController/RightController/ControllerInteractors/Controller Ray Interactor`
  * **Inspector (Controller Ray Interactor):** **Optionals → Interactable Filters → +**

    * **Drag `RightController`** (the object with the **Tag Set Filter**) **into the slot**.
  * **Parent/child:** unchanged—just filling a list field on the interactor.

---

### 4) Quick playtest (what you should see)

* ⬜ **Left Controller** ray **selects the cube** (tagged `3DObject`) and **passes through the UI** (tagged `UICanvas`).
* ⬜ **Right Controller** ray **selects the UI Canvas** and **ignores the cube**.
* ⬜ **If it doesn’t work:** see troubleshooting below—most issues are a missed **reference** (Step 3) or a **tag typo** (Step 2).

---

## 🛠 Troubleshooting (exact spots to check)

* ⬜ **Left ray still hits UI** → On **LeftController**, verify **Tag Set Filter** is **Required=3DObject**, **Exclude=UICanvas**. On the **Left Controller Ray Interactor**, confirm the **Interactable Filters** list **includes LeftController**.
* ⬜ **Right ray still hits 3D** → On **RightController**, verify **Required=UICanvas**, **Exclude=3DObject**; on the **Right Controller Ray Interactor**, the **Interactable Filters** list **includes RightController**.
* ⬜ **Nothing is selectable** → Ensure the **interactable hosts** actually have **Tag Set** with **matching strings** (`3DObject` on the cube’s host; `UICanvas` on the Canvas host).
* ⬜ **Wizard confusion** → If you used the **Ray Grab wizard** on the cube, remember the **host** is the **wizard parent** (`ISDK Ray Grab Interaction`), not the mesh child. Put **Tag Set** on that parent.

---














































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
