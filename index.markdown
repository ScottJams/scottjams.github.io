---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

## About Me

Hi, I'm Scott Simpson and I'm an aspiring Gameplay Programmer based in the UK. Below you will find examples of the projects I've been working on and my previous experience as a developer.

## Experience

- **6 months** on *The Catacombs* - A turn based tactical roguelite - **Unity/C#** 
- **2 years as an iOS Developer** creating software for major airlines in an Agile environment - **Swift, Objective C, SQL**
- **BSc Computing Science** ("Automated tuning of game parameters to create compelling games" - **Unity, C#**)
- **1 year** Personal experience with **Unreal Engine 4/C++**

In addition to my game development experience, I have **at least 2 years professional development experience** using the following platforms, languages, and tools:

<p align="center">
  <img src="https://user-images.githubusercontent.com/69112024/155584338-4f1413fe-0a7f-4f51-a161-faaec3e20840.png"/>
</p>


## The Catacombs

Delve into The Catacombs as a plucky gang of cats, with a bit too much curiosity for their own good!
*The Catacombs* is a turn-based tactical Roguelite project with an emphasis on narrative, currently being developed by myself. 

Below is some Work in Progress footage of the project, demonstrating the combat system as well as the party forming menu.

# Combat system
<video src="https://user-images.githubusercontent.com/69112024/190674421-2027270d-840c-4aaa-8e5c-9051abda7e5c.mp4" controls="controls" style="max-width: 730px;">
</video>

# Pathfinding
<video src="https://user-images.githubusercontent.com/69112024/191861894-ecce2ee5-6163-455c-ac71-4c63a6b5778d.mp4" controls="controls" style="max-width: 730px;">
</video>

# Roster setup (WIP)
<video src="https://user-images.githubusercontent.com/69112024/191869430-1a2e3322-9e18-44cb-b760-3c83955504f9.mp4" controls="controls" style="max-width: 730px;">
</video>

# Animation
<video src="https://user-images.githubusercontent.com/69112024/191863324-6f73b40a-6e17-47ad-bd7a-745baa6d29b8.mp4" controls="controls" style="max-width: 730px;">
</video>


## Itch.io and Github

You can check out some of the **game jam projects** found in this portfolio below:

<iframe src="https://itch.io/embed/1279025?linkback=true" width="552" height="167" frameborder="0"><a href="https://scottjams.itch.io/fallen">Fallen by ScottJams</a></iframe>

<iframe src="https://itch.io/embed/839543?linkback=true" width="552" height="167" frameborder="0"><a href="https://scottjams.itch.io/moonshot-2020">Moonshot 2020 by ScottJams</a></iframe>

And below you can find my GitHub and Itch.io links:

- [ScottJams at GitHub](https://github.com/ScottJams)
- [ScottJams at Itch.io](https://scottjams.itch.io/)


## Unity Development Contents

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

<details><summary><b>Click here to expand sample code</b></summary>
<div markdown="1">

A `PlayerStateMachine` keeps track of the current `PlayerState`. `PlayerState` contains the base functions `LogicUpdate()`, `SpriteUpdate()` and `PhysicsUpdate()`. Individual states inherit from `PlayerState` and add their own behaviour to each of these functions. These functions are then called in the `Update()` and `FixedUpdate()` functions of the `PlayerController` to control our Player.

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

Some states might share a lot of functionality. For example, the actions we are able to perform in the `IdleState` and `WalkingState` are largely the same. To avoid code duplication, `GroundedState` inherits from `PlayerState` and adds common behaviours which dictate how our Player can move and act when grounded. `IdleState` and `WalkingState` then derive their base behaviour from `GroundedState` and can add their own specific functionality if required.

</div></details>
&nbsp;

&nbsp;

## AI and Pathfinding

In *The Catacombs*, I created my own implementation of the **A\* pathfinding algorithm** to control pathfinding and movement for player and enemy units.

<video src="https://user-images.githubusercontent.com/69112024/191861894-ecce2ee5-6163-455c-ac71-4c63a6b5778d.mp4" controls="controls" style="max-width: 730px;">
</video>

In *Project Whimsy*, I used a different heuristic to allow characters to have diagonal movement using the same implementation of the **A\* pathfinding algorithm** I created for *The Catacombs*.

<video src="https://user-images.githubusercontent.com/69112024/154096348-64eda84d-e279-40ce-a25c-3890e705801e.mp4" controls="controls" style="max-width: 730px;">
</video>
&nbsp;

## Dialogue systems

In *Fallen*, I created my own closed captioning system to display dialogue and provide text descriptions for sound effects. 

<video src="
https://user-images.githubusercontent.com/69112024/153856779-6cf8b766-5675-4564-863b-94b6058dc5b9.mp4" controls="controls" style="max-width: 730px;">
</video>

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

The API returns the appropriate dialogue line (or choices). Our `DialogueManager` pops up the dialogue UI and the current line of dialogue and lets the logic play out in the Ink script. Once there is no more dialogue to display, we close the UI and gameplay resumes.

## Animation

In *The Catacombs*, I created, rigged, and animated 2D sprites, making use of Inverse Kinematics to create some "bouncy" animations. These animations were created to suit the "quirky" nature of the cat characters in *The Catacombs*

<video src="https://user-images.githubusercontent.com/69112024/191863324-6f73b40a-6e17-47ad-bd7a-745baa6d29b8.mp4" controls="controls" style="max-width: 730px;">
</video>


In *Fallen*, I imported animations from [Mixamo](https://www.mixamo.com) and used an `Animator` to transition between them as appropriate. I also used `Animations` for fading in and out during scene transitions.

<video src="https://user-images.githubusercontent.com/69112024/155396952-c836e898-dc3d-484a-a2e4-007956fd6d87.mp4" controls="controls" style="max-width: 730px;">
</video>

Since this was a game jam project, I didn't commit to fully fleshing it out with animations such as rotating in position, moving diagonally, moving backwards and so forth - but it was a good introduction to blending between more complex `Animation` timelines.

I also used `Animators` in *Moonshot* for basic UI effects, such as when picking up collectibles.

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


## Contact me

You can reach me via email at [ScottJamsDev@gmail.com](ScottJamsDev@gmail.com).

You can also check out my socials at [Twitter](https://twitter.com/ScottJamsDev) and [Github](https://github.com/ScottJams).