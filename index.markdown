---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

## Introduction


This is a **short** decription of who I am, what I've done and what I'm trying to do.

I'll decribe my **past experience** in professional software development, my degree in Computing Science, and my **transition towards game dev.**

I want to highlight my experience in **Unity/UE4** and **C#/C++**


## Player Controllers

In *Project Whimsy*, I used a finite state machine to compartmentalise movement and actions into individual states. 

<video src="https://user-images.githubusercontent.com/69112024/152352692-f6ee8042-9aa2-4a7e-8ee9-fdceab6ab3b8.mp4" controls="controls" style="max-width: 730px;">
</video>

A `PlayerStateMachine` keeps track of the current `PlayerState`. `PlayerState` contains the base functions `LogicUpdate()`, `SpriteUpdate()` and `PhysicsUpdate()`. Individual states inherit from `PlayerState` and add their own behaviour to each of these functions. These functions are then called in the `Update()` and `FixedUpdate()` functions of the `PlayerController` to control our Player.

	public class PlayerController : MonoBehaviour 
	{
		void Update()
		{
			// Current state logic and sprite updates
			movementStateMachine.LogicUpdate();
			movementStateMachine.SpriteUpdate();	
		}
	
		void FixedUpdate()
		{
			// Current state movement and physics updates
			movementStateMachine.PhysicsUpdate();
		}
	}

Some states might share a lot of functionality. For example, the actions we are able to perform in the `IdleState` and `WalkingState` are largely the same. To avoid code duplication, `GroundedState` inherits from `PlayerState` and adds common behaviours which dictate how our Player can move and act when grounded. `IdleState` and `WalkingState` then derive their base behaviour from `GroundedState` and can add their own specific functionality if required.

The following demonstrates this with very simple movement:

	public class WalkState : GroundedState 
	{
		public override void PhysicsUpdate()
		{
			base.PhysicsUpdate();
		}
	}
	
	public class GroundedState : PlayerState
	{
		public override void PhysicsUpdate()
		{
			// Simple movement, directly modifying velocity
			float horizontalInput = inputActions.HorizontalMoveInput;
			Vector2 newVelocity = Vector2.zero;
			if (horizontalInput == 0)
			{
				newVelocity.x = 0;
			}
			else
			{
				newVelocity.x = (horizontalInput > 0) ? playerController.properties.WalkingSpeed : -playerController.properties.WalkingSpeed;
			}
			newVelocity.y = 0;
			playerController.RigidBody2D.velocity = newVelocity;
		}
	}



## AI & Pathfinding

I will demonstrate enemies using various **movement and attack behaviours** during various states and discuss how it was accomplished.

I will demonstrate **enemy pathfinding** and specifically mention my impementation of **Astar pathfinding**.

## Dialogue systems

In *Fallen*, I created a closed captioning system to display dialogue and provide a non-audio source of information for sound effects. 

In *Project Whimsy*, I utilised the [Ink narrative scripting language by Inkle.](https://www.inklestudios.com/ink/) to display contextual dialogue options and responses.  Variables are updated within the dialogue script and referenced via the Ink for Unity C# API. 


## Area Structure and Save Games

I will demonstrate how I **serailised game state information** to create save files.

I will demonstrate how areas of the game **spawn actors based upon saved game state** and how **NPCs and objects are contextual with regards to game progress.**


## Animation

I will demonstrate blending between animations in both engines and detail usage of **engine specific tools**.


## Shaders

I will demonstrate my use of shaders throughout my various projects.


## Audio

I will demonstrate my use of **spatial sound** to create interesting atmospheres.

I will demonstrate **dynamic audio** such as tying sound effects to footstep animations and checking the ground type to play contextual audio.

## UI and Menus

I will demonstrate various **UI and menu elements**, such as radial menus, health displays and pause/start menus.


## Multiplayer & Networking



## Other development experience

I will describe my experience of various software development tools in a professional setting.

- Other platforms (iOS - Swift, Objective C. SQL.)
- Source Control (Git, Perforce)
- Agile development, sprints (Azure DevOps, Jira)
- Teamwork suite



## Placeholder video embed

Testing a placeholder video embed.

<video src="https://user-images.githubusercontent.com/69112024/143461891-da157848-f3d9-4156-8a6f-d4105aacff7e.mp4" controls="controls" style="max-width: 730px;">
</video>
Check video above.

