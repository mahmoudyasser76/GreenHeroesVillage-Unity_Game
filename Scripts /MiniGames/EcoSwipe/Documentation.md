# 📚 EcoSwipe Mini-Game - Complete Line-by-Line Documentation

**Folder Path:** `Assets/Scripts/MiniGames/EcoSwipe`

This document provides an **extremely detailed, line-by-line explanation** of ALL scripts in the EcoSwipe mini-game folder.

---

# Table of Contents

1. [Overview](#overview)
2. [Script 1: EcoCardData.cs (ScriptableObject)](#script-1-ecocarddatacs)
3. [Script 2: EcoSwipeCard.cs (Card UI & Swipe)](#script-2-ecoswipecardcs)
4. [Script 3: EcoSwipeGameManager.cs (Game Controller)](#script-3-ecoswipegamemanagercs)
5. [How Everything Works Together](#how-everything-works-together)

---

# Overview

The **Eco Swipe** mini-game teaches water conservation and clean energy concepts:

- Cards show eco-related actions (e.g., "Short Shower", "Solar Panel")
- Player swipes **RIGHT** for positive/eco-friendly actions
- Player swipes **LEFT** for negative/wasteful actions
- Labels change based on card type (Water vs Energy)
- **+10 GP** for correct swipes, **-2 GP** for wrong swipes

**Scripts in this folder:**

| Script                   | Type             | Purpose                          |
| ------------------------ | ---------------- | -------------------------------- |
| `EcoCardData.cs`         | ScriptableObject | Data container for each card     |
| `EcoSwipeCard.cs`        | MonoBehaviour    | Card display and swipe detection |
| `EcoSwipeGameManager.cs` | MonoBehaviour    | Main game controller             |

---

# Script 1: EcoCardData.cs

**File Path:** `Assets/Scripts/MiniGames/EcoSwipe/EcoCardData.cs`

## Script Purpose

`EcoCardData` is a **ScriptableObject** that defines the properties of each eco card. It stores:

- The card's title (what action it represents)
- The card type (Water or Energy)
- Whether the action is positive (eco-friendly) or negative
- The card's background color

---

## Complete Script Code

```csharp
using UnityEngine;

[CreateAssetMenu(fileName = "Card_New", menuName = "GreenHeroes/EcoCardData")]
public class EcoCardData : ScriptableObject
{
    [Header("Card Info")]
    [Tooltip("The title shown on the card (e.g., 'Solar Panel', 'Short Shower')")]
    public string cardTitle;

    [Tooltip("Water card or Energy card?")]
    public CardType cardType;

    [Tooltip("Is this an eco-friendly (positive) action?")]
    public bool isPositive;

    [Header("Appearance")]
    [Tooltip("Background color for the card")]
    public Color cardColor = Color.white;
}

public enum CardType
{
    Water,   // Shows "Saving Water" / "Wasting Water"
    Energy   // Shows "Clean Energy" / "Dirty Energy"
}
```

---

## Imports / Using Statements

### Line 1: `using UnityEngine;`

```csharp
using UnityEngine;
```

#### What it does:

- Imports Unity's core namespace
- Required for `ScriptableObject`, `Color`, and attributes like `[Header]`

---

## Attribute: CreateAssetMenu

### Line 7: `[CreateAssetMenu(...)]`

```csharp
[CreateAssetMenu(fileName = "Card_New", menuName = "GreenHeroes/EcoCardData")]
```

#### What is an Attribute:

- Metadata attached to a class, method, or field
- Modifies behavior without changing the code logic
- Enclosed in square brackets `[...]`

#### What CreateAssetMenu does:

- Allows creating instances of this ScriptableObject from Unity's menu
- Appears in: **Right-click → Create → GreenHeroes → EcoCardData**

#### Parameters:

| Parameter  | Value                       | Purpose                     |
| ---------- | --------------------------- | --------------------------- |
| `fileName` | `"Card_New"`                | Default name for new assets |
| `menuName` | `"GreenHeroes/EcoCardData"` | Path in the Create menu     |

#### Without this attribute:

- Cannot create assets from the Editor
- Would need to create via code (inconvenient)

---

## Class Declaration

### Line 8: ScriptableObject Class

```csharp
public class EcoCardData : ScriptableObject
```

#### What is ScriptableObject:

- A Unity class for storing data as **assets** in the project
- NOT attached to GameObjects (unlike MonoBehaviour)
- Exists as files in the Assets folder
- Shared across scenes (no duplication)

#### ScriptableObject vs MonoBehaviour:

| Feature       | ScriptableObject     | MonoBehaviour             |
| ------------- | -------------------- | ------------------------- |
| Attached to   | Project (asset file) | GameObject in scene       |
| Lifecycle     | Project lifetime     | Scene lifetime            |
| Multiple uses | Shared reference     | Each instance is separate |
| Methods       | No Update/Start      | Has lifecycle methods     |

#### Why use ScriptableObject here:

- Cards are **data**, not behavior
- Same card data used across multiple games
- Easy to create/edit in the Inspector
- Designers can add cards without code

---

## Variables & Fields

### Lines 10-12: Card Title

```csharp
[Header("Card Info")]
[Tooltip("The title shown on the card (e.g., 'Solar Panel', 'Short Shower')")]
public string cardTitle;
```

#### Line 10: `[Header("Card Info")]`

- Creates a labeled section in the Inspector
- Groups related fields visually

#### Line 11: `[Tooltip("...")]`

- Shows help text when hovering over the field in Inspector
- Helps designers understand what to enter

#### Line 12: `public string cardTitle;`

**What it stores:**

- The text displayed on the card
- Examples: "Solar Panel", "Short Shower", "Leaving Lights On"

**Why public:**

- Editable in Inspector
- Accessible from EcoSwipeCard to display

---

### Lines 14-15: Card Type

```csharp
[Tooltip("Water card or Energy card?")]
public CardType cardType;
```

**What it stores:**

- Whether this card is about Water or Energy
- Determines which labels appear (Saving Water/Clean Energy)

**In Inspector:**

- Shows as dropdown with options: "Water", "Energy"

---

### Lines 17-18: Is Positive

```csharp
[Tooltip("Is this an eco-friendly (positive) action?")]
public bool isPositive;
```

**What it stores:**

- `true` = eco-friendly action (swipe right is correct)
- `false` = wasteful action (swipe left is correct)

**Examples:**

| Card Title            | isPositive | Correct Swipe        |
| --------------------- | ---------- | -------------------- |
| "Solar Panel"         | true       | Right (Clean Energy) |
| "Short Shower"        | true       | Right (Saving Water) |
| "Leaving Tap Running" | false      | Left (Wasting Water) |
| "Coal Power Plant"    | false      | Left (Dirty Energy)  |

---

### Lines 21-22: Card Color

```csharp
[Header("Appearance")]
[Tooltip("Background color for the card")]
public Color cardColor = Color.white;
```

**What is Color:**

- Unity's color type (RGBA values)
- `Color.white` = (1, 1, 1, 1) = white

**What it stores:**

- Background color of the card
- Visual distinction between card types

**Default `= Color.white`:**

- If not set, card is white
- Can be changed per card in Inspector

---

## Enum: CardType

### Lines 28-32: CardType Enum

```csharp
public enum CardType
{
    Water,   // Shows "Saving Water" / "Wasting Water"
    Energy   // Shows "Clean Energy" / "Dirty Energy"
}
```

**What it defines:**

- Two possible categories for cards
- `Water` = 0 (internal value)
- `Energy` = 1 (internal value)

**Why separate enum:**

- Clear categorization
- Controls which labels are displayed
- Easy to extend (could add "Recycling", etc.)

---

---

# Script 2: EcoSwipeCard.cs

**File Path:** `Assets/Scripts/MiniGames/EcoSwipe/EcoSwipeCard.cs`

## Script Purpose

`EcoSwipeCard` handles the **visual display and swipe interaction** for a single card. It:

1. Displays the card's title and color
2. Detects drag/swipe gestures
3. Moves the card with the finger/mouse
4. Returns the card to center if not swiped far enough
5. Fires an event when a swipe is completed

---

## Complete Script Code

```csharp
using System;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;
using TMPro;

public class EcoSwipeCard : MonoBehaviour, IBeginDragHandler, IDragHandler, IEndDragHandler
{
    [Header("UI References")]
    public TextMeshProUGUI cardTitleText;
    public Image cardBackground;

    [Header("Swipe Settings")]
    public float swipeThreshold = 100f;
    public float returnSpeed = 10f;

    private EcoCardData currentCard;
    private Vector3 originalPosition;
    private RectTransform rectTransform;
    private bool isDragging = false;
    private Vector3 dragStartPosition;

    public event Action<bool> OnSwipeComplete;

    void Awake() { /* ... */ }
    void Start() { /* ... */ }
    void Update() { /* ... */ }
    // ... more methods
}
```

---

## Imports / Using Statements

### Line 1: `using System;`

```csharp
using System;
```

#### What it does:

- Imports the base .NET System namespace
- Required for `Action<T>` delegate type

#### What it provides:

- `Action<bool>` - used for the `OnSwipeComplete` event

---

### Line 2: `using UnityEngine;`

```csharp
using UnityEngine;
```

#### What it provides:

- `MonoBehaviour`, `Vector3`, `Color`
- `Time.deltaTime` for smooth animations
- `RectTransform` for UI positioning

---

### Line 3: `using UnityEngine.EventSystems;`

```csharp
using UnityEngine.EventSystems;
```

#### What it provides:

- Drag handler interfaces: `IBeginDragHandler`, `IDragHandler`, `IEndDragHandler`
- `PointerEventData` - information about the drag event

---

### Line 4: `using UnityEngine.UI;`

```csharp
using UnityEngine.UI;
```

#### What it provides:

- `Image` component for card background

---

### Line 5: `using TMPro;`

```csharp
using TMPro;
```

#### What it provides:

- `TextMeshProUGUI` for high-quality text

---

## Class Declaration

### Line 11: Class with Interfaces

```csharp
public class EcoSwipeCard : MonoBehaviour, IBeginDragHandler, IDragHandler, IEndDragHandler
```

#### Breaking it down:

**`: MonoBehaviour`:**

- Unity component that can be attached to GameObjects
- Provides lifecycle methods (Awake, Start, Update)

**`, IBeginDragHandler`:**

- Interface requiring: `void OnBeginDrag(PointerEventData eventData)`
- Called when user starts dragging this object

**`, IDragHandler`:**

- Interface requiring: `void OnDrag(PointerEventData eventData)`
- Called every frame while dragging

**`, IEndDragHandler`:**

- Interface requiring: `void OnEndDrag(PointerEventData eventData)`
- Called when user releases the drag

#### Why use interfaces instead of OnMouseDrag:

- Works with Unity UI (Canvas-based)
- Works on both mouse and touch
- More control over the drag lifecycle
- Part of Unity's EventSystem

---

## Variables & Fields

### Lines 13-15: UI References

```csharp
[Header("UI References")]
[Tooltip("Text displaying the card title")]
public TextMeshProUGUI cardTitleText;

[Tooltip("Background image of the card (for color tinting)")]
public Image cardBackground;
```

**`cardTitleText`:**

- The text element showing the action name
- Updated when a new card is shown

**`cardBackground`:**

- The Image component for the card background
- Color is tinted based on EcoCardData.cardColor

---

### Lines 20-25: Swipe Settings

```csharp
[Header("Swipe Settings")]
[Tooltip("How far the card must be dragged to trigger a swipe (in pixels)")]
public float swipeThreshold = 100f;

[Tooltip("How fast the card returns to center when not swiped far enough")]
public float returnSpeed = 10f;
```

**`swipeThreshold = 100f`:**

- Minimum horizontal distance (in pixels) to count as a swipe
- Less than 100 pixels = card snaps back to center
- More than 100 pixels = swipe is registered

**`returnSpeed = 10f`:**

- How fast the card moves back to center
- Higher value = faster return
- Used in `Vector3.Lerp()` calculation

---

### Lines 28-36: Private State

```csharp
private EcoCardData currentCard;
private Vector3 originalPosition;
private RectTransform rectTransform;
private bool isDragging = false;
private Vector3 dragStartPosition;
```

| Variable            | Purpose                         |
| ------------------- | ------------------------------- |
| `currentCard`       | The current card's data         |
| `originalPosition`  | Center position to return to    |
| `rectTransform`     | Reference for UI positioning    |
| `isDragging`        | Whether currently being dragged |
| `dragStartPosition` | Position when drag started      |

---

### Line 39: Event Declaration

```csharp
public event Action<bool> OnSwipeComplete; // true = right, false = left
```

#### What is an Event:

- A way for one script to notify others when something happens
- Publishers fire events, subscribers listen
- Loose coupling between scripts

#### What is `Action<bool>`:

- A delegate type from System
- Represents a method that takes one `bool` parameter and returns `void`
- The bool indicates swipe direction (true = right)

#### How it's used:

```csharp
// In EcoSwipeGameManager (subscriber):
swipeCard.OnSwipeComplete += OnCardSwiped;

// In EcoSwipeCard (publisher):
OnSwipeComplete?.Invoke(swipedRight);  // Fires the event
```

---

## Unity Lifecycle Methods

### Lines 41-44: Awake Method

```csharp
void Awake()
{
    rectTransform = GetComponent<RectTransform>();
}
```

#### When Unity calls Awake():

- First lifecycle method called
- Called once when the object is created
- Called before Start()

#### What it does:

- Gets the RectTransform component for UI positioning

**What is RectTransform:**

- Special Transform for UI elements
- Has `anchoredPosition` instead of `position`
- Works with Canvas coordinate system

---

### Lines 46-50: Start Method

```csharp
void Start()
{
    // Store original position
    originalPosition = rectTransform.anchoredPosition;
}
```

#### What it does:

- Saves the card's starting position (center of screen)
- Used to return card to center after incomplete swipe

---

### Lines 52-63: Update Method

```csharp
void Update()
{
    // Smoothly return to center when not dragging
    if (!isDragging && Vector3.Distance(rectTransform.anchoredPosition, originalPosition) > 0.1f)
    {
        rectTransform.anchoredPosition = Vector3.Lerp(
            rectTransform.anchoredPosition,
            originalPosition,
            Time.deltaTime * returnSpeed
        );
    }
}
```

#### When Unity calls Update():

- Every frame while the object is active
- After Start() has completed

#### Line 55: Condition Check

```csharp
if (!isDragging && Vector3.Distance(rectTransform.anchoredPosition, originalPosition) > 0.1f)
```

**Conditions:**

1. `!isDragging` - NOT currently being dragged
2. `Distance(...) > 0.1f` - Card is not already at center

**Why 0.1f threshold:**

- Prevents tiny movements when essentially at center
- Optimization: stops calculating when close enough

#### Lines 57-61: Smooth Return (Lerp)

```csharp
rectTransform.anchoredPosition = Vector3.Lerp(
    rectTransform.anchoredPosition,  // Current position
    originalPosition,                 // Target position
    Time.deltaTime * returnSpeed      // Interpolation factor
);
```

**What is Lerp:**

- Linear Interpolation
- Smoothly blends between two values
- `Lerp(A, B, t)` where t is 0 to 1:
  - t = 0 → returns A
  - t = 1 → returns B
  - t = 0.5 → returns midpoint

**How the animation works:**

```
Frame 1: pos = Lerp(100, 0, 0.16) ≈ 84
Frame 2: pos = Lerp(84, 0, 0.16) ≈ 70
Frame 3: pos = Lerp(70, 0, 0.16) ≈ 59
...eventually reaches 0
```

**Why multiply by Time.deltaTime:**

- Frame-rate independent animation
- Same speed regardless of FPS

---

## Custom Functions

### Lines 68-89: SetupCard Method

```csharp
public void SetupCard(EcoCardData data)
{
    currentCard = data;

    // Update title
    if (cardTitleText != null)
    {
        cardTitleText.text = data.cardTitle;
    }

    // Update background color
    if (cardBackground != null)
    {
        cardBackground.color = data.cardColor;
    }

    // Reset position
    rectTransform.anchoredPosition = originalPosition;

    // Make sure card is visible
    gameObject.SetActive(true);
}
```

#### What it does:

1. Store the card data
2. Update the title text
3. Update the background color
4. Reset position to center
5. Ensure card is visible

---

### Lines 94-97: HideCard Method

```csharp
public void HideCard()
{
    gameObject.SetActive(false);
}
```

**What it does:**

- Hides the card (game over or between cards)

---

## Drag Handlers

### Lines 101-105: OnBeginDrag

```csharp
public void OnBeginDrag(PointerEventData eventData)
{
    isDragging = true;
    dragStartPosition = rectTransform.anchoredPosition;
}
```

#### When Unity calls this:

- When user starts dragging (mouse down + move, or touch + move)
- Called once at drag start

#### What `PointerEventData` contains:

- `position` - current pointer position
- `delta` - movement since last frame
- `button` - which mouse button (if applicable)

---

### Lines 107-115: OnDrag

```csharp
public void OnDrag(PointerEventData eventData)
{
    if (!isDragging) return;

    // Move card with finger/mouse (only horizontal movement)
    Vector2 newPosition = rectTransform.anchoredPosition;
    newPosition.x += eventData.delta.x;
    rectTransform.anchoredPosition = newPosition;
}
```

#### When Unity calls this:

- Every frame while dragging
- After OnBeginDrag, before OnEndDrag

#### Line 113: `newPosition.x += eventData.delta.x;`

**What is `eventData.delta`:**

- Movement since last frame in pixels
- `delta.x` = horizontal movement
- `delta.y` = vertical movement (not used here)

**Why only horizontal:**

- Swipe mechanic is left/right only
- Vertical movement is ignored

---

### Lines 117-132: OnEndDrag

```csharp
public void OnEndDrag(PointerEventData eventData)
{
    isDragging = false;

    // Calculate how far the card was dragged from center
    float dragDistance = rectTransform.anchoredPosition.x - originalPosition.x;

    // Check if swipe threshold was reached
    if (Mathf.Abs(dragDistance) >= swipeThreshold)
    {
        // Swipe completed
        bool swipedRight = dragDistance > 0;
        OnSwipeComplete?.Invoke(swipedRight);
    }
    // If not far enough, card will return to center via Update()
}
```

#### When Unity calls this:

- When user releases the drag
- Called once at drag end

#### Line 122: Calculate Drag Distance

```csharp
float dragDistance = rectTransform.anchoredPosition.x - originalPosition.x;
```

**What it calculates:**

- How far from center the card is
- Positive = right of center
- Negative = left of center

#### Line 125: Check Threshold

```csharp
if (Mathf.Abs(dragDistance) >= swipeThreshold)
```

**What `Mathf.Abs` does:**

- Returns absolute value (removes negative sign)
- `Abs(-150)` = 150
- `Abs(150)` = 150

**Why use absolute value:**

- Threshold applies to both directions
- 100 pixels right OR 100 pixels left counts

#### Lines 128-129: Fire Event

```csharp
bool swipedRight = dragDistance > 0;
OnSwipeComplete?.Invoke(swipedRight);
```

**What `?.Invoke` does:**

- Null-conditional invoke
- Only fires if there are subscribers
- Prevents NullReferenceException if no one is listening

---

### Lines 137-140: GetCurrentCard

```csharp
public EcoCardData GetCurrentCard()
{
    return currentCard;
}
```

**What it does:**

- Returns the current card's data
- Allows other scripts to check card properties

---

---

# Script 3: EcoSwipeGameManager.cs

**File Path:** `Assets/Scripts/MiniGames/EcoSwipe/EcoSwipeGameManager.cs`

## Script Purpose

`EcoSwipeGameManager` is the **main controller** for the Eco Swipe mini-game. It manages:

1. **Card preparation** - shuffling and selecting cards
2. **Game flow** - showing cards, tracking progress
3. **Scoring** - correct/wrong swipes, GreenPoints
4. **Label updates** - changing labels based on card type
5. **Game over** - displaying results, restart/exit

---

## Complete Script Code

```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
using TMPro;

public class EcoSwipeGameManager : MonoBehaviour
{
    [Header("Card System")]
    public List<EcoCardData> allCards = new List<EcoCardData>();
    public EcoSwipeCard swipeCard;
    public int cardsPerGame = 10;

    [Header("UI - Labels")]
    public TextMeshProUGUI rightLabel;
    public TextMeshProUGUI leftLabel;

    [Header("UI - Score Display")]
    public TextMeshProUGUI scoreText;
    public TextMeshProUGUI cardCountText;
    public TextMeshProUGUI greenPointsText;

    [Header("UI - Game Over")]
    public GameObject gameOverPanel;
    public TextMeshProUGUI finalScoreText;
    public TextMeshProUGUI rewardText;
    public Button restartButton;
    public Button exitButton;

    [Header("Scoring - GreenPoints")]
    public int pointsPerCorrect = 10;
    public int pointsPerWrong = 2;

    // Game state
    private List<EcoCardData> shuffledCards = new List<EcoCardData>();
    private int currentCardIndex = 0;
    private int correctSwipes = 0;
    private int wrongSwipes = 0;
    private int totalCards = 0;
    private int earnedPoints = 0;

    // ... methods
}
```

---

## Imports / Using Statements

### Line 1: `using System.Collections.Generic;`

```csharp
using System.Collections.Generic;
```

#### What it provides:

- `List<T>` - dynamic array type
- Used for `allCards` and `shuffledCards`

---

### Lines 2-5: Unity Imports

```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
using TMPro;
```

Same as previous scripts - core Unity, UI, scene management, and TextMeshPro.

---

## Variables & Fields

### Lines 13-16: Card System

```csharp
[Header("Card System")]
public List<EcoCardData> allCards = new List<EcoCardData>();
public EcoSwipeCard swipeCard;
public int cardsPerGame = 10;
```

**`allCards`:**

- List of ALL available card data assets
- Assigned in Inspector from ScriptableObjects
- Source pool for random selection

**`swipeCard`:**

- Reference to the single card UI element
- Same card object reused for all cards (updates content)

**`cardsPerGame = 10`:**

- How many cards to show per game session
- Designer-tunable difficulty

---

### Lines 18-20: Label References

```csharp
[Header("UI - Labels")]
public TextMeshProUGUI rightLabel;
public TextMeshProUGUI leftLabel;
```

**What these are:**

- Text elements showing swipe options
- Change based on card type:

| Card Type | Right Label    | Left Label      |
| --------- | -------------- | --------------- |
| Water     | "Saving Water" | "Wasting Water" |
| Energy    | "Clean Energy" | "Dirty Energy"  |

---

### Lines 34-38: Scoring Settings

```csharp
[Header("Scoring - GreenPoints")]
[Tooltip("+10 GP for correct answer")]
public int pointsPerCorrect = 10;
[Tooltip("-2 GP for wrong answer")]
public int pointsPerWrong = 2;
```

**Game balance:**

- Correct swipe: +10 GP
- Wrong swipe: -2 GP
- Net positive if player gets more right than wrong

---

### Lines 41-46: Private Game State

```csharp
private List<EcoCardData> shuffledCards = new List<EcoCardData>();
private int currentCardIndex = 0;
private int correctSwipes = 0;
private int wrongSwipes = 0;
private int totalCards = 0;
private int earnedPoints = 0;
```

| Variable           | Purpose                                 |
| ------------------ | --------------------------------------- |
| `shuffledCards`    | Cards to show this game (randomized)    |
| `currentCardIndex` | Which card we're on (0 to totalCards-1) |
| `correctSwipes`    | Count of correct answers                |
| `wrongSwipes`      | Count of wrong answers                  |
| `totalCards`       | How many cards this session             |
| `earnedPoints`     | Running total of GP earned/lost         |

---

## Unity Lifecycle Methods

### Lines 48-66: Start Method

```csharp
void Start()
{
    if (restartButton != null)
    {
        restartButton.onClick.RemoveAllListeners();
        restartButton.onClick.AddListener(RestartGame);
    }

    if (exitButton != null)
    {
        exitButton.onClick.RemoveAllListeners();
        exitButton.onClick.AddListener(ExitToVillage);
    }

    if (swipeCard != null)
        swipeCard.OnSwipeComplete += OnCardSwiped;

    StartGame();
}
```

#### Lines 50-60: Button Setup

**Same pattern as other managers:**

- Remove any existing listeners
- Add the correct method as listener

#### Lines 62-63: Subscribe to Swipe Event

```csharp
if (swipeCard != null)
    swipeCard.OnSwipeComplete += OnCardSwiped;
```

**What `+=` does with events:**

- Adds a subscriber (listener) to the event
- When `OnSwipeComplete` fires, `OnCardSwiped` is called

**Why in Start not Awake:**

- swipeCard must be initialized first
- Start runs after all Awake methods

---

### Lines 68-72: OnDestroy Method

```csharp
void OnDestroy()
{
    if (swipeCard != null)
        swipeCard.OnSwipeComplete -= OnCardSwiped;
}
```

#### When Unity calls OnDestroy():

- When the GameObject is destroyed
- When scene is unloaded
- Important for cleanup

#### What `-=` does:

- Removes the subscriber from the event
- **CRITICAL:** Prevents memory leaks and errors

**What happens without unsubscribing:**

- Event still holds reference to this object
- Can cause "missing reference" errors
- Potential memory leak

---

## Custom Functions

### Lines 74-88: StartGame Method

```csharp
void StartGame()
{
    correctSwipes = 0;
    wrongSwipes = 0;
    currentCardIndex = 0;
    earnedPoints = 0;

    if (gameOverPanel != null)
        gameOverPanel.SetActive(false);

    PrepareCards();
    ShowNextCard();

    Debug.Log("EcoSwipe Game Started!");
}
```

**What it does:**

1. Reset all counters to zero
2. Hide game over panel
3. Prepare the card deck (shuffle)
4. Show the first card

---

### Lines 90-120: PrepareCards Method

```csharp
void PrepareCards()
{
    shuffledCards.Clear();

    if (allCards.Count == 0)
    {
        Debug.LogError("EcoSwipeGameManager: No cards assigned!");
        return;
    }

    while (shuffledCards.Count < cardsPerGame)
    {
        foreach (var card in allCards)
        {
            shuffledCards.Add(card);
            if (shuffledCards.Count >= cardsPerGame)
                break;
        }
    }

    // Fisher-Yates shuffle
    for (int i = shuffledCards.Count - 1; i > 0; i--)
    {
        int randomIndex = Random.Range(0, i + 1);
        var temp = shuffledCards[i];
        shuffledCards[i] = shuffledCards[randomIndex];
        shuffledCards[randomIndex] = temp;
    }

    totalCards = Mathf.Min(shuffledCards.Count, cardsPerGame);
}
```

#### Lines 92-98: Clear and Validate

```csharp
shuffledCards.Clear();

if (allCards.Count == 0)
{
    Debug.LogError("EcoSwipeGameManager: No cards assigned!");
    return;
}
```

- Clear any previous cards
- Validate that cards exist

#### Lines 100-108: Fill Deck

```csharp
while (shuffledCards.Count < cardsPerGame)
{
    foreach (var card in allCards)
    {
        shuffledCards.Add(card);
        if (shuffledCards.Count >= cardsPerGame)
            break;
    }
}
```

**What this does:**

- Fills the deck until we have enough cards
- If allCards has 5 cards and cardsPerGame is 10, each card appears twice

#### Lines 111-117: Fisher-Yates Shuffle

```csharp
for (int i = shuffledCards.Count - 1; i > 0; i--)
{
    int randomIndex = Random.Range(0, i + 1);
    var temp = shuffledCards[i];
    shuffledCards[i] = shuffledCards[randomIndex];
    shuffledCards[randomIndex] = temp;
}
```

**What is Fisher-Yates shuffle:**

- The standard algorithm for random shuffling
- Each element has equal probability of any position
- O(n) efficiency

**How it works:**

```
Start: [A, B, C, D, E]
i=4: swap E with random(0-4), e.g., [A, E, C, D, B]
i=3: swap D with random(0-3), e.g., [D, E, C, A, B]
i=2: swap C with random(0-2), e.g., [D, C, E, A, B]
i=1: swap E with random(0-1), e.g., [C, D, E, A, B]
Done!
```

---

### Lines 122-139: ShowNextCard Method

```csharp
void ShowNextCard()
{
    if (currentCardIndex >= totalCards)
    {
        EndGame();
        return;
    }

    EcoCardData card = shuffledCards[currentCardIndex];

    if (swipeCard != null)
        swipeCard.SetupCard(card);

    UpdateLabels(card.cardType);
    UpdateUI();

    Debug.Log($"Card {currentCardIndex + 1}/{totalCards}: {card.cardTitle}");
}
```

**What it does:**

1. Check if all cards shown → end game
2. Get the next card data
3. Tell swipeCard to display it
4. Update labels based on card type
5. Update score UI

---

### Lines 141-153: UpdateLabels Method

```csharp
void UpdateLabels(CardType type)
{
    if (type == CardType.Water)
    {
        if (rightLabel != null) rightLabel.text = "Saving Water";
        if (leftLabel != null) leftLabel.text = "Wasting Water";
    }
    else
    {
        if (rightLabel != null) rightLabel.text = "Clean Energy";
        if (leftLabel != null) leftLabel.text = "Dirty Energy";
    }
}
```

**Dynamic labels:**

- Labels change based on current card type
- Helps player understand what each swipe means

---

### Lines 155-191: OnCardSwiped Method

```csharp
void OnCardSwiped(bool swipedRight)
{
    if (currentCardIndex >= totalCards) return;

    EcoCardData card = shuffledCards[currentCardIndex];
    bool isCorrect = (swipedRight == card.isPositive);

    if (isCorrect)
    {
        correctSwipes++;
        earnedPoints += pointsPerCorrect;

        if (GreenPointsManager.Instance != null)
            GreenPointsManager.Instance.AddPoints(pointsPerCorrect);

        AudioManager.Instance?.PlayClick();
        Debug.Log($"Correct! +{pointsPerCorrect} GP for {card.cardTitle}");
    }
    else
    {
        wrongSwipes++;
        earnedPoints -= pointsPerWrong;

        if (GreenPointsManager.Instance != null)
            GreenPointsManager.Instance.DeductPoints(pointsPerWrong);

        AudioManager.Instance?.PlayCancel();
        Debug.Log($"Wrong! -{pointsPerWrong} GP for {card.cardTitle}");
    }

    currentCardIndex++;
    UpdateUI();

    Invoke(nameof(ShowNextCard), 0.3f);
}
```

#### Line 160: Determine Correctness

```csharp
bool isCorrect = (swipedRight == card.isPositive);
```

**The game logic:**

- If card is positive (eco-friendly) → swipe RIGHT is correct
- If card is negative (wasteful) → swipe LEFT is correct

| Card        | isPositive | Swiped Right | Result    |
| ----------- | ---------- | ------------ | --------- |
| Solar Panel | true       | true         | ✓ Correct |
| Solar Panel | true       | false        | ✗ Wrong   |
| Coal Plant  | false      | false        | ✓ Correct |
| Coal Plant  | false      | true         | ✗ Wrong   |

#### Line 190: Delayed Next Card

```csharp
Invoke(nameof(ShowNextCard), 0.3f);
```

**Why delay:**

- Gives visual feedback time
- Smooth transition between cards
- 0.3 seconds = 300 milliseconds

---

### Lines 205-225: EndGame Method

```csharp
void EndGame()
{
    Debug.Log($"Game Over! Correct: {correctSwipes}/{totalCards}, Earned: {earnedPoints} GP");

    if (swipeCard != null)
        swipeCard.HideCard();

    if (gameOverPanel != null)
        gameOverPanel.SetActive(true);

    if (finalScoreText != null)
        finalScoreText.text = $"You got {correctSwipes}/{totalCards} correct!";

    if (rewardText != null)
    {
        string sign = earnedPoints >= 0 ? "+" : "";
        rewardText.text = $"GreenPoints: {sign}{earnedPoints}";
    }

    AudioManager.Instance?.PlayPlace();
}
```

**What it does:**

1. Hide the swipe card
2. Show game over panel
3. Display final score and GP earned

---

### Lines 227-235: Restart and Exit

```csharp
public void RestartGame()
{
    SceneManager.LoadScene(SceneManager.GetActiveScene().name);
}

public void ExitToVillage()
{
    SceneManager.LoadScene("Village");
}
```

Same pattern as other mini-games.

---

# How Everything Works Together

## Complete Game Flow

```
1. SCENE LOADS
   └── EcoSwipeGameManager.Start()
       ├── Setup button listeners
       ├── Subscribe to OnSwipeComplete event
       └── StartGame()
           ├── Reset counters
           ├── PrepareCards() → shuffle deck
           └── ShowNextCard() → display first card

2. FOR EACH CARD:
   ├── ShowNextCard()
   │   ├── Get card data from shuffledCards
   │   ├── swipeCard.SetupCard(data)
   │   │   ├── Update title text
   │   │   ├── Update background color
   │   │   └── Reset position
   │   ├── UpdateLabels() → Water/Energy specific
   │   └── UpdateUI() → score display

3. PLAYER DRAGS CARD:
   ├── EcoSwipeCard.OnBeginDrag()
   │   └── isDragging = true
   ├── EcoSwipeCard.OnDrag() (every frame)
   │   └── Move card horizontally with finger
   └── EcoSwipeCard.OnEndDrag()
       ├── Check if past swipeThreshold
       ├── If YES: OnSwipeComplete?.Invoke(direction)
       └── If NO: Update() will return card to center

4. ON SWIPE COMPLETE:
   └── EcoSwipeGameManager.OnCardSwiped(swipedRight)
       ├── Determine if correct: swipedRight == card.isPositive
       ├── If correct: +10 GP
       ├── If wrong: -2 GP
       ├── Increment counters
       ├── UpdateUI()
       └── Invoke(ShowNextCard, 0.3f)

5. AFTER ALL CARDS:
   └── EndGame()
       ├── Hide card
       ├── Show game over panel
       └── Display results
```

---

# Common Mistakes & Notes

## Mistake 1: No Cards Assigned

**Problem:** `allCards` list is empty
**Result:** Error message, no cards shown
**Fix:** Create EcoCardData assets and assign to list

## Mistake 2: Missing Event Subscription

**Problem:** Forgot `swipeCard.OnSwipeComplete += OnCardSwiped`
**Result:** Swipes detected but nothing happens
**Fix:** Subscribe in Start()

## Mistake 3: No Unsubscribe in OnDestroy

**Problem:** Missing `OnSwipeComplete -=`
**Result:** Memory leaks, potential errors on restart
**Fix:** Always unsubscribe in OnDestroy()

## Mistake 4: Threshold Too High/Low

**Problem:** swipeThreshold set wrong
**Result:** Too easy (low) or impossible (high)
**Fix:** Test and tune - 100 pixels is reasonable

## Mistake 5: Labels Not Assigned

**Problem:** rightLabel/leftLabel null
**Result:** Labels don't update (no crash due to null checks)
**Fix:** Assign in Inspector

---

# Summary Table

| Script                   | Responsibility                                   |
| ------------------------ | ------------------------------------------------ |
| `EcoCardData.cs`         | Data definition for each card (ScriptableObject) |
| `EcoSwipeCard.cs`        | Card display & swipe detection (UI component)    |
| `EcoSwipeGameManager.cs` | Game control, scoring, flow (Manager)            |

| Communication Flow                                            |
| ------------------------------------------------------------- |
| GameManager creates shuffled deck from EcoCardData assets     |
| GameManager tells EcoSwipeCard to display each card           |
| EcoSwipeCard fires OnSwipeComplete event when swiped          |
| GameManager receives event, calculates score, shows next card |
| GameManager updates GreenPointsManager and AudioManager       |
