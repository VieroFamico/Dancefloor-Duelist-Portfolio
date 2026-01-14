# Dancefloor-Duelist-Portfolio-
The Code Design Documentation for the Dancefloor Duelist
<br> **The Manager/Controller Script**:
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `GridManager.cs` | Responsible for managing everything about the grid which every entity will be on |
| `RhythmManager.cs` | Responsible for managing the rhythm, and the timing of each beat, which every entity's movement will be based on |
| `GameplayBeatController.cs` | Responsible for making sure the collision of each entity occurs after movement has occured |
| `TimingFeedbackController.cs` | Responsible for triggering the events of the player's timing (perfect, great, good, bad, and miss) |
| `GameEvents.cs` | Responsible for storing all the events of the game |
| `MenuManager.cs` | Responsible for managing main menu, as well as showing the tutorials |
<br>

<br> **The Player Script**:
|  Script       | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `PlayerMovement.cs` | Responsible for the players's input, movement, and movement's animation |
| `MoveCommand.cs` | Responsible for storing the player's planned move direction, which will be executed right when the beat happens |
| `GameplayBeatController.cs` | Responsible for making sure the player's collision occurs only after movement is executed (to allow for side switching with projectiles) |
| `PlayerHealth.cs` | Responsible for managing the player's current health |
<br>
