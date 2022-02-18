---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

## Introduction


This is a **short** decription of who I am, what I've done and what I'm trying to do.

I'll decribe my **past experience** in professional software development, my degree in Computing Science, and my **transition towards game dev.**

I want to highlight my experience in **Unity/UE4** and **C#/C++**

## Contents

- [Player Controllers](#player-controllers) 
- [AI and Pathfinding](#ai-and-pathfinding) 
- [Dialogue Systems](#dialogue-systems) 
- [Animation](#animation) 
- [Shaders](#shaders) 
- [Audio](#audio) 
- [UI and Menus](#ui-and-menus) 
- [Other Development Experience](#other-development-experience) 

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
			if (horizontalInput != 0)
			{
				newVelocity.x = (horizontalInput > 0) ? 
				playerController.properties.WalkingSpeed : 
				-playerController.properties.WalkingSpeed;
			}
			newVelocity.y = 0;
			playerController.RigidBody2D.velocity = newVelocity;
		}
	}



## AI & Pathfinding

In *Project Whimsy*, I created my own implementation of the **A\* pathfinding algorithm** to control enemy pathfinding and movement.

<video src="https://user-images.githubusercontent.com/69112024/154096348-64eda84d-e279-40ce-a25c-3890e705801e.mp4" controls="controls" style="max-width: 730px;">
</video>


// Describe the concept of Astar
// Describe my implementation of it (using Chebyshev distance) and provide appropriate code/pseudocode
In order to implement this in *Project Whimsy*, I would need 3 components:
- A way to make a graph of nodes out of each level to use with the algorithm
- 
-

## Dialogue systems

In *Project Whimsy*, I utilised the [Ink narrative scripting language by Inkle](https://www.inklestudios.com/ink/) to display contextual dialogue options and responses. 

<video src="https://user-images.githubusercontent.com/69112024/153493429-8adbd973-761a-4d42-88f0-8c7ba92a63dc.mp4" controls="controls" style="max-width: 730px;">
</video>

The Ink script contains all of the games dialogue alongside the logic which determines which dialogue to display next. The Ink script is exported in **json** and accessed via the [Ink for Unity C# API](https://github.com/inkle/ink-unity-integration), allowing us to easily determine which dialogue to display on screen based upon the players previous choices and progress.

When we speak with a character, our `DialogueManager` defers to that characters `decider` section in the Ink script which contains the logic for deciding which text to display. 

<p align="center">
  <img src="https://user-images.githubusercontent.com/69112024/153217288-eac3fb80-074d-4d8d-9400-c32714c07b57.PNG"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/69112024/153217266-437a4c6e-108e-4e82-9177-7dfa6b3514a6.PNG"/>
</p>

The API returns the appropriate dialogue line (or choices). Our `DialogueManager` pops up the dialogue UI and the current line of dialogue and lets the logic play out in the Ink script. Once there is no more dialogue to display, we close the UI and gameplay resumes.

	private void ContinueDialogue()
    {

        // Remove all current dialogue UI on screen
        RemoveChildren();

        // Get current choices
        int choiceCount = story.currentChoices.Count;

        // If there's no line of text to display, display choices or end dialogue
        if (!story.canContinue)
        {
            if (choiceCount == 0)
            {
                CloseDialogueBox();
                return;
            }

            DisplayDialogueBox();
            DisplayChoices();
            return;
        }

        // Gets the next line of the story
        string text = story.Continue();

        // Display next line unless it's whitespace
        if (text != "")
        {
            // Show box
            DisplayDialogueBox();
            // Removes any extra whitespace from the text
            text = text.Trim();
            // Display the text on screen
            CreateContentView(text);
            return;
        }

        // Empty line found, look for next line
        ContinueDialogue();

    }


In *Fallen*, I created my own closed captioning system to display dialogue whilst providing a textual source of information for sound effects. 

<video src="
https://user-images.githubusercontent.com/69112024/153856779-6cf8b766-5675-4564-863b-94b6058dc5b9.mp4" controls="controls" style="max-width: 730px;">
</video>

A `DialogueLine` consists of the `text` to be displayed, the `name` of the speaker, and optionally an `AudioClip` and `AudioSource` for the voice line/sound effect to accompany it. A `DialogueBlock` consists of a `List<DialogueLine>` and flags for whether or not the block of dialogue has already been played, and whether or not the dialogue is repeatable. 

The `DialogueManager` takes a `DialogueBlock` and plays each `DialogueLine` sequentially, using an asynchronous `IEnumerator`. The `DialogueManager` will wait an appropriate length of time between displaying each `DialogueLine`, either waiting for the supplied `AudioClip` to finish playing or by varying the delay based upon the length of the text. If a new `DialogueBlock` is passed into the `DialogueManager` whilst one is currently in the process of being displayed, it will be queued up to be played after the current one finishes.

Considering *Fallen* was a two-week game jam project, the scope of the system was fairly limited. If I were to extend it's functionality, I would move the dialogue into dedicated files which can be loaded in at runtime. This would be much more maintainable and would make it much easier to implement features such as displaying alternative languages on the fly. Another easy change would be to add various font options for displaying the dialogue and closed captions, such as [the OpenDyslexic font.](https://opendyslexic.org/)


## Animation

I will demonstrate blending between animations in both engines and detail usage of **engine specific tools**.


## Shaders

I will demonstrate my use of shaders throughout my various projects.


## Audio

I will demonstrate my use of **spatial sound** to create interesting atmospheres.

I will demonstrate **dynamic audio** such as tying sound effects to footstep animations and checking the ground type to play contextual audio.

## UI and Menus

I will demonstrate various **UI and menu elements**, such as radial menus, health displays and pause/start menus.


## Other development experience

I will describe my experience of various software development tools in a professional setting.

- Other platforms (iOS - Swift, Objective C. SQL.)
- Source Control (Git, Perforce)
- Agile development, sprints (Azure DevOps, Jira)
- Teamwork suite