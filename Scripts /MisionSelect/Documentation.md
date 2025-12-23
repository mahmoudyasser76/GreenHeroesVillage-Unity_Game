# 📚 MissionSelect Scripts - Complete Line-by-Line Documentation

This document provides **extremely detailed, line-by-line explanations** of all scripts in the `Assets/Scripts/MissionSelect` folder.

---

# Table of Contents

1. [MissionData.cs](#-script-1-missiondatacs) - ScriptableObject for mission configuration
2. [MissionButtonUI.cs](#-script-2-missionbuttonuics) - Individual button controller
3. [MissionSelectManager.cs](#-script-3-missionselectmanagercs) - Scene manager

---

# 📗 Script 1: MissionData.cs

**File Path:** `Assets/Scripts/MissionSelect/MissionData.cs`

## Script Purpose

This script is a **ScriptableObject** that defines the data for a single mission/mini-game. It stores:

- Mission name and description
- Scene to load when playing
- Icon sprite for the button
- GreenPoints required to unlock
- Methods to check unlock status

**ScriptableObject** is a special Unity class that stores data as an asset file, not attached to GameObjects.

---

## Complete Script Code

```csharp
using UnityEngine;

[CreateAssetMenu(fileName = "Mission_New", menuName = "GreenHeroes/MissionData")]
public class MissionData : ScriptableObject
{
    [Header("Mission Info")]
    [Tooltip("Display name shown on the button")]
    public string missionName = "New Mission";

    [Tooltip("Scene name to load when playing this mission")]
    public string sceneName;

    [Tooltip("Optional icon for the mission button")]
    public Sprite icon;

    [Header("Unlock Settings")]
    [Tooltip("GreenPoints required to unlock this mission (0 = available from start)")]
    public int requiredGreenPoints = 0;

    [Header("Display")]
    [Tooltip("Short description of the mission")]
    [TextArea(2, 4)]
    public string description;

    public bool IsUnlocked()
    {
        if (GreenPointsManager.Instance == null)
            return requiredGreenPoints <= 0;

        return GreenPointsManager.Instance.GreenPoints >= requiredGreenPoints;
    }

    public string GetLockedText()
    {
        return $"Requires {requiredGreenPoints} GP";
    }
}
```

---

## Imports / Using Statements

### Line 1: `using UnityEngine;`

```csharp
using UnityEngine;
```

**What it does:** Imports Unity's core namespace containing `ScriptableObject`, `Sprite`, `Header`, `Tooltip`, `TextArea`, and `CreateAssetMenu`.

---

## Class Declaration

### Line 7-8: CreateAssetMenu Attribute and Class

```csharp
[CreateAssetMenu(fileName = "Mission_New", menuName = "GreenHeroes/MissionData")]
public class MissionData : ScriptableObject
```

#### `[CreateAssetMenu(...)]`

**What it does:**

- Adds an entry to Unity's **Assets > Create** menu
- Allows you to create new MissionData assets without code

**Parameters:**

- `fileName = "Mission_New"`: Default name when creating new asset
- `menuName = "GreenHeroes/MissionData"`: Menu path (GreenHeroes submenu > MissionData)

**How to use:**

1. Right-click in Project window
2. Create > GreenHeroes > MissionData
3. New `Mission_New.asset` file appears
4. Rename it (e.g., `Mission_EcoCollector.asset`)
5. Configure values in Inspector

#### `: ScriptableObject`

**What is ScriptableObject:**

- Unity class for data containers that exist as **asset files**
- NOT attached to GameObjects in scenes
- Stored in the Project folder as `.asset` files
- Shared across all scenes that reference them

**How it differs from MonoBehaviour:**

| MonoBehaviour           | ScriptableObject        |
| ----------------------- | ----------------------- |
| Attached to GameObjects | Exists as asset file    |
| Lives in scenes         | Lives in Project folder |
| Instance per GameObject | One instance shared     |
| Has Update(), Start()   | No lifecycle methods    |
| Destroyed with scene    | Persists in project     |

**Why use ScriptableObject here:**

- Mission data doesn't need to be in a scene
- Same mission data used by all menu systems
- Easy to edit in Inspector
- Changes saved to asset file

---

## Variables & Fields

### Lines 10-12: Mission Name

```csharp
[Header("Mission Info")]
[Tooltip("Display name shown on the button")]
public string missionName = "New Mission";
```

- `[Header]`: Creates bold section header in Inspector
- `[Tooltip]`: Hover text explaining the field
- `public string`: Text field visible in Inspector
- `= "New Mission"`: Default value for new assets

---

### Lines 14-15: Scene Name

```csharp
[Tooltip("Scene name to load when playing this mission")]
public string sceneName;
```

**What it stores:**

- The EXACT name of the scene to load (e.g., "MiniGame_EcoSwipe")
- Must match a scene in Build Settings

**No default value:**

- Designer MUST fill this in
- Empty string would cause LoadScene to fail

---

### Lines 17-18: Icon Sprite

```csharp
[Tooltip("Optional icon for the mission button")]
public Sprite icon;
```

**What is Sprite:**

- Unity's 2D image class
- Used for UI images, 2D game graphics
- Drag image from Project to this slot

**Why optional:**

- Some buttons may not need icons
- UI can handle null icon gracefully

---

### Lines 21-22: Required GreenPoints

```csharp
[Header("Unlock Settings")]
[Tooltip("GreenPoints required to unlock this mission (0 = available from start)")]
public int requiredGreenPoints = 0;
```

**What it does:**

- Defines how many GreenPoints player needs to unlock this mission
- `= 0`: Default means unlocked from start

**Game design usage:**

- First mission: 0 (always available)
- Second mission: 50 (need to play first mission)
- Third mission: 100 (need to play more)

---

### Lines 25-27: Description

```csharp
[Header("Display")]
[Tooltip("Short description of the mission")]
[TextArea(2, 4)]
public string description;
```

#### `[TextArea(2, 4)]`

**What it does:**

- Changes Inspector from single-line to multi-line text box
- `2`: Minimum visible lines
- `4`: Maximum visible lines before scrolling

**Why TextArea:**

- Descriptions can be long
- Single-line field is hard to read/edit
- Better UX for designers

---

## Custom Methods

### Lines 32-38: IsUnlocked Method

```csharp
public bool IsUnlocked()
{
    if (GreenPointsManager.Instance == null)
        return requiredGreenPoints <= 0;

    return GreenPointsManager.Instance.GreenPoints >= requiredGreenPoints;
}
```

**What it does:**

- Checks if player has enough GreenPoints to play this mission
- Returns `true` if unlocked, `false` if locked

**Line 34-35: Null safety check**

```csharp
if (GreenPointsManager.Instance == null)
    return requiredGreenPoints <= 0;
```

- If GreenPointsManager doesn't exist (during testing)
- Return true only if requirement is 0 or less

**Line 37: Main check**

```csharp
return GreenPointsManager.Instance.GreenPoints >= requiredGreenPoints;
```

- Compare player's points to required points
- Example: Player has 75 GP, mission needs 50 → returns `true`
- Example: Player has 75 GP, mission needs 100 → returns `false`

---

### Lines 43-46: GetLockedText Method

```csharp
public string GetLockedText()
{
    return $"Requires {requiredGreenPoints} GP";
}
```

**What it does:**

- Returns human-readable text explaining unlock requirement
- Example: "Requires 100 GP"

**String interpolation (`$`):**

- `$"Requires {requiredGreenPoints} GP"` inserts the variable value
- Cleaner than: `"Requires " + requiredGreenPoints + " GP"`

---

---

# 📗 Script 2: MissionButtonUI.cs

**File Path:** `Assets/Scripts/MissionSelect/MissionButtonUI.cs`

## Script Purpose

This script controls a **single mission button** in the UI. It:

- Displays mission name and icon
- Shows lock/unlock visual state
- Handles click to load mission or show "locked" popup
- Updates visuals when GreenPoints change

---

## Complete Script Code

```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
using TMPro;

public class MissionButtonUI : MonoBehaviour
{
    [Header("UI References")]
    public Button button;
    public TextMeshProUGUI nameText;
    public TextMeshProUGUI requirementText;
    public Image iconImage;
    public GameObject lockOverlay;

    [Header("Colors")]
    public Color unlockedColor = Color.white;
    public Color lockedColor = new Color(0.5f, 0.5f, 0.5f, 1f);

    private MissionData missionData;
    private MissionSelectManager selectManager;

    public void Initialize(MissionData data, MissionSelectManager manager)
    {
        missionData = data;
        selectManager = manager;

        if (nameText != null)
            nameText.text = data.missionName;

        if (iconImage != null && data.icon != null)
            iconImage.sprite = data.icon;

        if (button != null)
        {
            button.onClick.RemoveAllListeners();
            button.onClick.AddListener(OnButtonClicked);
        }

        UpdateLockState();
    }

    public void UpdateLockState()
    {
        if (missionData == null) return;

        bool isUnlocked = missionData.IsUnlocked();

        if (requirementText != null)
        {
            if (isUnlocked)
            {
                requirementText.text = "";
                requirementText.gameObject.SetActive(false);
            }
            else
            {
                requirementText.text = missionData.GetLockedText();
                requirementText.gameObject.SetActive(true);
            }
        }

        if (lockOverlay != null)
            lockOverlay.SetActive(!isUnlocked);

        if (button != null)
        {
            var colors = button.colors;
            colors.normalColor = isUnlocked ? unlockedColor : lockedColor;
            colors.highlightedColor = isUnlocked ? unlockedColor : lockedColor;
            button.colors = colors;
        }

        if (nameText != null)
            nameText.color = isUnlocked ? Color.white : new Color(0.7f, 0.7f, 0.7f);
    }

    private void OnButtonClicked()
    {
        if (missionData == null) return;

        AudioManager.Instance?.PlayClick();

        if (missionData.IsUnlocked())
        {
            Debug.Log($"Loading mission: {missionData.missionName}");
            SceneManager.LoadScene(missionData.sceneName);
        }
        else
        {
            if (selectManager != null)
            {
                selectManager.ShowLockedPopup(missionData.requiredGreenPoints);
            }
        }
    }
}
```

---

## Imports / Using Statements

### Line 1-4: All Imports

```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
using TMPro;
```

- `UnityEngine`: Core Unity (MonoBehaviour, Color, GameObject)
- `UnityEngine.UI`: UI components (Button, Image)
- `UnityEngine.SceneManagement`: Scene loading (SceneManager)
- `TMPro`: TextMeshPro text (TextMeshProUGUI)

---

## Variables & Fields

### Lines 12-17: UI References

```csharp
[Header("UI References")]
public Button button;
public TextMeshProUGUI nameText;
public TextMeshProUGUI requirementText;
public Image iconImage;
public GameObject lockOverlay;
```

| Variable          | Type            | Purpose                           |
| ----------------- | --------------- | --------------------------------- |
| `button`          | Button          | Clickable button component        |
| `nameText`        | TextMeshProUGUI | Shows mission name                |
| `requirementText` | TextMeshProUGUI | Shows "Requires X GP" when locked |
| `iconImage`       | Image           | Mission icon                      |
| `lockOverlay`     | GameObject      | Lock icon/overlay when locked     |

**Why public:**

- Assigned in Inspector on the prefab
- Different buttons can have different layouts

---

### Lines 19-20: Colors

```csharp
[Header("Colors")]
public Color unlockedColor = Color.white;
public Color lockedColor = new Color(0.5f, 0.5f, 0.5f, 1f);
```

**Color values:**

- `Color.white`: RGB(1,1,1) - full brightness
- `new Color(0.5f, 0.5f, 0.5f, 1f)`: Gray (50% brightness, full opacity)

**Why configurable:**

- Designers can tweak without code
- Different themes possible

---

### Lines 23-24: Private References

```csharp
private MissionData missionData;
private MissionSelectManager selectManager;
```

**Why private:**

- Set programmatically via `Initialize()`
- Not meant for Inspector assignment
- Internal state storage

---

## Custom Methods

### Lines 29-44: Initialize Method

```csharp
public void Initialize(MissionData data, MissionSelectManager manager)
{
    missionData = data;
    selectManager = manager;

    if (nameText != null)
        nameText.text = data.missionName;

    if (iconImage != null && data.icon != null)
        iconImage.sprite = data.icon;

    if (button != null)
    {
        button.onClick.RemoveAllListeners();
        button.onClick.AddListener(OnButtonClicked);
    }

    UpdateLockState();
}
```

**When called:**

- By MissionSelectManager after spawning this button

**Parameters:**

- `data`: The MissionData asset for this button
- `manager`: Reference to call ShowLockedPopup if needed

**What it does:**

1. Stores references for later use
2. Sets mission name text
3. Sets icon sprite (if both exist)
4. Configures button click listener
5. Updates visual lock state

**Line 37-38: Button listener setup**

```csharp
button.onClick.RemoveAllListeners();
button.onClick.AddListener(OnButtonClicked);
```

- `RemoveAllListeners()`: Clear any existing handlers (prevents duplicates)
- `AddListener()`: Add our click handler

---

### Lines 49-73: UpdateLockState Method

```csharp
public void UpdateLockState()
{
    if (missionData == null) return;

    bool isUnlocked = missionData.IsUnlocked();

    // Update requirement text
    if (requirementText != null)
    {
        if (isUnlocked)
        {
            requirementText.text = "";
            requirementText.gameObject.SetActive(false);
        }
        else
        {
            requirementText.text = missionData.GetLockedText();
            requirementText.gameObject.SetActive(true);
        }
    }

    // Update lock overlay
    if (lockOverlay != null)
        lockOverlay.SetActive(!isUnlocked);

    // Update button color
    if (button != null)
    {
        var colors = button.colors;
        colors.normalColor = isUnlocked ? unlockedColor : lockedColor;
        colors.highlightedColor = isUnlocked ? unlockedColor : lockedColor;
        button.colors = colors;
    }

    // Update name text color
    if (nameText != null)
        nameText.color = isUnlocked ? Color.white : new Color(0.7f, 0.7f, 0.7f);
}
```

**When called:**

- During Initialize()
- When GreenPoints change (via MissionSelectManager)

**Visual changes based on lock state:**

| Element            | Unlocked | Locked                |
| ------------------ | -------- | --------------------- |
| requirementText    | Hidden   | Shows "Requires X GP" |
| lockOverlay        | Hidden   | Visible               |
| button.normalColor | White    | Gray                  |
| nameText.color     | White    | Light gray            |

**Line 64-67: Button color modification**

```csharp
var colors = button.colors;
colors.normalColor = isUnlocked ? unlockedColor : lockedColor;
colors.highlightedColor = isUnlocked ? unlockedColor : lockedColor;
button.colors = colors;
```

- `button.colors` returns a ColorBlock struct (copied, not referenced)
- Modify the copy
- Assign back to button

---

### Lines 78-98: OnButtonClicked Method

```csharp
private void OnButtonClicked()
{
    if (missionData == null) return;

    AudioManager.Instance?.PlayClick();

    if (missionData.IsUnlocked())
    {
        Debug.Log($"Loading mission: {missionData.missionName}");
        SceneManager.LoadScene(missionData.sceneName);
    }
    else
    {
        if (selectManager != null)
        {
            selectManager.ShowLockedPopup(missionData.requiredGreenPoints);
        }
    }
}
```

**Flow:**

1. Play click sound
2. Check if unlocked
3. If YES: Load the mission scene
4. If NO: Show the "locked" popup with required points

**Line 82: Null-conditional operator**

```csharp
AudioManager.Instance?.PlayClick();
```

- `?.` means: "If Instance is not null, call PlayClick()"
- Safe way to handle optional managers

---

---

# 📗 Script 3: MissionSelectManager.cs

**File Path:** `Assets/Scripts/MissionSelect/MissionSelectManager.cs`

## Script Purpose

This script is the **main controller for the Mission Select scene**. It:

- Holds list of all missions
- Dynamically creates mission buttons from prefab
- Shows current GreenPoints
- Manages the "locked mission" popup
- Updates button states when GreenPoints change
- Handles navigation back to main menu

---

## Complete Script Code

```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
using TMPro;

public class MissionSelectManager : MonoBehaviour
{
    [Header("Mission Data")]
    public List<MissionData> allMissions = new List<MissionData>();

    [Header("UI Setup")]
    public Transform missionButtonContainer;
    public GameObject missionButtonPrefab;

    [Header("GreenPoints Display")]
    public TextMeshProUGUI greenPointsText;

    [Header("Locked Popup")]
    public GameObject lockedPopup;
    public TextMeshProUGUI lockedPopupText;
    public Button popupOkButton;

    [Header("Navigation")]
    public Button backButton;

    private List<MissionButtonUI> missionButtons = new List<MissionButtonUI>();

    void Start()
    {
        if (lockedPopup != null)
            lockedPopup.SetActive(false);

        if (popupOkButton != null)
        {
            popupOkButton.onClick.RemoveAllListeners();
            popupOkButton.onClick.AddListener(HideLockedPopup);
        }

        if (backButton != null)
        {
            backButton.onClick.RemoveAllListeners();
            backButton.onClick.AddListener(GoToMainMenu);
        }

        if (GreenPointsManager.Instance != null)
        {
            GreenPointsManager.Instance.OnGreenPointsChanged += OnGreenPointsChanged;
        }

        GenerateMissionButtons();
        UpdateGreenPointsDisplay();

        Debug.Log("MissionSelectManager: Initialized with " + allMissions.Count + " missions");
    }

    void OnDestroy()
    {
        if (GreenPointsManager.Instance != null)
        {
            GreenPointsManager.Instance.OnGreenPointsChanged -= OnGreenPointsChanged;
        }
    }

    void GenerateMissionButtons()
    {
        if (missionButtonContainer == null || missionButtonPrefab == null)
        {
            Debug.LogError("MissionSelectManager: Missing button container or prefab!");
            return;
        }

        foreach (Transform child in missionButtonContainer)
        {
            Destroy(child.gameObject);
        }
        missionButtons.Clear();

        foreach (var missionData in allMissions)
        {
            GameObject buttonObj = Instantiate(missionButtonPrefab, missionButtonContainer);
            buttonObj.name = "MissionButton_" + missionData.missionName;

            MissionButtonUI buttonUI = buttonObj.GetComponent<MissionButtonUI>();
            if (buttonUI != null)
            {
                buttonUI.Initialize(missionData, this);
                missionButtons.Add(buttonUI);
            }
        }
    }

    void OnGreenPointsChanged(int newTotal)
    {
        UpdateGreenPointsDisplay();
        RefreshAllButtonStates();
    }

    void UpdateGreenPointsDisplay()
    {
        if (greenPointsText != null && GreenPointsManager.Instance != null)
        {
            greenPointsText.text = $"GreenPoints: {GreenPointsManager.Instance.GreenPoints}";
        }
    }

    void RefreshAllButtonStates()
    {
        foreach (var button in missionButtons)
        {
            button.UpdateLockState();
        }
    }

    public void ShowLockedPopup(int requiredPoints)
    {
        if (lockedPopup == null) return;

        AudioManager.Instance?.PlayCancel();

        if (lockedPopupText != null)
        {
            lockedPopupText.text = $"You need {requiredPoints} GreenPoints\nto access this level";
        }

        lockedPopup.SetActive(true);
    }

    public void HideLockedPopup()
    {
        if (lockedPopup != null)
        {
            AudioManager.Instance?.PlayClick();
            lockedPopup.SetActive(false);
        }
    }

    public void GoToMainMenu()
    {
        AudioManager.Instance?.PlayClick();
        SceneManager.LoadScene("MainMenu");
    }
}
```

---

## Imports / Using Statements

### Lines 1-5: All Imports

```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
using TMPro;
```

- `System.Collections.Generic`: For `List<T>` generic collections
- `UnityEngine`: Core Unity types
- `UnityEngine.UI`: UI components (Button)
- `UnityEngine.SceneManagement`: Scene loading
- `TMPro`: TextMeshPro text components

---

## Variables & Fields

### Lines 13-15: Mission Data

```csharp
[Header("Mission Data")]
public List<MissionData> allMissions = new List<MissionData>();
```

**What is `List<MissionData>`:**

- A dynamic array that holds MissionData assets
- Can add/remove items
- Generic type: `<MissionData>` specifies what it holds

**How to populate in Inspector:**

1. Set list size (e.g., 4)
2. Drag each MissionData asset to slots

**`= new List<MissionData>()`:**

- Initializes empty list (prevents null reference)
- Designer adds items in Inspector

---

### Lines 17-19: UI Setup

```csharp
[Header("UI Setup")]
public Transform missionButtonContainer;
public GameObject missionButtonPrefab;
```

- `missionButtonContainer`: Parent object for spawned buttons (e.g., Vertical Layout Group)
- `missionButtonPrefab`: Button template to instantiate

---

### Lines 21-22: GreenPoints Display

```csharp
[Header("GreenPoints Display")]
public TextMeshProUGUI greenPointsText;
```

- Text element showing current points
- Updated whenever points change

---

### Lines 24-27: Locked Popup

```csharp
[Header("Locked Popup")]
public GameObject lockedPopup;
public TextMeshProUGUI lockedPopupText;
public Button popupOkButton;
```

- `lockedPopup`: Panel to show when clicking locked mission
- `lockedPopupText`: Text inside popup showing requirement
- `popupOkButton`: Button to close popup

---

### Lines 29-30: Navigation

```csharp
[Header("Navigation")]
public Button backButton;
```

- Button to return to main menu

---

### Line 33: Private Button List

```csharp
private List<MissionButtonUI> missionButtons = new List<MissionButtonUI>();
```

- Stores references to spawned buttons
- Used to update all buttons when points change

---

## Unity Lifecycle Methods

### Lines 35-53: Start Method

```csharp
void Start()
{
    // Hide popup initially
    if (lockedPopup != null)
        lockedPopup.SetActive(false);

    // Setup popup OK button
    if (popupOkButton != null)
    {
        popupOkButton.onClick.RemoveAllListeners();
        popupOkButton.onClick.AddListener(HideLockedPopup);
    }

    // Setup back button
    if (backButton != null)
    {
        backButton.onClick.RemoveAllListeners();
        backButton.onClick.AddListener(GoToMainMenu);
    }

    // Subscribe to GreenPoints changes
    if (GreenPointsManager.Instance != null)
    {
        GreenPointsManager.Instance.OnGreenPointsChanged += OnGreenPointsChanged;
    }

    // Generate mission buttons
    GenerateMissionButtons();

    // Initial display update
    UpdateGreenPointsDisplay();

    Debug.Log("MissionSelectManager: Initialized with " + allMissions.Count + " missions");
}
```

**Execution order:**

1. Hide popup (shouldn't be visible initially)
2. Setup popup OK button click handler
3. Setup back button click handler
4. Subscribe to GreenPoints event (for live updates)
5. Generate mission buttons dynamically
6. Update GreenPoints display text
7. Log initialization

---

### Lines 55-62: OnDestroy Method

```csharp
void OnDestroy()
{
    if (GreenPointsManager.Instance != null)
    {
        GreenPointsManager.Instance.OnGreenPointsChanged -= OnGreenPointsChanged;
    }
}
```

**Why unsubscribe:**

- Prevents memory leaks
- Prevents errors when this object is destroyed but event fires
- Clean up after ourselves

---

## Custom Methods

### Lines 67-94: GenerateMissionButtons Method

```csharp
void GenerateMissionButtons()
{
    if (missionButtonContainer == null || missionButtonPrefab == null)
    {
        Debug.LogError("MissionSelectManager: Missing button container or prefab!");
        return;
    }

    // Clear existing buttons
    foreach (Transform child in missionButtonContainer)
    {
        Destroy(child.gameObject);
    }
    missionButtons.Clear();

    // Create button for each mission
    foreach (var missionData in allMissions)
    {
        GameObject buttonObj = Instantiate(missionButtonPrefab, missionButtonContainer);
        buttonObj.name = "MissionButton_" + missionData.missionName;

        MissionButtonUI buttonUI = buttonObj.GetComponent<MissionButtonUI>();
        if (buttonUI != null)
        {
            buttonUI.Initialize(missionData, this);
            missionButtons.Add(buttonUI);
        }
    }
}
```

**Flow:**

1. Validate required references
2. Destroy any existing buttons (in case of regeneration)
3. Loop through all missions
4. Instantiate button prefab
5. Get MissionButtonUI component
6. Initialize it with data
7. Store reference for later updates

**Line 84: `Instantiate(missionButtonPrefab, missionButtonContainer)`**

- Creates copy of prefab
- Sets parent to container
- Returns the new GameObject

---

### Lines 99-103: OnGreenPointsChanged Method

```csharp
void OnGreenPointsChanged(int newTotal)
{
    UpdateGreenPointsDisplay();
    RefreshAllButtonStates();
}
```

**When called:**

- Automatically when GreenPoints change (via event subscription)

**What it does:**

- Updates the points display text
- Refreshes all button lock states (missions may now be unlocked!)

---

### Lines 108-113: UpdateGreenPointsDisplay Method

```csharp
void UpdateGreenPointsDisplay()
{
    if (greenPointsText != null && GreenPointsManager.Instance != null)
    {
        greenPointsText.text = $"GreenPoints: {GreenPointsManager.Instance.GreenPoints}";
    }
}
```

- Sets text to show current GreenPoints
- Example: "GreenPoints: 150"

---

### Lines 118-123: RefreshAllButtonStates Method

```csharp
void RefreshAllButtonStates()
{
    foreach (var button in missionButtons)
    {
        button.UpdateLockState();
    }
}
```

- Loops through all spawned buttons
- Calls UpdateLockState() on each
- Previously locked missions may now be unlocked

---

### Lines 128-139: ShowLockedPopup Method

```csharp
public void ShowLockedPopup(int requiredPoints)
{
    if (lockedPopup == null) return;

    AudioManager.Instance?.PlayCancel();

    if (lockedPopupText != null)
    {
        lockedPopupText.text = $"You need {requiredPoints} GreenPoints\nto access this level";
    }

    lockedPopup.SetActive(true);
}
```

**When called:**

- By MissionButtonUI when clicking a locked mission

**What it does:**

1. Plays "cancel" sound
2. Sets popup text with required points
3. Shows the popup panel

---

### Lines 144-151: HideLockedPopup Method

```csharp
public void HideLockedPopup()
{
    if (lockedPopup != null)
    {
        AudioManager.Instance?.PlayClick();
        lockedPopup.SetActive(false);
    }
}
```

- Plays click sound
- Hides the popup panel
- Called by popup OK button

---

### Lines 156-160: GoToMainMenu Method

```csharp
public void GoToMainMenu()
{
    AudioManager.Instance?.PlayClick();
    SceneManager.LoadScene("MainMenu");
}
```

- Plays click sound
- Loads MainMenu scene
- Called by back button

---

## How Everything Works Together

```
1. MissionSelect scene loads
   ↓
2. MissionSelectManager.Start() runs
   ↓
3. Hide popup, setup buttons
   ↓
4. Subscribe to GreenPoints events
   ↓
5. GenerateMissionButtons() loops through allMissions
   ↓
6. For each mission: Instantiate prefab → Initialize button
   ↓
7. Each button shows name, icon, lock state
   ↓
8. PLAYER CLICKS UNLOCKED MISSION
   ↓
9. MissionButtonUI.OnButtonClicked()
   ↓
10. SceneManager.LoadScene(missionData.sceneName)
    ↓
11. Mini-game loads!

ALTERNATE: PLAYER CLICKS LOCKED MISSION
   ↓
9. MissionButtonUI.OnButtonClicked()
   ↓
10. selectManager.ShowLockedPopup(requiredPoints)
    ↓
11. Popup appears: "You need X GreenPoints..."
    ↓
12. Player clicks OK
    ↓
13. HideLockedPopup() hides popup
```

---

## Common Mistakes & Notes

### Mistake 1: Forgetting to add missions to list

**Problem:** `allMissions` is empty
**Fix:** Drag MissionData assets to the list in Inspector

### Mistake 2: Button prefab missing MissionButtonUI

**Problem:** `GetComponent<MissionButtonUI>()` returns null
**Fix:** Add MissionButtonUI script to prefab

### Mistake 3: Scene not in Build Settings

**Problem:** `SceneManager.LoadScene()` fails
**Fix:** Add mission scenes to File > Build Settings

### Mistake 4: Not unsubscribing from events

**Problem:** Memory leaks, errors after scene change
**Fix:** Always unsubscribe in OnDestroy()

---

## Summary

| Script               | Type             | Purpose                                           |
| -------------------- | ---------------- | ------------------------------------------------- |
| MissionData          | ScriptableObject | Data container for mission configuration          |
| MissionButtonUI      | MonoBehaviour    | Controls single button appearance and clicks      |
| MissionSelectManager | MonoBehaviour    | Scene manager creating buttons and handling popup |

**Data flow:**

```
MissionData (assets) → MissionSelectManager (spawns buttons) → MissionButtonUI (displays & handles clicks)
```
