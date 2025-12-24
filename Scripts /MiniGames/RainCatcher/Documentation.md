# 📚 RainCatcher Mini-Game - Complete Line-by-Line Documentation

**Folder Path:** `Assets/Scripts/MiniGames/RainCatcher`

This document provides an **extremely detailed, line-by-line explanation** of ALL scripts in the RainCatcher mini-game folder.

---

# Table of Contents

1. [Overview](#overview)
2. [Script 1: Basket.cs (Player Basket)](#script-1-basketcs)
3. [Script 2: RainDrop.cs (Falling Raindrop)](#script-2-raindropcs)
4. [Script 3: RainCatcherGame.cs (Game Controller)](#script-3-raincatchergamecs)
5. [How Everything Works Together](#how-everything-works-together)

---

# Overview

The **Rain Catcher** mini-game teaches water conservation:

- **Raindrops** fall from the sky
- Player moves a **basket** horizontally with the mouse
- Catch raindrops to earn **+10 GreenPoints** each
- Game runs for **30 seconds** (configurable)
- No penalty for missed drops

**Scripts in this folder:**

| Script               | Lines | Purpose                                           |
| -------------------- | ----- | ------------------------------------------------- |
| `Basket.cs`          | 46    | Player's basket - visual feedback on catch        |
| `RainDrop.cs`        | 83    | Individual raindrop - falls and detects collision |
| `RainCatcherGame.cs` | 212   | Main game controller                              |

---

# Script 1: Basket.cs

**File Path:** `Assets/Scripts/MiniGames/RainCatcher/Basket.cs`

## Script Purpose

`Basket` is attached to the player's basket GameObject. It:

1. Detects when raindrops collide with the basket (trigger collision)
2. Plays visual feedback (particle effect, animation)

**Note:** Movement is handled by `RainCatcherGame.cs`, not this script.

---

## Complete Script Code

```csharp
using UnityEngine;

public class Basket : MonoBehaviour
{
    [Header("Visual Feedback (Optional)")]
    [Tooltip("Effect to play when basket catches a raindrop")]
    public GameObject catchEffect;

    [Tooltip("Basket animator (optional)")]
    public Animator basketAnimator;

    void OnTriggerEnter2D(Collider2D other)
    {
        if (other.CompareTag("RainDrop"))
        {
            PlayCatchFeedback();
        }
    }

    void PlayCatchFeedback()
    {
        if (catchEffect != null)
        {
            Instantiate(catchEffect, transform.position, Quaternion.identity);
        }

        if (basketAnimator != null)
        {
            basketAnimator.SetTrigger("Catch");
        }
    }
}
```

---

## Imports / Using Statements

### Line 1: `using UnityEngine;`

```csharp
using UnityEngine;
```

#### What it provides:

- `MonoBehaviour` - base class
- `GameObject` - for catchEffect prefab
- `Collider2D` - collision detection parameter
- `Animator` - animation controller
- `Quaternion` - rotation (identity = no rotation)
- `Debug` - console logging

---

## Class Declaration

### Line 8: Class Definition

```csharp
public class Basket : MonoBehaviour
```

- `public class Basket`: Accessible from other scripts
- `: MonoBehaviour`: Unity component, can be attached to GameObjects

---

## Variables & Fields

### Lines 10-15: Visual Feedback References

```csharp
[Header("Visual Feedback (Optional)")]
[Tooltip("Effect to play when basket catches a raindrop")]
public GameObject catchEffect;

[Tooltip("Basket animator (optional)")]
public Animator basketAnimator;
```

#### Line 12: `public GameObject catchEffect;`

**What it stores:**

- A prefab for visual effects (splash, sparkles, etc.)
- Instantiated when catching a raindrop

**Why GameObject not ParticleSystem:**

- More flexible - can be any type of effect prefab
- Could contain particles, sprites, sounds, etc.

**Optional:**

- If null, no effect is played
- Game still works without it

#### Line 15: `public Animator basketAnimator;`

**What is Animator:**

- Unity component that controls animations
- Uses Animation Controller with states and transitions
- Can be triggered with parameters

**What it stores:**

- Reference to this basket's Animator component
- Used to play "catch" animation

---

## Unity Callback Methods

### Lines 20-28: OnTriggerEnter2D

```csharp
void OnTriggerEnter2D(Collider2D other)
{
    // Check if basket collided with raindrop
    if (other.CompareTag("RainDrop"))
    {
        // Play catch effect if available
        PlayCatchFeedback();
    }
}
```

#### What is OnTriggerEnter2D:

**When Unity calls this:**

- When another Collider2D **enters** this object's trigger collider
- Called once at the moment of first overlap
- Only works if at least one collider has `isTrigger = true`

**Trigger vs Collider:**

| Type                | Physical             | OnTrigger events | OnCollision events |
| ------------------- | -------------------- | ---------------- | ------------------ |
| `isTrigger = false` | Objects bounce off   | No               | Yes                |
| `isTrigger = true`  | Objects pass through | Yes              | No                 |

**Requirements for OnTriggerEnter2D to work:**

1. Both objects have Collider2D components
2. At least one has `isTrigger = true`
3. At least one has Rigidbody2D
4. Both objects are on layers that can interact

#### Parameter: `Collider2D other`

**What it contains:**

- Reference to the OTHER object's collider
- NOT this basket's collider
- Can access the other object: `other.gameObject`, `other.transform`

#### Line 23: Tag Comparison

```csharp
if (other.CompareTag("RainDrop"))
```

**What is a Tag:**

- A label assigned to GameObjects
- Used to identify object types
- Set in Inspector or via `gameObject.tag = "TagName"`

**What CompareTag does:**

- Checks if the other object has the "RainDrop" tag
- Returns `true` if tag matches, `false` otherwise

**Why CompareTag instead of `other.tag == "RainDrop"`:**

- `CompareTag()` is **faster** (no string allocation)
- No garbage collection overhead
- Best practice for tag comparisons

**How to set up the tag:**

1. Select a GameObject in hierarchy
2. In Inspector, click "Tag" dropdown
3. Choose "Add Tag..." if "RainDrop" doesn't exist
4. Create "RainDrop" tag
5. Select raindrops and assign the tag

---

## Custom Functions

### Lines 33-44: PlayCatchFeedback

```csharp
void PlayCatchFeedback()
{
    if (catchEffect != null)
    {
        Instantiate(catchEffect, transform.position, Quaternion.identity);
    }

    if (basketAnimator != null)
    {
        basketAnimator.SetTrigger("Catch");
    }
}
```

#### Lines 35-38: Spawn Effect

```csharp
if (catchEffect != null)
{
    Instantiate(catchEffect, transform.position, Quaternion.identity);
}
```

**What Instantiate does:**

- Creates a copy of `catchEffect` prefab
- Spawns at `transform.position` (basket's position)
- With no rotation (`Quaternion.identity`)

**Why null check:**

- catchEffect is optional
- Without check: NullReferenceException if not assigned

**What happens to the instantiated effect:**

- It exists in the scene until destroyed
- Particle effects often self-destroy when complete
- Or you can destroy it: `Destroy(instantiatedEffect, 2f);`

#### Lines 40-43: Trigger Animation

```csharp
if (basketAnimator != null)
{
    basketAnimator.SetTrigger("Catch");
}
```

**What is SetTrigger:**

- Sets an animation parameter of type "Trigger"
- Triggers are like one-shot bools that auto-reset
- Causes state machine to transition if matching trigger exists

**How Animation Trigger works:**

1. In Animator Controller, create parameter: `Catch` (type: Trigger)
2. Create transition from "Idle" to "CatchAnimation"
3. Condition: `Catch` trigger is set
4. When `SetTrigger("Catch")` is called:
   - Parameter is set
   - Transition occurs
   - Animation plays
   - Parameter auto-resets

---

---

# Script 2: RainDrop.cs

**File Path:** `Assets/Scripts/MiniGames/RainCatcher/RainDrop.cs`

## Script Purpose

`RainDrop` controls an individual falling raindrop. It:

1. Falls from top of screen at configurable speed
2. Detects collision with basket via trigger
3. Notifies game manager when caught
4. Destroys itself when caught or falls off screen

---

## Complete Script Code

```csharp
using UnityEngine;

public class RainDrop : MonoBehaviour
{
    private float fallSpeed = 3f;
    private RainCatcherGame gameManager;
    private bool collected = false;

    public void Initialize(float speed, RainCatcherGame manager)
    {
        fallSpeed = speed;
        gameManager = manager;
    }

    void Update()
    {
        if (collected) return;

        // Fall down
        transform.position += Vector3.down * fallSpeed * Time.deltaTime;

        // Destroy if falls off screen
        if (transform.position.y < -6f)
        {
            Destroy(gameObject);
        }
    }

    void OnTriggerEnter2D(Collider2D other)
    {
        Debug.Log($"RainDrop triggered! Collided with: '{other.name}', Tag: '{other.tag}'");

        if (other.CompareTag("Basket"))
        {
            Debug.Log("Basket tag matched! Collecting...");
            if (!collected)
            {
                Collect();
            }
        }
        else
        {
            Debug.LogWarning($"Tag mismatch! Expected 'Basket', got '{other.tag}'");
        }
    }

    void Collect()
    {
        collected = true;

        if (gameManager != null)
        {
            gameManager.OnDropCaught();
        }

        Destroy(gameObject);
    }
}
```

---

## Imports / Using Statements

### Line 1: `using UnityEngine;`

```csharp
using UnityEngine;
```

Provides all Unity types needed: `MonoBehaviour`, `Vector3`, `Time`, `Debug`, `Collider2D`.

---

## Class Declaration

### Line 7: Class Definition

```csharp
public class RainDrop : MonoBehaviour
```

Standard Unity component class.

---

## Variables & Fields

### Lines 9-11: Private Fields

```csharp
private float fallSpeed = 3f;
private RainCatcherGame gameManager;
private bool collected = false;
```

| Variable      | Default | Purpose                                |
| ------------- | ------- | -------------------------------------- |
| `fallSpeed`   | 3.0     | How fast the drop falls (units/second) |
| `gameManager` | null    | Reference to notify when caught        |
| `collected`   | false   | Prevents double-collection             |

**Why all private:**

- Set via `Initialize()` method, not Inspector
- Internal state, not for external access

---

## Custom Functions

### Lines 18-22: Initialize Method

```csharp
public void Initialize(float speed, RainCatcherGame manager)
{
    fallSpeed = speed;
    gameManager = manager;
}
```

**What it does:**

- Sets the fall speed
- Stores reference to game manager

**Called by:**

- `RainCatcherGame.SpawnRainDrop()` after instantiation

**Why not use Start():**

- Values come from outside (game manager)
- Decouples raindrop from specific manager instance
- More flexible for testing/reuse

---

## Unity Lifecycle Methods

### Lines 24-36: Update Method

```csharp
void Update()
{
    if (collected) return;

    // Fall down
    transform.position += Vector3.down * fallSpeed * Time.deltaTime;

    // Destroy if falls off screen
    if (transform.position.y < -6f)
    {
        Destroy(gameObject);
    }
}
```

#### Line 26: Guard Clause

```csharp
if (collected) return;
```

**What it does:**

- If already collected, skip all update logic
- Prevents movement after collection (brief moment before destroy)

#### Line 29: Falling Movement

```csharp
transform.position += Vector3.down * fallSpeed * Time.deltaTime;
```

**Breaking it down:**

| Part                 | Value            | Purpose                 |
| -------------------- | ---------------- | ----------------------- |
| `transform.position` | Current position | Access to position      |
| `+=`                 | Add to current   | Update position         |
| `Vector3.down`       | (0, -1, 0)       | Direction: downward     |
| `* fallSpeed`        | e.g., `* 3`      | Speed multiplier        |
| `* Time.deltaTime`   | e.g., `* 0.016`  | Frame-rate independence |

**Example calculation (60 FPS):**

```
Frame 1: position.y = 6.0 + (-1 * 3 * 0.0167) = 5.95
Frame 2: position.y = 5.95 + (-1 * 3 * 0.0167) = 5.90
...
After 2 seconds: position.y ≈ 0
```

#### Lines 32-35: Off-Screen Destruction

```csharp
if (transform.position.y < -6f)
{
    Destroy(gameObject);
}
```

**What it does:**

- Checks if raindrop fell below y = -6 (off-screen)
- Destroys the raindrop to free memory

**Why -6:**

- Typical screen bottom in Unity 2D
- Depends on camera orthographic size
- Ensures raindrop is definitely off-screen

---

### Lines 42-64: OnTriggerEnter2D

```csharp
void OnTriggerEnter2D(Collider2D other)
{
    // Debug: Print all collisions
    Debug.Log($"RainDrop triggered! Collided with: '{other.name}', Tag: '{other.tag}'");

    // Check if raindrop collided with basket
    if (other.CompareTag("Basket"))
    {
        Debug.Log("Basket tag matched! Collecting...");
        if (!collected)
        {
            Collect();
        }
        else
        {
            Debug.Log("Already collected!");
        }
    }
    else
    {
        Debug.LogWarning($"Tag mismatch! Expected 'Basket', got '{other.tag}'");
    }
}
```

#### When Unity calls this:

- When this raindrop's collider **enters** another trigger collider
- The basket has `isTrigger = true` (set by game manager)

#### Line 45: Debug Logging

```csharp
Debug.Log($"RainDrop triggered! Collided with: '{other.name}', Tag: '{other.tag}'");
```

**Why extensive logging:**

- Collision detection can be tricky to debug
- Shows exactly what was hit and its tag
- Helpful during development

#### Line 48: Tag Check

```csharp
if (other.CompareTag("Basket"))
```

- Only collect if hit the basket
- Other objects (walls, floor) ignored

#### Lines 51-54: Prevent Double Collection

```csharp
if (!collected)
{
    Collect();
}
```

**Why check collected:**

- `OnTriggerEnter2D` might fire multiple times
- Especially with complex collider shapes
- `collected` flag ensures single collection

---

### Lines 69-81: Collect Method

```csharp
void Collect()
{
    collected = true;

    // Notify game manager
    if (gameManager != null)
    {
        gameManager.OnDropCaught();
    }

    // Destroy the raindrop
    Destroy(gameObject);
}
```

#### Line 71: Set Flag First

```csharp
collected = true;
```

**Why first:**

- Immediately prevents any other collection attempts
- Guard clause in Update() and OnTrigger will exit early

#### Lines 74-77: Notify Game Manager

```csharp
if (gameManager != null)
{
    gameManager.OnDropCaught();
}
```

**What this triggers:**

- `RainCatcherGame.OnDropCaught()`
- Awards points to player
- Updates UI

#### Line 80: Destroy Raindrop

```csharp
Destroy(gameObject);
```

- Removes raindrop from game
- Frees memory
- Stops all scripts on this object

---

---

# Script 3: RainCatcherGame.cs

**File Path:** `Assets/Scripts/MiniGames/RainCatcher/RainCatcherGame.cs`

## Script Purpose

`RainCatcherGame` is the **main controller** for the Rain Catcher game. It manages:

1. **Game timing** - 30-second countdown
2. **Basket movement** - Follows mouse position
3. **Raindrop spawning** - Creates falling drops at intervals
4. **Scoring** - Tracks caught drops, awards GreenPoints
5. **UI updates** - Timer, score, game over panel

---

## Complete Script Code (Key Parts)

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.SceneManagement;
using TMPro;

public class RainCatcherGame : MonoBehaviour
{
    [Header("Game Settings")]
    public float gameDuration = 30f;
    public float spawnInterval = 0.8f;
    public float dropSpeed = 3f;

    [Header("Spawn Area")]
    public float spawnMinX = -8f;
    public float spawnMaxX = 8f;
    public float spawnY = 6f;

    [Header("Basket Settings")]
    public GameObject basketObject;
    public float basketSpeed = 8f;
    public float basketMinX = -8f;
    public float basketMaxX = 8f;

    [Header("Prefabs")]
    public GameObject rainDropPrefab;

    [Header("UI References")]
    public TextMeshProUGUI timerText;
    public TextMeshProUGUI scoreText;
    public TextMeshProUGUI greenPointsText;
    public GameObject gameOverPanel;
    public TextMeshProUGUI finalScoreText;
    public TextMeshProUGUI rewardText;

    [Header("Scoring - GreenPoints")]
    public int pointsPerDrop = 10;

    // Game state
    private float timeRemaining;
    private int dropsCollected = 0;
    private bool gameActive = false;
    private float spawnTimer = 0f;

    // ... methods
}
```

---

## Imports / Using Statements

### Lines 1-4:

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.SceneManagement;
using TMPro;
```

| Import                        | Provides                                           |
| ----------------------------- | -------------------------------------------------- |
| `System.Collections`          | IEnumerator for coroutines (not used but imported) |
| `UnityEngine`                 | Core Unity types                                   |
| `UnityEngine.SceneManagement` | SceneManager.LoadScene()                           |
| `TMPro`                       | TextMeshProUGUI                                    |

---

## Variables & Fields

### Lines 12-21: Game Settings

```csharp
[Header("Game Settings")]
[Tooltip("Game duration in seconds")]
public float gameDuration = 30f;

[Tooltip("Time between drop spawns")]
public float spawnInterval = 0.8f;

[Tooltip("Drop fall speed")]
public float dropSpeed = 3f;
```

| Field           | Default | Purpose                    |
| --------------- | ------- | -------------------------- |
| `gameDuration`  | 30      | Total game time in seconds |
| `spawnInterval` | 0.8     | Seconds between spawns     |
| `dropSpeed`     | 3       | How fast drops fall        |

**Game difficulty tuning:**

- Lower `spawnInterval` = more drops = harder
- Higher `dropSpeed` = faster drops = harder
- Lower `gameDuration` = less time = potentially lower scores

---

### Lines 23-26: Spawn Area

```csharp
[Header("Spawn Area")]
public float spawnMinX = -8f;
public float spawnMaxX = 8f;
public float spawnY = 6f;
```

**What these define:**

```
Screen Layout:
                    spawnY = 6 (top, above visible area)
                         ↓
    spawnMinX = -8  ←——————→  spawnMaxX = 8
           ↓         drops spawn randomly        ↓
```

---

### Lines 28-32: Basket Settings

```csharp
[Header("Basket Settings")]
public GameObject basketObject;
public float basketSpeed = 8f;
public float basketMinX = -8f;
public float basketMaxX = 8f;
```

| Field             | Purpose                                       |
| ----------------- | --------------------------------------------- |
| `basketObject`    | Reference to the basket in scene              |
| `basketSpeed`     | (Not actually used - mouse controls position) |
| `basketMinX/MaxX` | Boundaries for basket movement                |

---

### Lines 44-46: Scoring

```csharp
[Header("Scoring - GreenPoints")]
[Tooltip("+10 GP per drop caught")]
public int pointsPerDrop = 10;
```

**Simple scoring:**

- Each drop caught = +10 GreenPoints
- No penalty for missed drops
- Total = dropsCollected × pointsPerDrop

---

### Lines 48-52: Private Game State

```csharp
private float timeRemaining;
private int dropsCollected = 0;
private bool gameActive = false;
private float spawnTimer = 0f;
```

| Variable         | Purpose                          |
| ---------------- | -------------------------------- |
| `timeRemaining`  | Countdown from gameDuration to 0 |
| `dropsCollected` | Number of drops caught           |
| `gameActive`     | Whether game is running          |
| `spawnTimer`     | Accumulator for spawn timing     |

---

## Unity Lifecycle Methods

### Lines 54-57: Start Method

```csharp
void Start()
{
    StartGame();
}
```

- Immediately starts the game on scene load
- No menu, no delay

---

### Lines 59-80: Update Method

```csharp
void Update()
{
    if (!gameActive) return;

    timeRemaining -= Time.deltaTime;
    UpdateTimerUI();

    if (timeRemaining <= 0)
    {
        EndGame();
        return;
    }

    MoveBasket();

    spawnTimer += Time.deltaTime;
    if (spawnTimer >= spawnInterval)
    {
        SpawnRainDrop();
        spawnTimer = 0f;
    }
}
```

#### Update Flow:

```
Each Frame:
├── If not active → exit
├── Decrease timer
├── Update timer UI
├── If timer <= 0 → end game
├── Move basket to mouse position
└── Spawn timer:
    ├── Add deltaTime
    └── If >= interval → spawn drop, reset timer
```

---

## Custom Functions

### Lines 82-111: StartGame Method

```csharp
void StartGame()
{
    timeRemaining = gameDuration;
    dropsCollected = 0;
    gameActive = true;

    if (gameOverPanel != null)
        gameOverPanel.SetActive(false);

    // Auto-setup basket
    if (basketObject != null)
    {
        var rb = basketObject.GetComponent<Rigidbody2D>();
        if (rb == null)
        {
            rb = basketObject.AddComponent<Rigidbody2D>();
            rb.bodyType = RigidbodyType2D.Kinematic;
            rb.gravityScale = 0f;
        }

        var col = basketObject.GetComponent<Collider2D>();
        if (col != null && !col.isTrigger)
            col.isTrigger = true;
    }

    UpdateScoreUI();
    UpdateTimerUI();

    Debug.Log("Rain Catcher Started!");
}
```

#### Lines 92-105: Auto-Setup Basket Physics

```csharp
if (basketObject != null)
{
    var rb = basketObject.GetComponent<Rigidbody2D>();
    if (rb == null)
    {
        rb = basketObject.AddComponent<Rigidbody2D>();
        rb.bodyType = RigidbodyType2D.Kinematic;
        rb.gravityScale = 0f;
    }

    var col = basketObject.GetComponent<Collider2D>();
    if (col != null && !col.isTrigger)
        col.isTrigger = true;
}
```

**Why auto-setup:**

- Ensures physics work correctly
- Reduces setup errors in scene

**Rigidbody2D setup:**

| Property       | Value       | Why                              |
| -------------- | ----------- | -------------------------------- |
| `bodyType`     | `Kinematic` | Moved by script, not physics     |
| `gravityScale` | `0`         | No gravity (controlled manually) |

**What is Kinematic:**

- Object is moved via code, not physics forces
- Still participates in collision detection
- Required for OnTriggerEnter2D to work (at least one Rigidbody2D needed)

**Why `isTrigger = true`:**

- Drops pass through basket (not bounce off)
- OnTriggerEnter2D events fire
- Physical collision would push drops away

---

### Lines 113-121: MoveBasket Method

```csharp
void MoveBasket()
{
    if (basketObject == null) return;

    Vector3 mouseWorldPos = Camera.main.ScreenToWorldPoint(Input.mousePosition);
    Vector3 pos = basketObject.transform.position;
    pos.x = Mathf.Clamp(mouseWorldPos.x, basketMinX, basketMaxX);
    basketObject.transform.position = pos;
}
```

#### Line 117: Screen to World Conversion

```csharp
Vector3 mouseWorldPos = Camera.main.ScreenToWorldPoint(Input.mousePosition);
```

**What `Input.mousePosition` returns:**

- Mouse position in **screen pixels**
- (0, 0) = bottom-left corner
- (Screen.width, Screen.height) = top-right

**What `ScreenToWorldPoint` does:**

- Converts screen pixels to world units
- Uses camera's projection to calculate

#### Lines 118-120: Apply Clamped Position

```csharp
Vector3 pos = basketObject.transform.position;
pos.x = Mathf.Clamp(mouseWorldPos.x, basketMinX, basketMaxX);
basketObject.transform.position = pos;
```

**What `Mathf.Clamp` does:**

- Constrains a value between min and max
- `Clamp(-10, -8, 8)` → `-8` (clamped to min)
- `Clamp(5, -8, 8)` → `5` (within range)
- `Clamp(12, -8, 8)` → `8` (clamped to max)

**Why keep Y unchanged:**

- Only modify X (horizontal)
- Basket stays at same height

**Example:**

```
Mouse at screen X=100 → World X=-3.5
Clamp to [-8, 8] → X=-3.5 (within range)
Basket moves to X=-3.5
```

---

### Lines 123-135: SpawnRainDrop Method

```csharp
void SpawnRainDrop()
{
    if (rainDropPrefab == null) return;

    float randomX = Random.Range(spawnMinX, spawnMaxX);
    Vector3 spawnPos = new Vector3(randomX, spawnY, 0f);

    GameObject drop = Instantiate(rainDropPrefab, spawnPos, Quaternion.identity);

    var rainDrop = drop.GetComponent<RainDrop>();
    if (rainDrop != null)
        rainDrop.Initialize(dropSpeed, this);
}
```

**The spawning process:**

1. Generate random X between -8 and 8
2. Create position at (randomX, 6, 0)
3. Instantiate prefab at that position
4. Get RainDrop component
5. Initialize with speed and manager reference

---

### Lines 140-152: OnDropCaught Method

```csharp
public void OnDropCaught()
{
    dropsCollected++;

    // Award GreenPoints immediately
    if (GreenPointsManager.Instance != null)
        GreenPointsManager.Instance.AddPoints(pointsPerDrop);

    UpdateScoreUI();
    AudioManager.Instance?.PlayClick();

    Debug.Log($"Drop caught! +{pointsPerDrop} GP");
}
```

**Called by:** `RainDrop.Collect()` when caught

**What it does:**

1. Increment drop count
2. Add GreenPoints to global manager
3. Update UI
4. Play sound feedback
5. Log for debugging

---

### Lines 154-163: EndGame Method

```csharp
void EndGame()
{
    gameActive = false;

    int totalEarned = dropsCollected * pointsPerDrop;

    ShowGameOver(totalEarned);

    Debug.Log($"Game Over! Drops: {dropsCollected}, Total GP: {totalEarned}");
}
```

**Final calculation:**

```
dropsCollected = 15
pointsPerDrop = 10
totalEarned = 15 × 10 = 150 GP
```

---

### Lines 165-177: ShowGameOver Method

```csharp
void ShowGameOver(int earnedGP)
{
    if (gameOverPanel != null)
        gameOverPanel.SetActive(true);

    if (finalScoreText != null)
        finalScoreText.text = $"Drops Collected: {dropsCollected}";

    if (rewardText != null)
        rewardText.text = $"GreenPoints: +{earnedGP}";

    Invoke(nameof(ReturnToVillage), 3f);
}
```

**What happens:**

1. Show game over panel
2. Display final stats
3. After 3 seconds, return to Village scene

---

### Lines 184-200: UI Update Methods

```csharp
void UpdateTimerUI()
{
    if (timerText != null)
    {
        int seconds = Mathf.CeilToInt(timeRemaining);
        timerText.text = $"Time: {seconds}s";
    }
}

void UpdateScoreUI()
{
    if (scoreText != null)
        scoreText.text = $"Drops: {dropsCollected}";

    if (greenPointsText != null && GreenPointsManager.Instance != null)
        greenPointsText.text = $"GP: {GreenPointsManager.Instance.GreenPoints}";
}
```

**Timer display:**

- `Mathf.CeilToInt(29.3)` → 30
- Shows whole seconds, more intuitive

---

# How Everything Works Together

## Complete Game Flow

```
1. SCENE LOADS
   └── RainCatcherGame.Start()
       └── StartGame()
           ├── Reset timer and score
           ├── Hide game over panel
           ├── Auto-setup basket:
           │   ├── Add Rigidbody2D (Kinematic, no gravity)
           │   └── Set Collider2D.isTrigger = true
           └── Update UI

2. EVERY FRAME (Update):
   ├── Decrease timeRemaining
   ├── Update timer text
   ├── MoveBasket()
   │   ├── Get mouse world position
   │   ├── Clamp X to boundaries
   │   └── Set basket position
   └── Spawn timer:
       ├── Add deltaTime
       └── If >= 0.8s:
           └── SpawnRainDrop()
               ├── Random X position
               ├── Instantiate at (X, 6, 0)
               └── rainDrop.Initialize(speed, this)

3. RAINDROP FALLS (RainDrop.Update):
   ├── Move down: Y -= speed × deltaTime
   └── If Y < -6: Destroy (missed)

4. RAINDROP HITS BASKET:
   └── RainDrop.OnTriggerEnter2D()
       └── If tag == "Basket":
           └── Collect()
               ├── collected = true
               ├── gameManager.OnDropCaught()
               │   ├── dropsCollected++
               │   ├── GreenPointsManager.AddPoints(10)
               │   ├── UpdateScoreUI()
               │   └── PlayClick()
               └── Destroy(raindrop)

5. BASKET DETECTS COLLISION (Basket.OnTriggerEnter2D):
   └── If tag == "RainDrop":
       └── PlayCatchFeedback()
           ├── Instantiate(catchEffect)
           └── basketAnimator.SetTrigger("Catch")

6. TIMER REACHES ZERO:
   └── RainCatcherGame.EndGame()
       ├── gameActive = false
       ├── Calculate total GP
       └── ShowGameOver()
           ├── Show panel
           ├── Display stats
           └── Invoke(ReturnToVillage, 3f)

7. AFTER 3 SECONDS:
   └── ReturnToVillage()
       └── SceneManager.LoadScene("Village")
```

---

## Physics Setup Requirements

For collision detection to work, the scene must have:

| Object              | Component   | Settings                        |
| ------------------- | ----------- | ------------------------------- |
| **Basket**          | Collider2D  | `isTrigger = true`              |
| **Basket**          | Rigidbody2D | `Kinematic`, `gravityScale = 0` |
| **Basket**          | Tag         | `"Basket"`                      |
| **RainDrop Prefab** | Collider2D  | `isTrigger = true`              |
| **RainDrop Prefab** | Rigidbody2D | Can be Dynamic or Kinematic     |
| **RainDrop Prefab** | Tag         | `"RainDrop"`                    |

---

# Common Mistakes & Notes

## Mistake 1: Missing Tags

**Problem:** "Basket" or "RainDrop" tags not created/assigned
**Symptom:** CompareTag returns false, no collection
**Fix:** Create tags in Tags & Layers settings, assign to objects

## Mistake 2: No Rigidbody2D

**Problem:** Neither basket nor raindrop has Rigidbody2D
**Symptom:** OnTriggerEnter2D never fires
**Fix:** Add Rigidbody2D to at least one object (script auto-adds to basket)

## Mistake 3: isTrigger = false

**Problem:** Colliders are solid, not triggers
**Symptom:** Drops bounce off basket
**Fix:** Set `isTrigger = true` on both colliders

## Mistake 4: Camera.main is null

**Problem:** Main camera not tagged "MainCamera"
**Symptom:** MoveBasket throws NullReferenceException
**Fix:** Select camera → Tag → MainCamera

## Mistake 5: Wrong Layer Matrix

**Problem:** Objects on non-interacting layers
**Symptom:** Collisions don't register
**Fix:** Check Edit → Project Settings → Physics 2D → Layer Collision Matrix

---

# Summary Table

| Script               | Responsibility                                        |
| -------------------- | ----------------------------------------------------- |
| `Basket.cs`          | Visual feedback on catch (effects, animation)         |
| `RainDrop.cs`        | Falling behavior, collision detection, notify manager |
| `RainCatcherGame.cs` | Game loop, spawning, basket control, scoring, UI      |

| Communication Flow                                       |
| -------------------------------------------------------- |
| GameManager spawns RainDrop with Initialize(speed, this) |
| RainDrop falls via Update()                              |
| RainDrop detects basket via OnTriggerEnter2D             |
| RainDrop calls gameManager.OnDropCaught()                |
| GameManager adds points, updates UI                      |
| Basket also detects collision for visual feedback        |
| Both detection events fire simultaneously                |
