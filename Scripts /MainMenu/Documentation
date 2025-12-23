# 📚 MainMenu Scripts - Complete Line-by-Line Documentation

This document provides **extremely detailed, line-by-line explanations** of all scripts in the `Assets/Scripts/MainMenu` folder. Written for beginners who want to fully understand how Unity works internally.

---

# 📗 Script: MainMenuManager.cs

**File Path:** `Assets/Scripts/MainMenu/MainMenuManager.cs`

---

## Script Purpose

This script is the **main controller for the Main Menu scene**. It is responsible for:

- Playing background music when the menu loads
- Handling button clicks (Play, Missions, Settings, Quit)
- Navigating to different scenes (Village, MissionSelect, Mini-games)
- Playing button click sounds for audio feedback
- Quitting the application

This script is the **entry point** of the game - it's the first interactive script the player encounters.

---

## Complete Script Code

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class MainMenuManager : MonoBehaviour
{
    [Header("Audio")]
    public AudioClip backgroundMusic;
    public AudioClip buttonClickSound;

    private void Start()
    {
       // backgroundMusic = "Assets/Audio/bg_music.wav";
        // Play background music when the main menu loads
        if (backgroundMusic != null && AudioManager.Instance != null)
        {
            AudioManager.Instance.PlayMusic(backgroundMusic, 0.5f);
        }
    }

    /// <summary>
    /// Navigate to the Village scene (main gameplay)
    /// </summary>
    public void PlayGame()
    {
        PlayButtonSound();
        Debug.Log("Loading Village scene...");
        SceneManager.LoadScene("Village");
    }

    /// <summary>
    /// Navigate to the Mission Select scene
    /// </summary>
    public void OpenMissions()
    {
        PlayButtonSound();
        Debug.Log("Loading MissionSelect scene...");
        SceneManager.LoadScene("MissionSelect");
    }

    public void LoadMiniGameEcoSwipe()
    {
        PlayButtonSound();
        Debug.Log("Loading EcoSwipe scene...");
        SceneManager.LoadScene("MiniGame_EcoSwipe");
    }

    public void LoadMaterialSortingGame()
    {
        PlayButtonSound();
        Debug.Log("Loading Material Sorting game...");
        SceneManager.LoadScene("ColorLearningGame");
    }

    /// <summary>
    /// Open settings panel (placeholder for future implementation)
    /// </summary>
    public void OpenSettings()
    {
        PlayButtonSound();
        Debug.Log("Settings panel - Coming soon!");
        // TODO: Implement settings panel with volume controls, etc.
    }

    /// <summary>
    /// Quit the application (works in build, not in editor)
    /// </summary>
    public void QuitGame()
    {
        PlayButtonSound();
        Debug.Log("Quitting game...");

        #if UNITY_EDITOR
        UnityEditor.EditorApplication.isPlaying = false;
        #else
        Application.Quit();
        #endif
    }

    /// <summary>
    /// Play button click sound through AudioManager
    /// </summary>
    private void PlayButtonSound()
    {
        if (AudioManager.Instance != null)
        {
            //buttonClickSound = "Assets/Audio/UI/ui_click.wav";
            // Try to use the button click sound from this script first
            if (buttonClickSound != null)
            {
                AudioManager.Instance.PlaySFX(buttonClickSound);
            }
            // Otherwise use the AudioManager's default click sound
            else
            {
                AudioManager.Instance.PlayClick();
            }
        }
    }

    /// <summary>
    /// Play hover sound for button hover effects (optional)
    /// </summary>
    public void OnButtonHover()
    {
        // Optional: Add hover sound effect
        // AudioManager.Instance?.PlaySFX(hoverSound, 0.3f);
    }
}
```

---

## Imports / Using Statements

### Line 1: `using UnityEngine;`

```csharp
using UnityEngine;
```

**What it does:**

- Imports Unity's core engine namespace
- This namespace contains ALL fundamental Unity types

**What it provides access to:**

- `MonoBehaviour` - Base class for Unity components
- `GameObject` - Represents objects in the scene
- `Transform` - Position, rotation, scale of objects
- `Debug` - Console logging and debugging tools
- `AudioClip` - Audio file references
- `Application` - Application-level controls (quit, paths, etc.)

**Why it's needed here:**

- This script inherits from `MonoBehaviour`
- Uses `Debug.Log()` for console output
- Uses `AudioClip` for sound references
- Uses `Application.Quit()` to exit the game

**What would happen if removed:**

- Compilation error: `MonoBehaviour`, `AudioClip`, `Debug`, `Application` would all be unrecognized

---

### Line 2: `using UnityEngine.SceneManagement;`

```csharp
using UnityEngine.SceneManagement;
```

**What it does:**

- Imports Unity's scene management namespace
- This namespace is SEPARATE from the main UnityEngine namespace

**What it provides access to:**

- `SceneManager` - Static class for loading/unloading scenes
- `Scene` - Represents a scene asset
- `LoadSceneMode` - Enum for loading modes (Single, Additive)

**Why it's needed here:**

- This script uses `SceneManager.LoadScene()` to navigate between scenes
- Without this, you cannot load other scenes

**What would happen if removed:**

- Compilation error: `The name 'SceneManager' does not exist in the current context`

**Why is it a separate namespace?**

- Unity organizes code into namespaces by functionality
- Keeps the main `UnityEngine` namespace cleaner
- You only import what you need

---

## Class Declaration

### Line 4: `public class MainMenuManager : MonoBehaviour`

```csharp
public class MainMenuManager : MonoBehaviour
```

**Breaking this down word by word:**

---

#### `public`

**What it does:**

- Makes this class accessible from ANY other script in the project
- Other scripts can reference `MainMenuManager` by name

**Why it's public:**

- Unity needs to access it to attach it to GameObjects
- UI buttons may need to call its methods

**What would happen if changed to `private`:**

- Compilation error: Top-level classes cannot be private
- Only nested classes can be private

---

#### `class`

**What it does:**

- Declares a reference type in C#
- A class is a blueprint for creating objects

**Unity context:**

- Every Unity script is a class
- Each class typically represents one component type

---

#### `MainMenuManager`

**What it does:**

- The unique identifier/name for this class
- This is what you see in the Inspector when you attach the script

**IMPORTANT Unity Rule:**

- The class name MUST match the file name exactly
- File: `MainMenuManager.cs` → Class: `MainMenuManager`
- If they don't match, Unity will show a warning and the script won't work

**Naming convention:**

- PascalCase (each word capitalized)
- Descriptive name indicating purpose: "Main Menu Manager"

---

#### `: MonoBehaviour`

**What it does:**

- This class INHERITS from Unity's `MonoBehaviour` base class
- The colon `:` means "inherits from" or "extends"

**What is MonoBehaviour?**

- The base class for ALL Unity components that attach to GameObjects
- Provides the "Unity magic" - lifecycle methods, component access, etc.

**What MonoBehaviour provides:**

| Feature           | Description                                                            |
| ----------------- | ---------------------------------------------------------------------- |
| Lifecycle Methods | `Awake()`, `Start()`, `Update()`, `FixedUpdate()`, `OnDestroy()`, etc. |
| GameObject Access | `this.gameObject` - the GameObject this script is on                   |
| Transform Access  | `this.transform` - position/rotation/scale                             |
| Component Methods | `GetComponent<T>()`, `AddComponent<T>()`                               |
| Coroutines        | `StartCoroutine()`, `StopCoroutine()`                                  |
| Enable/Disable    | Can be turned on/off in Inspector                                      |
| Inspector Support | Public fields appear in Inspector                                      |

**Why inherit from MonoBehaviour:**

- We need the `Start()` lifecycle method
- We need to attach this to a GameObject in the scene
- We need the Inspector to show our audio clips

**What would happen if we didn't inherit:**

```csharp
public class MainMenuManager  // No inheritance
{
    void Start() { }  // Unity would NEVER call this!
}
```

- Cannot attach to GameObjects
- Unity lifecycle methods won't work
- Just a plain C# class with no Unity integration

---

## Variables & Fields

### Lines 6-8: Audio Configuration

```csharp
[Header("Audio")]
public AudioClip backgroundMusic;
public AudioClip buttonClickSound;
```

---

#### Line 6: `[Header("Audio")]`

```csharp
[Header("Audio")]
```

**What it does:**

- This is an **Attribute** (also called Decorator or Annotation)
- `[Header]` is a Unity-specific attribute
- Adds a bold text label in the Inspector above the following fields

**What it looks like in Inspector:**

```
▼ Main Menu Manager (Script)
  ▼ Audio                         ← This bold header
     Background Music: None (Audio Clip)
     Button Click Sound: None (Audio Clip)
```

**Why use Headers:**

- Organizes the Inspector visually
- Groups related variables together
- Makes it easier for designers to understand what to configure

**What would happen if removed:**

- Script works exactly the same
- Variables just won't have a header above them in Inspector
- Less organized appearance

---

#### Line 7: `public AudioClip backgroundMusic;`

```csharp
public AudioClip backgroundMusic;
```

**Breaking it down:**

##### `public`

**What it does:**

- Makes this field visible in the Unity Inspector
- Unity's serialization system automatically shows public fields
- Other scripts can also access this field

**Unity Inspector behavior:**

- Shows as a slot where you can drag an audio file
- The value is SAVED with the scene/prefab
- Overrides any default value when set in Inspector

**Alternative to public (for Inspector visibility):**

```csharp
[SerializeField]
private AudioClip backgroundMusic;
```

- Still shows in Inspector
- But cannot be accessed from other scripts
- Better encapsulation

##### `AudioClip`

**What is AudioClip:**

- Unity's class representing an audio file
- Can be .wav, .mp3, .ogg, etc.
- This is a REFERENCE to an audio asset, not the audio data itself

**How it's used:**

- Drag an audio file from Project window to this slot in Inspector
- Pass to AudioManager to play: `AudioManager.Instance.PlayMusic(backgroundMusic)`

##### `backgroundMusic`

**What it does:**

- Variable name describing its purpose
- Holds reference to the menu's background music

**Naming convention:**

- camelCase (first word lowercase, subsequent words capitalized)
- Descriptive: tells you it's background music

##### No `= value;` (No default value)

**What this means:**

- Defaults to `null` (no audio clip assigned)
- MUST be assigned in the Inspector or it will be null

**Why no default:**

- AudioClip is a reference type
- You can't create AudioClips in code easily
- Assets must be assigned via Inspector or loaded at runtime

---

#### Line 8: `public AudioClip buttonClickSound;`

```csharp
public AudioClip buttonClickSound;
```

**Identical structure to backgroundMusic:**

- Public for Inspector visibility
- AudioClip type for audio file reference
- Used for button click feedback sounds

**Why separate from backgroundMusic:**

- Different purpose (SFX vs music)
- Different volume and play method
- Music loops; click sounds play once

---

## Unity Lifecycle Methods

### Lines 10-18: Start Method

```csharp
private void Start()
{
   // backgroundMusic = "Assets/Audio/bg_music.wav";
    // Play background music when the main menu loads
    if (backgroundMusic != null && AudioManager.Instance != null)
    {
        AudioManager.Instance.PlayMusic(backgroundMusic, 0.5f);
    }
}
```

---

#### Line 10: `private void Start()`

```csharp
private void Start()
```

**What is Start():**

- A Unity **lifecycle method** (also called "Magic Method" or "Message")
- Unity automatically calls this - you NEVER call it yourself

**When Unity calls Start():**

- Called **once** per script instance
- Called on the **first frame** the script is active
- Called AFTER `Awake()` but BEFORE the first `Update()`
- Only called if the script component is ENABLED

**Execution order of lifecycle methods:**

```
1. Awake()       - Called when object is created (even if disabled)
2. OnEnable()    - Called when script becomes enabled
3. Start()       - Called before first Update, only if enabled  ← WE ARE HERE
4. Update()      - Called every frame
5. LateUpdate()  - Called after all Updates
6. OnDisable()   - Called when disabled
7. OnDestroy()   - Called when destroyed
```

**Why Start() instead of Awake():**

- `Start()` guarantees other scripts have run their `Awake()`
- The `AudioManager.Instance` singleton is set up in Awake()
- By using `Start()`, we ensure AudioManager exists

**Why `private`:**

- This method should only be called by Unity, not by other scripts
- Other scripts should never call `Start()` directly
- `private` makes this intention clear

**Why `void`:**

- Lifecycle methods don't return values
- Unity doesn't expect any return value

---

#### Line 12: Commented-out code

```csharp
// backgroundMusic = "Assets/Audio/bg_music.wav";
```

**What this is:**

- A comment (compiler ignores it)
- Shows incorrect code that was removed

**Why this is wrong:**

```csharp
backgroundMusic = "Assets/Audio/bg_music.wav";  // ERROR!
```

- You CANNOT assign a string path to an AudioClip
- AudioClip is not a string
- Assets must be loaded via Resources.Load() or assigned in Inspector

**Correct alternatives:**

```csharp
// Option 1: Assign in Inspector (recommended)
// Just drag the audio file in the Inspector

// Option 2: Load from Resources folder
backgroundMusic = Resources.Load<AudioClip>("Audio/bg_music");

// Option 3: Use Addressables (advanced)
```

---

#### Lines 14-17: Playing Background Music

```csharp
if (backgroundMusic != null && AudioManager.Instance != null)
{
    AudioManager.Instance.PlayMusic(backgroundMusic, 0.5f);
}
```

##### Line 14: `if (backgroundMusic != null && AudioManager.Instance != null)`

**What this does:**

- Checks TWO conditions before playing music
- Both must be true (`&&` = AND operator)

**Condition 1: `backgroundMusic != null`**

- Is an audio clip assigned?
- Prevents null reference error if nothing is in the Inspector slot

**Condition 2: `AudioManager.Instance != null`**

- Does the AudioManager singleton exist?
- Prevents null reference error if AudioManager hasn't loaded yet

**What is `&&` (AND operator):**

- Both conditions must be true
- If the first is false, the second isn't even checked (short-circuit evaluation)

**Why both checks:**

- Defensive programming
- The game won't crash if:
  - Designer forgot to assign audio clip
  - AudioManager hasn't been set up
  - AudioManager was destroyed somehow

**What would happen without these checks:**

```csharp
AudioManager.Instance.PlayMusic(backgroundMusic, 0.5f);
// If AudioManager is null: NullReferenceException!
// If backgroundMusic is null: Passes null to PlayMusic (might crash there)
```

---

##### Line 16: `AudioManager.Instance.PlayMusic(backgroundMusic, 0.5f);`

**Breaking it down:**

###### `AudioManager`

- The AudioManager class (from Core scripts)
- Manages all game audio

###### `.Instance`

- Static property holding the singleton instance
- Returns the ONE AudioManager that exists

###### `.PlayMusic()`

- Method on AudioManager that plays background music
- Handles looping and volume

###### `backgroundMusic`

- The AudioClip we're passing to play
- The actual audio file reference

###### `0.5f`

- Volume level (0.0 = silent, 1.0 = full volume)
- The `f` suffix indicates it's a float (decimal number)
- 0.5 = 50% volume (not too loud)

**Why 0.5f volume:**

- Background music shouldn't be too loud
- Leaves headroom for sound effects
- Better user experience

---

## Custom Functions

### Lines 23-28: PlayGame Method

```csharp
/// <summary>
/// Navigate to the Village scene (main gameplay)
/// </summary>
public void PlayGame()
{
    PlayButtonSound();
    Debug.Log("Loading Village scene...");
    SceneManager.LoadScene("Village");
}
```

---

#### Lines 20-22: XML Documentation Comment

```csharp
/// <summary>
/// Navigate to the Village scene (main gameplay)
/// </summary>
```

**What this is:**

- XML Documentation Comment (triple slash `///`)
- Different from regular comments (`//`)

**What it does:**

- Provides documentation for the method
- IDEs show this when you hover over the method name
- Can be exported to documentation files

**What you see when hovering:**

```
void MainMenuManager.PlayGame()
Navigate to the Village scene (main gameplay)
```

---

#### Line 23: Method Signature

```csharp
public void PlayGame()
```

##### `public`

**Why public:**

- This method is called by UI Button components
- Unity's button OnClick() system needs public methods
- Buttons can ONLY call public methods

**How buttons work in Unity:**

1. Create Button in scene
2. Select the Button
3. In Inspector, find OnClick() section
4. Drag the GameObject with this script
5. Select: MainMenuManager > PlayGame()

**What would happen if private:**

```csharp
private void PlayGame()  // Button can't see this!
```

- Method wouldn't appear in button's dropdown
- UI would have no way to trigger it

##### `void`

**What it means:**

- Method doesn't return any value
- It DOES something (navigate to scene) but doesn't report back

##### `PlayGame`

**Naming:**

- Verb-based name (Play = action)
- Describes what happens when called
- Short and clear for the button dropdown

##### `()`

**Empty parameters:**

- This method takes no input
- UI buttons calling OnClick() don't pass parameters by default

---

#### Line 25: `PlayButtonSound();`

```csharp
PlayButtonSound();
```

**What it does:**

- Calls our private helper method
- Plays a click sound for feedback

**Why call this first:**

- Audio feedback should be immediate
- Player hears the click BEFORE the scene loads
- Loading might take a moment

**What would happen if removed:**

- Button would work silently
- Poor user experience (no feedback)

---

#### Line 26: `Debug.Log("Loading Village scene...");`

```csharp
Debug.Log("Loading Village scene...");
```

**What it does:**

- Prints a message to the Unity Console
- Useful for debugging and development

**What is Debug.Log:**

- Unity's primary debugging tool
- Messages appear in Console window (Window > General > Console)

**Where you see this:**

```
Console:
[12:34:56] Loading Village scene...
```

**Production considerations:**

- Debug.Log calls have performance cost
- In final builds, consider removing or using conditional compilation:

```csharp
#if UNITY_EDITOR
Debug.Log("Loading Village scene...");
#endif
```

---

#### Line 27: `SceneManager.LoadScene("Village");`

```csharp
SceneManager.LoadScene("Village");
```

**This is the KEY LINE - it changes the entire game scene!**

##### `SceneManager`

**What it is:**

- Static class for managing scenes
- Part of `UnityEngine.SceneManagement` namespace
- You don't create an instance; use it directly

##### `.LoadScene()`

**What it does:**

- Unloads the current scene (MainMenu)
- Destroys all GameObjects in current scene (except DontDestroyOnLoad objects)
- Loads the specified scene
- Creates all GameObjects from that scene

**The loading process:**

1. Unity finds the scene file "Village.unity"
2. Current scene starts unloading
3. All GameObjects in MainMenu scene are destroyed
4. Village scene is read from disk
5. Village scene's GameObjects are created
6. Awake(), Start() are called on new objects

##### `"Village"`

**What this is:**

- The NAME of the scene to load
- Must match EXACTLY (case-sensitive)

**Where scene names come from:**

1. The scene file name (Village.unity)
2. OR the path (Scenes/Village)

**IMPORTANT: Build Settings Requirement**

- The scene MUST be in Build Settings!
- File > Build Settings > Add scene to list
- Without this, LoadScene will fail

**Common error:**

```
Scene 'Village' couldn't be loaded because it has not been added to the build settings
```

**How to fix:**

1. Open Build Settings (Ctrl+Shift+B)
2. Drag Village.unity into "Scenes In Build"
3. Save

---

### Lines 33-38: OpenMissions Method

```csharp
/// <summary>
/// Navigate to the Mission Select scene
/// </summary>
public void OpenMissions()
{
    PlayButtonSound();
    Debug.Log("Loading MissionSelect scene...");
    SceneManager.LoadScene("MissionSelect");
}
```

**Structure identical to PlayGame():**

- XML documentation
- Public for button access
- Plays click sound
- Logs for debugging
- Loads a different scene ("MissionSelect")

**The MissionSelect scene:**

- Shows available mini-games
- Player chooses which game to play
- Has back button to return to MainMenu

---

### Lines 40-45: LoadMiniGameEcoSwipe Method

```csharp
public void LoadMiniGameEcoSwipe()
{
    PlayButtonSound();
    Debug.Log("Loading EcoSwipe scene...");
    SceneManager.LoadScene("MiniGame_EcoSwipe");
}
```

**Purpose:**

- Direct link to EcoSwipe mini-game
- Bypasses mission select screen

**Scene name: "MiniGame_EcoSwipe"**

- Note the underscore `_` in the name
- Scene names can have underscores, spaces, etc.
- Must match file name exactly

---

### Lines 47-52: LoadMaterialSortingGame Method

```csharp
public void LoadMaterialSortingGame()
{
    PlayButtonSound();
    Debug.Log("Loading Material Sorting game...");
    SceneManager.LoadScene("ColorLearningGame");
}
```

**Note the scene name mismatch:**

- Method is called "LoadMaterialSortingGame"
- But loads scene "ColorLearningGame"
- This might be a legacy name or renamed scene

---

### Lines 57-62: OpenSettings Method

```csharp
/// <summary>
/// Open settings panel (placeholder for future implementation)
/// </summary>
public void OpenSettings()
{
    PlayButtonSound();
    Debug.Log("Settings panel - Coming soon!");
    // TODO: Implement settings panel with volume controls, etc.
}
```

**This is a PLACEHOLDER method:**

- Plays sound (feedback that button works)
- Logs message (for debugging)
- Doesn't actually do anything yet

**TODO comment:**

```csharp
// TODO: Implement settings panel with volume controls, etc.
```

- Standard convention for marking future work
- IDEs can find and list all TODOs
- Reminds developers what needs to be done

**Why have placeholder methods:**

- Button is already in the UI
- Method exists so button can reference it
- Prevents crashes when button is clicked
- Can be implemented later without changing UI

---

### Lines 67-77: QuitGame Method

```csharp
/// <summary>
/// Quit the application (works in build, not in editor)
/// </summary>
public void QuitGame()
{
    PlayButtonSound();
    Debug.Log("Quitting game...");

    #if UNITY_EDITOR
    UnityEditor.EditorApplication.isPlaying = false;
    #else
    Application.Quit();
    #endif
}
```

**This method demonstrates PREPROCESSOR DIRECTIVES!**

---

#### Lines 72-76: Preprocessor Directives

```csharp
#if UNITY_EDITOR
UnityEditor.EditorApplication.isPlaying = false;
#else
Application.Quit();
#endif
```

##### What are Preprocessor Directives:

**Preprocessor directives** are instructions to the C# compiler:

- They start with `#`
- They're processed BEFORE compilation
- They conditionally include/exclude code

**`#if UNITY_EDITOR`**

- "If we're running in the Unity Editor..."
- `UNITY_EDITOR` is a symbol defined by Unity when running in Editor

**`#else`**

- "Otherwise..." (when running in a build)

**`#endif`**

- Marks the end of the conditional block

##### Why Two Different Quit Methods:

**Problem: Application.Quit() doesn't work in Editor**

```csharp
Application.Quit();  // Does NOTHING in Unity Editor!
```

- In Editor, you're not running a standalone application
- You're running the Editor itself
- Quitting would require closing the Editor

**Solution: Different code for Editor vs Build**

**In Unity Editor:**

```csharp
UnityEditor.EditorApplication.isPlaying = false;
```

- Sets the "Play" mode to false
- Stops the game simulation
- Same as clicking the Play button to stop

**In Built Game:**

```csharp
Application.Quit();
```

- Closes the application window
- Exits the process
- Works on Windows, Mac, Linux, mobile, etc.

##### What the Compiler Sees:

**When compiling for Editor:**

```csharp
public void QuitGame()
{
    PlayButtonSound();
    Debug.Log("Quitting game...");
    UnityEditor.EditorApplication.isPlaying = false;
    // Application.Quit(); is completely removed!
}
```

**When compiling for Build:**

```csharp
public void QuitGame()
{
    PlayButtonSound();
    Debug.Log("Quitting game...");
    Application.Quit();
    // UnityEditor code is completely removed!
}
```

##### Common Preprocessor Symbols:

| Symbol             | True When               |
| ------------------ | ----------------------- |
| `UNITY_EDITOR`     | Running in Unity Editor |
| `UNITY_STANDALONE` | Building for desktop    |
| `UNITY_ANDROID`    | Building for Android    |
| `UNITY_IOS`        | Building for iOS        |
| `UNITY_WEBGL`      | Building for WebGL      |
| `DEBUG`            | Debug build             |

---

### Lines 82-98: PlayButtonSound Method

```csharp
/// <summary>
/// Play button click sound through AudioManager
/// </summary>
private void PlayButtonSound()
{
    if (AudioManager.Instance != null)
    {
        //buttonClickSound = "Assets/Audio/UI/ui_click.wav";
        // Try to use the button click sound from this script first
        if (buttonClickSound != null)
        {
            AudioManager.Instance.PlaySFX(buttonClickSound);
        }
        // Otherwise use the AudioManager's default click sound
        else
        {
            AudioManager.Instance.PlayClick();
        }
    }
}
```

---

#### Line 82: Method Signature

```csharp
private void PlayButtonSound()
```

##### `private`

**Why private:**

- This is a HELPER method (internal use only)
- Not meant to be called by UI buttons
- Not meant to be called by other scripts
- Only this script uses it internally

**Encapsulation:**

- Hides implementation details
- Other scripts only know about public methods
- Can change private methods without breaking other code

---

#### Lines 84-97: Method Body

```csharp
if (AudioManager.Instance != null)
{
    if (buttonClickSound != null)
    {
        AudioManager.Instance.PlaySFX(buttonClickSound);
    }
    else
    {
        AudioManager.Instance.PlayClick();
    }
}
```

##### Outer Check: `if (AudioManager.Instance != null)`

**Defensive null check:**

- Ensures AudioManager exists before using it
- Prevents NullReferenceException

##### Inner Logic:

**If buttonClickSound is assigned:**

```csharp
AudioManager.Instance.PlaySFX(buttonClickSound);
```

- Uses the custom sound assigned in THIS script's Inspector
- Allows different menus to have different click sounds

**If buttonClickSound is NOT assigned:**

```csharp
AudioManager.Instance.PlayClick();
```

- Falls back to AudioManager's default click sound
- Convenience method that uses AudioManager's own clickSound field

##### Why This Two-Tier Approach:

1. **Flexibility**: Each menu can have custom sounds
2. **Fallback**: Default sound if nothing is assigned
3. **Centralization**: AudioManager handles the actual playing
4. **Safety**: Null checks prevent crashes

---

### Lines 103-107: OnButtonHover Method

```csharp
/// <summary>
/// Play hover sound for button hover effects (optional)
/// </summary>
public void OnButtonHover()
{
    // Optional: Add hover sound effect
    // AudioManager.Instance?.PlaySFX(hoverSound, 0.3f);
}
```

**This is a PLACEHOLDER for future feature:**

- Method exists but does nothing
- Can be connected to button hover events
- Ready for implementation when needed

**How button hover works:**

1. Add EventTrigger component to button
2. Add PointerEnter event
3. Call OnButtonHover() when mouse enters button

**The commented code shows intended implementation:**

```csharp
AudioManager.Instance?.PlaySFX(hoverSound, 0.3f);
```

- `?.` is null-conditional (safe if Instance is null)
- `hoverSound` would need to be a field
- `0.3f` is a quieter volume for subtle feedback

---

## How Everything Works Together

### Scene Setup Requirements

For this script to work, the scene must have:

1. **GameObject with MainMenuManager attached**

   - Usually empty GameObject named "MenuManager"
   - MainMenuManager component added
   - Audio clips assigned in Inspector

2. **AudioManager (from another scene)**

   - Must exist (usually from first scene load)
   - Must be DontDestroyOnLoad
   - Must have Instance set up

3. **UI Buttons**

   - Canvas with buttons
   - Each button's OnClick() connected to methods

4. **Scenes in Build Settings**
   - Village, MissionSelect, etc. must be in build list

### Step-by-Step Flow

```
1. GAME STARTS
   ↓
2. MainMenu scene loads
   ↓
3. MainMenuManager.Start() runs
   ↓
4. Checks: backgroundMusic assigned? AudioManager exists?
   ↓
5. If yes: Plays background music at 50% volume
   ↓
6. PLAYER CLICKS "PLAY" BUTTON
   ↓
7. Button.OnClick() triggers
   ↓
8. PlayGame() is called
   ↓
9. PlayButtonSound() plays click sound
   ↓
10. Debug.Log prints to console
    ↓
11. SceneManager.LoadScene("Village") starts
    ↓
12. MainMenu scene unloads (all GameObjects destroyed)
    ↓
13. Village scene loads
    ↓
14. New scene's scripts run
```

### Button Connection Diagram

```
[Play Button] ─────OnClick()────→ PlayGame()
[Missions Button] ─OnClick()────→ OpenMissions()
[Settings Button] ─OnClick()────→ OpenSettings()
[Quit Button] ────OnClick()────→ QuitGame()
```

---

## Common Mistakes & Notes

### Mistake 1: Scene Not in Build Settings

**Error:**

```
Scene 'Village' couldn't be loaded because it has not been added to the build settings
```

**Fix:**

1. File > Build Settings
2. Drag scene into list
3. Check scene index matches

---

### Mistake 2: Scene Name Typo

```csharp
SceneManager.LoadScene("village");  // Wrong! Case matters!
SceneManager.LoadScene("Village");  // Correct
```

**Scene names are case-sensitive!**

---

### Mistake 3: Method Not Public

```csharp
private void PlayGame()  // Button can't find this!
```

**Button OnClick() can only see public methods**

---

### Mistake 4: AudioManager Doesn't Exist

**Problem:** AudioManager singleton isn't set up

**Symptoms:**

- No music plays
- No click sounds
- (But no crash because of null checks)

**Fix:**

- Ensure AudioManager exists in first loaded scene
- Has DontDestroyOnLoad

---

### Mistake 5: No AudioClip Assigned

**Problem:** backgroundMusic is null in Inspector

**Symptoms:**

- No music plays
- No error (null check handles it)

**Fix:**

- Drag audio file to Inspector slot

---

## Tips for Extension

### Adding a New Menu Button

1. Create UI Button in scene
2. Add public method to MainMenuManager:

```csharp
public void OpenCredits()
{
    PlayButtonSound();
    Debug.Log("Opening credits...");
    SceneManager.LoadScene("Credits");
}
```

3. Connect button's OnClick() to method
4. Add Credits scene to build settings

### Adding Settings Panel

```csharp
public GameObject settingsPanel;

public void OpenSettings()
{
    PlayButtonSound();
    if (settingsPanel != null)
    {
        settingsPanel.SetActive(true);
    }
}

public void CloseSettings()
{
    PlayButtonSound();
    if (settingsPanel != null)
    {
        settingsPanel.SetActive(false);
    }
}
```

### Adding Scene Transition Effects

```csharp
public void PlayGame()
{
    PlayButtonSound();
    StartCoroutine(LoadSceneWithFade("Village"));
}

private IEnumerator LoadSceneWithFade("Village")
{
    // Fade out
    yield return FadeToBlack();

    // Load scene
    SceneManager.LoadScene(sceneName);
}
```

---

## Summary Table

| Method                    | Access  | Purpose                | Called By          |
| ------------------------- | ------- | ---------------------- | ------------------ |
| Start()                   | private | Initialize, play music | Unity (automatic)  |
| PlayGame()                | public  | Go to Village          | Play Button        |
| OpenMissions()            | public  | Go to MissionSelect    | Missions Button    |
| LoadMiniGameEcoSwipe()    | public  | Go to EcoSwipe         | Direct link button |
| LoadMaterialSortingGame() | public  | Go to ColorLearning    | Direct link button |
| OpenSettings()            | public  | Placeholder            | Settings Button    |
| QuitGame()                | public  | Exit application       | Quit Button        |
| PlayButtonSound()         | private | Audio feedback         | Internal helper    |
| OnButtonHover()           | public  | Placeholder            | EventTrigger       |

---

This script is simple but demonstrates key Unity concepts:

- ✅ MonoBehaviour lifecycle (Start)
- ✅ Scene management (LoadScene)
- ✅ UI button connections (public methods)
- ✅ Audio feedback (AudioManager)
- ✅ Null safety (defensive checks)
- ✅ Preprocessor directives (#if UNITY_EDITOR)
- ✅ Documentation comments (///)
