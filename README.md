# Dancefloor-Duelist-Portfolio-
The Code Design Documentation for the Dancefloor Duelist <br>
**The Manager/Controller Script**:
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `GridManager.cs` | Responsible for managing everything about the grid which every entity will be on |
| `RhythmManager.cs` | Responsible for managing the rhythm, and the timing of each beat and player's input timing, which every entity's movement will be based on |
| `GameplayBeatController.cs` | Responsible for making sure the collision of each entity occurs after movement is done |
| `GameEvents.cs` | Responsible for storing all the events of the game |
| `MenuManager.cs` | Responsible for managing main menu, as well as showing the tutorials |

**The Visual Script**:
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `BeatIndicatorPresenter.cs` and `BeatIndicatorView.cs` | Responsible for managing the visual for indicating beat |
| `ParryEffectsController.cs` and `ParryVFX.cs` | Responsible for managing the visual for parrying bullets |
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

