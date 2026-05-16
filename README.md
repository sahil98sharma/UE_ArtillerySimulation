# UE_ArtillerySimulation
## Assignment for Parallax Labs
## Engine Version: Unreal Engine 5.6.1


### Overview 

This project implements a two-player artillery simulation featuring an Observer and a Gunner. The core architecture relies on a centralized Player Controller for input routing and heavily encapsulated Pawns for execution, ensuring a clean separation of logic.

**1. Player Input & State Management**
 - Centralized Controller: `BP_ArtilleryController` acts as the single source of truth for all player inputs using the Enhanced Input System. This guarantees that input mappings persist seamlessly when toggling between actors.
 - Enum State Machine: The controller uses a custom enum `(EArtilleryController)` to track the active role. When an input is triggered (e.g., `IA_ElevateBarrel` or `IA_PlaceTargetMarker`), the controller checks the enum and casts directly to the relevant possessed Pawn (`BP_GunnerPawn` or `BP_ObserverPawn`) to call specific custom execution events.

**2. Pawn Logic:**
 - `The Observer (BP_ObserverPawn)`: Houses the target marker logic. Upon receiving the command from the controller, it calculates a screen-to-world line trace to place the marker. It then broadcasts the updated target coordinates back to the controller, which passes them to the Gunner.
 - `The Gunner (BP_GunnerPawn)`: Handles its own local mechanical math. It processes the elevation inputs to rotate the barrel mesh and uses an `RInterpTo` function on `Event Tick` to smoothly align the turret base's yaw toward the Observer's target marker.

**3. Ballistics & UI**
 - Physics: `The BP_ArtilleryShell` relies entirely on Unreal's native `ProjectileMovementComponent`. By setting an initial local-space velocity upon spawning at the barrel tip, the component automatically calculates the correct gravity-based ballistic trajectory.
 - Event-Driven HUD: The `WBP_GunnerHUD` is instantiated and destroyed based on the `OnPossess` and `OnUnpossess` events within the Gunner. The UI relies on event-driven updates rather than tick-based property binding to display current elevation and firing status optimally.
 - Selection Screen: The pre-game character selection utilizes a single Master Material. The `Level Blueprint (PrototypeMap)` intercepts UI dispatchers to create and apply `Dynamic Material Instances` to the Pawns, adjusting `vector color` and `scalar brightness` parameters without requiring duplicate material assets.
