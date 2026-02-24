# Dancefloor-Duelist-Portfolio
<img src="https://github.com/VieroFamico/VieroFamico/blob/main/assets/DancefloorDuelists.gif"/>

##  System Design
Other than individual scripts, the game relies on several interconnected systems to handle several mechanics. Below is an overview of how the core mechanics are engineered.

#### 1. Rhythm-Synced Execution Pipeline
**Problem:** In a rhythm game, player input happens asynchronously (at any frame), but game logic (movement, attacks) must resolve synchronously (exactly on the beat). <br>
**Solution:** A decoupled pipeline using the **Observer** and **Command** patterns.
*   **The Heartbeat:** A central `RhythmManager` constantly polls the FMOD audio timeline. Instead of relying on Unity's `Update()`, it calculates exact DSP timestamps to fire `OnBeatTriggered` events via a centralized `GameEvents` Singleton.
*   **Action Queuing:** When a player inputs a move, the logic evaluates the timing quality (Perfect/Great/Good) and encapsulates the intended action into a `MoveCommand`. The action is queued rather than executed immediately.
*   **Synchronous Resolution:** Once the global beat event fires, all active entities (Player, Enemies, Hazards) dequeue and execute their logic simultaneously. This guarantees that visual execution happens perfectly on the beat, maintaining absolute synchronization regardless of when the input occurred within the leniency window.

#### 2. Input Chord & Leniency System
**Problem:** Players rarely press multiple keys on the exact same frame. Requiring perfect simultaneous inputs for mechanics like Dashing (Left+Right), Parrying (Up+Down), or Diagonal movement results in dropped inputs and poor "game feel."<br>
**Solution:** A custom Input Chord Window with dynamic resolution.
*   **Micro-Buffering:** When a directional key is pressed, the system opens a 50ms "chord window" coroutine. 
*   **Dynamic Resolution:** If a second, compatible key is pressed within this 50ms window (e.g., Up, then Left), the system immediately registers a compound action (Diagonal Move) and cancels the timer. If the timer expires—or if the original key is released before expiration—it resolves as a single cardinal movement.
*   **State Priming:** For complex actions like the Parry, the chord window doesn't queue a movement command. Instead, it immediately activates an `IsParryingThisBeat` state flag. This ensures the player's defensive state is active for the current beat's collision resolution while preserving the fluidity of movement inputs.

#### 3. Data-Driven Hazard & Spawning System
**Problem:** Hardcoding enemy attack patterns and bullet behaviors creates massive, unmaintainable scripts and slows down level design iteration. <br>
**Solution:** A highly modular, data-driven approach using **Scriptable Objects** and **Object Pooling**.

*   **Behavior Definition:** Hazards (Bullets, Lasers) have no hardcoded movement. Instead, they read from `HazardBehaviorSO` Scriptable Objects, which define lifetime, looping rules, and a list of `Vector2Int` step sequences.
*   **Attack Scheduling:** Enemies utilize an `EnemyController` that holds a list of `ScheduledAttack` structs. On specific global beats, it passes a `HazardSpawningPatternSO` to the `PatternExecutor`.
*   **Concurrent Execution & Pooling:** The `PatternExecutor` can run multiple spawning sequences simultaneously. When a beat dictates a spawn, it requests a hazard from the `ObjectPooler` Singleton, initializing the reused GameObject with the specific `HazardBehaviorSO` and starting states (e.g., setting a bullet to `Parryable`). This allows designers to create complex "bullet hell" patterns entirely in the Unity Inspector without writing new code, while maintaining strict memory efficiency.

#### 4. Custom Grid Occupancy & Collision Resolution
**Problem:** Standard Unity Physics (Rigidbodies/Colliders) resolve continuously over the physics step, which can lead to floating-point inaccuracies, wall-clipping, and unpredictable state changes in a strict grid-based game. <br>
**Solution:** A custom, post-movement spatial tracking system managed by the `GridManager`.

*   **IGridOccupant Interface:** Every entity on the board (Player, Enemy, Hazards) implements `IGridOccupant`. They are responsible for calculating which `Vector2Int` grid cells they currently occupy based on their origin, size, and rotation.
*   **Execution Order Management:** When `OnBeatTriggered` fires, entities update their internal grid coordinates instantly (and begin their visual DOTween animations). However, collisions are *not* checked immediately.
*   **End-of-Frame Resolution:** To prevent race conditions (e.g., checking collision before an enemy has moved out of a space), a `GameplayBeatController` waits for the `WaitForEndOfFrame` coroutine. Once all entities have settled into their new logical coordinates, it commands the `GridManager` to map all occupants to a spatial dictionary.
*   **State-Based Interaction:** The `GridManager` then checks for overlapping entities in the dictionary. Instead of physical collisions, it resolves interactions based on logical states. For example, if a Player and a Bullet share a cell, it checks the player's `IsParryingThisBeat` flag against the bullet's `CurrentBulletState` to determine if the player takes damage or successfully triggers a Parry event.

#### 5. MVP Architecture for UI (Beat Indicators)
**Problem:** Tying UI visual logic directly into core game loops creates rigid code that is difficult to update or animate smoothly.<br>
**Solution:** Implementing the **Model-View-Presenter (MVP)** pattern for the rhythmic UI.
*   **Separation of Concerns:** The `BeatIndicatorPresenter` acts as the brain. It listens to the `RhythmManager` for upcoming beats and calculates exact DOTween durations based on look-ahead math (e.g., spawning an indicator exactly 3 beats early). 
*   **Dumb Views:** It instantiates `BeatIndicatorView` prefabs, which handle absolutely no game logic. They simply receive a start position, target position, and duration from the Presenter, and play their visual tween. Once the beat actually strikes, the Presenter issues a kill command, ensuring the UI remains perfectly synced with the FMOD audio backend without ever interfering with gameplay code.


## Scripts
The Code Design Documentation for the Dancefloor Duelist 
<br>**The Manager/Controller Script**:
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `GridManager.cs` | Responsible for managing everything about the grid which every entity will be on |
| `RhythmManager.cs` | Responsible for managing the rhythm's beat, and the timing of player's input, which determine every entity's movement |
| `GameplayBeatController.cs` | Responsible for making sure the collision of each entity occurs after movement is done |
| `GameEvents.cs` | Responsible for storing all the events of the game |
| `ScoreManager.cs` | Responsible keeping track of the player's score |
| `MenuManager.cs` | Responsible for managing main menu, as well as showing the tutorials |

**The Visual Script**:
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `BeatIndicatorPresenter.cs` and `BeatIndicatorView.cs` | Responsible for managing the visual for indicating when beat is about to happen |
| `ParryEffectsController.cs` and <br> `ParryVFX.cs` | Responsible for managing the visual for parrying bullets |
| `TimingFeedbackController.cs` | Responsible for managing and triggering the visual vfx of the player's beat timing |

**Interface Script**:
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `ICommand.cs` | Interface to define a Command for the movement and spawning for every entity, from the player, bullet, and laser |
| `IGridOccupant.cs` | Interface for defining what object/EntityType could occupy a grid |

**The Player Script**:
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `PlayerMovement.cs` | Responsible for the players's input, movement, and movement's animation |
| `MoveCommand.cs` | Responsible for storing the player's planned move direction, which will be executed right when the beat happens |
| `GameplayBeatController.cs` | Responsible for making sure the player's collision occurs only after movement is executed (to allow for side switching with projectiles) |
| `PlayerHealth.cs` and `PlayerHealthUI.cs` | Responsible for managing and showing the player's current health |

**The Enemy Script**:
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `EnemyController.cs` | Responsible for a list of movementSequence and ScheduledAttack structs, Which will be passed to the PatternExecutor and EnemyMovement.cs on specific global beats |
| `EnemyMovement.cs.cs` | Responsible for the Enemy's movement depending on their Movement Pattern |
| `EnemyMovementPatternSO.cs` and <br> `EnemyMovementSequenceSO.cs` | Responsible for storing the Enemy's planned movements, which will be executed each beat |

**The Enemy Projectile Script**: 
<br> (Note: Hazard means Bullets and Lasers)
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `HazardSpawningPatternSO.cs` | Responsible for storing the patterns for when and where hazards are spawned, example in a V or \ pattern, and the Movement Pattern of the spawned Hazard |
| `HazardBehaviorSO.cs`  | Responsible for storing movement patterns for a spawned Hazards |
| `BulletMovementPatternSO.cs` and `LaserMovementPatternSO.cs` | Derived Class of the HazardBehaviorSO.cs, seperated to allow for different movement for each type of Hazard |
| `Bullet.cs` and `Laser.cs` | Responsible for managing the movement based on the stored Movement Pattern |
| `PatternExecutor.cs` | Responsible for starting each assigned HazardSpawningPatternSO depending on their scheduled time, and allowing them to run in parallel with each other |
| `BulletState.cs` and `LaserState.cs` | Enums responsible defining the state for bullets, regular/parryable, and laser, charging/active |


