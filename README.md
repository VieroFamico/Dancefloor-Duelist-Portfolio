# Dancefloor-Duelist-Portfolio-
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



