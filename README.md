## Automaton Gym - Utility AI Experiment

This repository contains my AI gameplay gym built in Unreal Engine Blueprints for assignment 2. This project is to be used for education purposes. Architecture and code choices made are for readability and simplicity to extend.

Instead of using a rigid, hardcoded behavior tree or finite state machine, this system evaluates live variables against float curves to dynamically choose the most desirable action for an enemy.

---
<img width="1630" height="710" alt="gamedshot" src="https://github.com/user-attachments/assets/ac031ec0-75df-4921-89b7-9365360649aa" />

## Gameplay Demo & Loop

The enemy AI dynamically switches between 6 different actions depending on the situation, its current stamina, range to the player, and action cooldowns:
* Attacks: Light Attack, Heavy Attack, Dash Attack
* Movement: Walk, Strafe, Backstep

---

## Technical Architecture Breakdown

The entire architecture is completely modular and decoupled, built around these core systems:

### 1. Data Assets and Gameplay Tags
* Data Asset Actions: Each action is a discrete Data Asset. I used Gameplay Tags as dynamic identifiers so actions can be easily customized per enemy type.
* Attribute Mapping: Inside each asset, a gameplay tag map holds a float of the attribute like damage and stamina cost. This prevents non-damaging movement tasks from leaving behind heaps of unused variables.

### 2. Interface Pipeline and Math Loop
* Actor Component: The core utility scoring loop lives inside an Actor Component attached to the AI Controller.
* Unreal Interface: To avoid a messy connection between blueprints, the component uses an Interface to cleanly pull live variables like current stamina and distance from the Enemy Blueprint.
* The Multiplicative Formula: The utility formula sets the score to 1.0 and loops through every action and its associated consideration curves. It evaluates the data on the Unreal Float Curve asset and multiplies that to the current score to get the final total.

### 3. Filter Function and Cooldown Map
* Anti-Spam Filter: I made a filter function and created a cooldown map so the AI didn't spam actions quickly and run out of stamina. 
* The Math: When an action finishes executing, a function adds the cooldown duration to the current Game Time. The filter checks this map; if the current Game Time is less than that sum, the action is filtered out and not considered in the utility loop.

### 4. Consolidated Behavior Tree Task
* Instead of creating many separate tasks inside the behavior tree, I consolidated all tasks into one universal task that executes every action. 
* The task reads the winning action tag from the Blackboard, subtracts the appropriate stamina, and uses a Switch on Gameplay Tag node inside the Enemy Blueprint to reference and run its unique character behavior code.
