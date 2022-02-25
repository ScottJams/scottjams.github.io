---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

## About Me

Hi, I'm Scott Simpson and I'm a developer based in the UK. I've been a huge gaming enthusiast ever since my first foray with the Sega Master System, and I've been spending a lot of time learning gameplay programming in order to make the jump to games development. Below you will find examples of the projects I've been working on in order to expand my knowledge, whilst having a bit of fun along the way!

## Experience

- **BSc Computing Science** ("Automated tuning of game parameters to create compelling games" - Unity, C#)
- **2 years as an iOS Developer** creating software for major UK airlines in an Agile environment (Swift, Objective C, SQL)
- **1 year** learning **Unity/C#** and **Unreal Engine 4/C++**

## Itch.io and Github

You can check out some of the **game jam projects** found in this portfolio below:

<iframe src="https://itch.io/embed/839543?linkback=true" width="552" height="167" frameborder="0"><a href="https://scottjams.itch.io/moonshot-2020">Moonshot 2020 by ScottJams</a></iframe>

<iframe src="https://itch.io/embed/1279025?linkback=true" width="552" height="167" frameborder="0"><a href="https://scottjams.itch.io/fallen">Fallen by ScottJams</a></iframe>

And below you can find my GitHub and Itch.io links:

- [ScottJams at GitHub](https://github.com/ScottJams)
- [ScottJams at Itch.io](https://scottjams.itch.io/)

## Contents - Unity

- [Player Controllers](#player-controllers) 
- [AI and Pathfinding](#ai-and-pathfinding) 
- [Dialogue Systems](#dialogue-systems) 
- [Animation](#animation) 
- [Audio](#audio) 
- [Other Development Experience](#other-development-experience) 

## Player Controllers

In *Project Whimsy*, I used a finite state machine to compartmentalise movement and actions into individual states. 

<video src="https://user-images.githubusercontent.com/69112024/152352692-f6ee8042-9aa2-4a7e-8ee9-fdceab6ab3b8.mp4" controls="controls" style="max-width: 730px;">
</video>

A `PlayerStateMachine` keeps track of the current `PlayerState`. `PlayerState` contains the base functions `LogicUpdate()`, `SpriteUpdate()` and `PhysicsUpdate()`. Individual states inherit from `PlayerState` and add their own behaviour to each of these functions. These functions are then called in the `Update()` and `FixedUpdate()` functions of the `PlayerController` to control our Player.


<details><summary><b>Click here to expand sample code</b></summary>

<div markdown="1">
``` c#
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
```

</div></details>
&nbsp;

Some states might share a lot of functionality. For example, the actions we are able to perform in the `IdleState` and `WalkingState` are largely the same. To avoid code duplication, `GroundedState` inherits from `PlayerState` and adds common behaviours which dictate how our Player can move and act when grounded. `IdleState` and `WalkingState` then derive their base behaviour from `GroundedState` and can add their own specific functionality if required.

The following demonstrates this with very simple movement:

<details> <summary><b>Click here to expand sample code</b></summary>

<div markdown="1">
``` c#
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
```

</div></details>
&nbsp;

## AI and Pathfinding

In *Project Whimsy*, I created my own implementation of the **A\* pathfinding algorithm** to control enemy pathfinding and movement.

<video src="https://user-images.githubusercontent.com/69112024/154096348-64eda84d-e279-40ce-a25c-3890e705801e.mp4" controls="controls" style="max-width: 730px;">
</video>

The basic concept of A* pathfinding can be defined by `F = G + H`, where `F` is the total "Cost" of a given node, `G` is the distance between the node and the starting node, and `H` is a heuristic - an estimate of how far the current node is from the destination. 

<details> <summary><b>Click here to expand a breakdown of my A* pathfinding implementation</b></summary>

<div markdown="1">
In order to implement this in Unity, I needed to start with a way to create a graph of nodes to represent each area. Since the gameplay areas in *Project Whimsy* are comprised of 2D `Tilemaps`, I required some sort of grid (rather than say, a `NavMesh` in a 3D setting). I created a `PathfindingGrid` component which would serve as my method of separating the game world into `PathfindingNode`s and give me a way to iterate through them.

After generating a suitable grid of nodes, I needed to look through them and figure out a path. By maintaining a list of nodes we want to look through (the `openList`) and nodes we've already looked at (the `closedList`), we can calculate the `F` cost for each node and work our way towards the destination. Once the destination is found, we can work our way backwards to get the finished path. 

The first step is to calculate the F cost for the starting node. G would be zero, since we're at the start. Our H cost is the estimated distance to the destination, ignoring any obstacles. In this case I chose Chebyshev distance as my heuristic, which is a roundabout way of saying you can move diagonally on the grid (8-directional movement). F is simply the total of these two values.

With the initial node calculated, we can begin our main loop:
- If the current node is the destination, work backwards to return our path.
- Add current node to the closed list.
- Look at all the neighbours of the current node which aren't obstacles or on the closed list.
- If we have a faster path to a neighbour than we previously did, update the neighbours F cost and add to open list.

We iterate this until the current node is the same as the destination node. If we run out of nodes on the open list, then there's no path available to the destination.

To put all of this into action, I combined it with a state machine as outlined earlier on (check [here](#player-controllers)). When the `Player` approaches our "Bee" enemy, it moves into a `Seeking` state and begins searching for a path to the `Player`. When the path is found, we convert it into a `List` of `Vector3` and display them using `DrawRay` for debugging purposes. The enemy moves along these vectors until it reaches the player, where presumably it deals some damage or gives them a nice warm hug.

There are myriad ways to improve upon this implementation. First and foremost, this doesn't account for the size of the collider the enemy has - I'm sure if an enemy was too large or the grid size wasn't suitable then an enemy could easily get stuck. This can be further optimised; as it stands, the main loop is always finding the current nodes neighbours, when these could definitely be stored in advance if the level had static ground obstacles.
</div></details>
&nbsp;

<video src="https://user-images.githubusercontent.com/69112024/155009972-743d038e-8258-4bbe-ade3-978d16ef9617.mp4" controls="controls" style="max-width: 730px;">
</video>
&nbsp;

## Dialogue systems

In *Fallen*, I created my own closed captioning system to display dialogue and provide text descriptions for sound effects. 

<video src="
https://user-images.githubusercontent.com/69112024/153856779-6cf8b766-5675-4564-863b-94b6058dc5b9.mp4" controls="controls" style="max-width: 730px;">
</video>

A `DialogueLine` consists of the `text` to be displayed, the `name` of the speaker, and optionally an `AudioClip` and `AudioSource` for the voice line/sound effect to accompany it. A `DialogueBlock` consists of a `List<DialogueLine>` and flags for whether or not the block of dialogue has already been played, and whether or not the dialogue is repeatable. 

The `DialogueManager` takes a `DialogueBlock` and plays each `DialogueLine` sequentially, using an asynchronous `IEnumerator`. The `DialogueManager` will wait an appropriate length of time between displaying each `DialogueLine`, either waiting for the supplied `AudioClip` to finish playing or by varying the delay based upon the length of the text. If a new `DialogueBlock` is passed into the `DialogueManager` whilst one is currently in the process of being displayed, it will be queued up to be played after the current one finishes.

Considering *Fallen* was a two-week game jam project, the scope of the system was fairly limited. If I were to extend its functionality, I would move the dialogue into dedicated files which can be loaded in at runtime. This would be much more maintainable and would make it much easier to implement features such as displaying alternative languages. Another easy change would be to add various font options for displaying the dialogue and closed captions, such as [the OpenDyslexic font.](https://opendyslexic.org/)
&nbsp;

In *Project Whimsy*, I utilised the [Ink narrative scripting language by Inkle](https://www.inklestudios.com/ink/) to display contextual dialogue options and responses. 

<video src="https://user-images.githubusercontent.com/69112024/153493429-8adbd973-761a-4d42-88f0-8c7ba92a63dc.mp4" controls="controls" style="max-width: 730px;">
</video>

The Ink script contains all of the game's dialogue alongside the logic which determines which dialogue to display next. The Ink script is exported in **json** and accessed via the [Ink for Unity C# API](https://github.com/inkle/ink-unity-integration), allowing us to easily determine which dialogue to display on screen based upon the players previous choices and progress.

When we speak with a character, our `DialogueManager` defers to that characters `decider` section in the Ink script which contains the logic for deciding which text to display. 

<p align="center">
  <img src="https://user-images.githubusercontent.com/69112024/153217288-eac3fb80-074d-4d8d-9400-c32714c07b57.PNG"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/69112024/153217266-437a4c6e-108e-4e82-9177-7dfa6b3514a6.PNG"/>
</p>

The API returns the appropriate dialogue line (or choices). Our `DialogueManager` pops up the dialogue UI and the current line of dialogue and lets the logic play out in the Ink script. `ContinueDialogue()` is called as per user input. Once there is no more dialogue to display, we close the UI and gameplay resumes.

<details> <summary><b>Click here to expand example code</b></summary>

<div markdown="1">
``` c#
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

	// If the next line is whitespace, skip over it
	if (text == "")
	{
		ContinueDialogue();
	}
	
	// Show box
	DisplayDialogueBox();
	// Removes any extra whitespace from the text
	text = text.Trim();
	// Display the text on screen
	CreateContentView(text);
	return;
}
```

</div></details>
&nbsp;


## Animation

In *Fallen*, I imported animations from [Mixamo](https://www.mixamo.com) and used an `Animator` to transition between them as appropriate. I also used `Animations` for fading in and out during scene transitions.

<video src="https://user-images.githubusercontent.com/69112024/155396952-c836e898-dc3d-484a-a2e4-007956fd6d87.mp4" controls="controls" style="max-width: 730px;">
</video>

Since this was a game jam project, I didn't commit to fully fleshing it out with animations such as rotating in position, moving diagonally, moving backwards and so forth - but it was a good introduction to blending between more complex `Animation` timelines.

In *Moonshot*, I first learned to use the `Animation` timeline to create simple animations for the various player states.

<video src="https://user-images.githubusercontent.com/69112024/155396916-688dc3fd-3b8f-4e11-9622-e8b06c2e8d68.mp4" controls="controls" style="max-width: 730px;">
</video>

I also used `Animators` in this project for basic UI effects, such as when picking up collectibles.

<video src="https://user-images.githubusercontent.com/69112024/155396943-cff7ed08-8685-4cd4-a857-9af3f7fddcfc.mp4" controls="controls" style="max-width: 730px;">
</video>

## Audio

In *Fallen*, I used a lot of spatial audio to contribute to the overall atmosphere.

<video src="https://user-images.githubusercontent.com/69112024/155536810-8cdeb24f-300e-4d5c-a0d2-964b1913174b.mp4" controls="controls" style="max-width: 730px;">
</video>

<video src="https://user-images.githubusercontent.com/69112024/155536837-264a2cc7-ef18-4295-9b58-58c113f82d5d.mp4" controls="controls" style="max-width: 730px;">
</video>

I also used various `AudioFilters` to create interesting effects, such as a `Low Pass Filter` to create this muffled-sounding argument.

<video src="https://user-images.githubusercontent.com/69112024/155536869-65668379-b58c-42bb-8790-0d12e43c34c9.mp4" controls="controls" style="max-width: 730px;">
</video>

In all of these clips you can see that I tied sound effects to the player's `Animation` timeline to play sounds during walking animations. When the player's foot touches the ground during the `Walking` and `Running` states, an `AnimationEvent` is triggered. A script then checks for which type of ground we're currently walking over (wooden, concrete etc) and plays an appropriate sound effect. There's a good variety of sound effects for each different type of terrain in order to avoid repetition.


## Other development experience

In addition to my game development experience, I have **at least 2 years professional development experience** using the following platforms, languages, and tools:

<p align="center">
  <img src="https://user-images.githubusercontent.com/69112024/155584338-4f1413fe-0a7f-4f51-a161-faaec3e20840.png"/>
</p>

## Contact me

You can reach me via email at [ScottJamsDev@gmail.com](ScottJamsDev@gmail.com)
You can also check out my socials at [Twitter](https://twitter.com/ScottJamsDev) and [Github](https://github.com/ScottJams)!