---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

## About Me

Hi, I'm Scott Simpson and I'm a Developer based in the UK. Below you will find examples of the projects I've been working on and my previous experience as a developer.

## Experience
- **Coatsink - Programmer - 2 years** - Lead Developer on the online multiplayer game [Ready, Set, Cook!](https://www.facebook.com/gaming/play/301781071466501) for the Facebook Instant Gaming platform. **Unity, C#, JavaScript, Node.JS, AWS**
- **Programmer - 2 years** experience with Unity working on various indie projects such as *The Catacombs* - **Unity, C#**
- **Evoke Systems - Junior iOS Developer - 2 years** creating software for major airlines in an Agile environment - **Objective C, Swift, C#**

- **BSc Computing Science** - Final paper on game development in Unity - **Unity, C#**
- **1 year** experience with UE4 through University & personal use - **Unreal Engine, C++** 

&nbsp;

## Itch.io and Github

You can check out some of the **game jam projects** found in this portfolio below:

<div class="itchio-widget">
  <iframe frameborder="0" src="https://itch.io/embed/2694166?dark=true" width="552" height="167"><a href="https://scottjams.itch.io/bete-epoque-horror-in-paris">Bête Epoque: Horror in Paris by ScottJams, Vurj, Illidave Stormdave, Errol 🦇</a></iframe>
</div>

<div class="itchio-widget">
  <iframe src="https://itch.io/embed/839543?linkback=true" width="552" height="167" frameborder="0"></iframe>
</div>

<div class="itchio-widget">
  <iframe src="https://itch.io/embed/1279025?linkback=true" width="552" height="167" frameborder="0"></iframe>
</div>

And below you can find my GitHub and Itch.io links:

- [ScottJams at GitHub](https://github.com/ScottJams)  
- [ScottJams at Itch.io](https://scottjams.itch.io/)


And below you can find my GitHub and Itch.io links:

- [ScottJams at GitHub](https://github.com/ScottJams)
- [ScottJams at Itch.io](https://scottjams.itch.io/)


## Contents

- [AI and Pathfinding](#ai-and-pathfinding) 
- [Loot System](#loot-system) 
- [Player Controllers](#player-controllers) 
- [Dialogue Systems](#dialogue-systems) 
- [Animation](#animation) 
- [Audio](#audio) 
- [Other Development Experience](#other-development-experience) 


# AI and Pathfinding

In *The Catacombs*, I created my own implementation of the **A\* pathfinding algorithm** to control pathfinding and movement for player and enemy units.

<center><video src="https://user-images.githubusercontent.com/69112024/190674421-2027270d-840c-4aaa-8e5c-9051abda7e5c.mp4" controls="controls" style="max-width: 1050px;">
</video></center>
&nbsp;

## Pathfinding Scope

The scope for my pathfinding system for *The Catacombs* was that it should meet the following requirements:
- Allows for pathfinding across a 2D grid comprised of nodes.
- Uses the A* pathfinding algorithm.
- Can return the shortest path, given any two nodes.
- Can return all the nodes within a given maximum movement range.

## Pathfinding Usage

- Initialise `PathfindingNode` objects, supplying their positions on the grid and whether or not they can be traversed.
- Initialise a `PathfindingGrid` object, supplying the nodes to be connected on the grid, and optionally the heuristic to use for distance calculations.
- Call public function `FindPath` on the `PathfindingGrid` to get the path between any two given nodes.
- Call public function `NodesInRange` on the `PathfindingGrid` to get all of the nodes within a given movement range.

## Pathfinding Code

<details><summary><b> Click here to expand PathfindingNode.cs </b></summary>

<div markdown="1">
``` c# 
using System.Collections.Generic;
using System.Numerics;

public class PathfindingNode
{
    #region Node Properties
    /// <summary>
    /// Whether the PathfindingNode is currently traversable or not. 
    /// Nodes which are not Traversable will not be able to be pathed through during Pathfinding.
    /// </summary>
    public bool Traversable { get; private set; }

    /// <summary>
    /// Whether the node is currently within the current movement range or not.
    /// </summary>
    public bool CurrentlyInRange { get; private set; }

    /// <summary>
    /// The PathfindingNodes adjacent to this node.
    /// </summary>
    public List<PathfindingNode> NeighbourNodes { get; private set; }

    /// <summary>
    /// The parent node, used to create a path during pathfinding.
    /// </summary>
    public PathfindingNode ParentNode { get; private set; }

    /// <summary>
    /// The coordinates representing this nodes position on the grid.
    /// </summary>
    public Vector2 GridCoordinates { get; private set; }
    #endregion

    #region Pathfinding Values
    /// <summary>
    /// G is the distance between the current node and the start node.
    /// </summary>
    public float G { get; private set; }

    /// <summary>
    /// H is the estimated distance from the current node to the target node.
    /// </summary>
    public float H { get; private set; }

    /// <summary>
    /// F is the total cost of the node.
    /// </summary>
    public float F => G + H;
    #endregion
   
    /// <summary>
    /// A node to be used as part of a PathfindingGrid for pathfinding.
    /// </summary>
    /// <param name="traversable">Whether this node is Traversable or not for the purposes of Pathfinding.</param>
    /// <param name="gridPositionX">This nodes X position on the grid.</param>
    /// <param name="gridPositionY">This nodes Y position on the grid.</param>
    public PathfindingNode(bool traversable, float gridPositionX, float gridPositionY) 
    {
        Traversable = traversable;
        GridCoordinates = new Vector2(gridPositionX, gridPositionY);
    }

    #region Public Functions

    /// <summary>
    /// Stores the neighbours of this node for pathfinding.
    /// </summary>
    /// <param name="neighbours">The list of PathfindingNodes to set as this nodes neighbours.</param>
    public void CacheNeighbours(List<PathfindingNode> neighbours)
    {
        NeighbourNodes = neighbours;
    }
    
    /// <summary>
    /// Sets the parent of this node in order to create a path. 
    /// </summary>
    /// <param name="parentNode">The node to set as parent of this node.</param>
    public void SetParent(PathfindingNode parentNode)
    {
        ParentNode = parentNode;
    }

    /// <summary>
    /// Sets whether or not this node is currently traversable during pathfinding.
    /// </summary>
    /// <param name="value">The value to set.</param>
    public void SetTraversable(bool value)
    {
        Traversable = value;
    }

    /// <summary>
    /// Sets whether or not this node is currently in range during amovement range search.
    /// </summary>
    /// <param name="value">The value to set.</param>
    public void SetInRange(bool value)
    {
        CurrentlyInRange = value;
    }

    /// <summary>
    /// Sets the G value for this node. G is the distance between the current node and the start node.
    /// </summary>
    /// <param name="value"></param>
    public void SetG(float value)
    {
        G = value;
    }

    /// <summary>
    /// Sets the H value for this node. H is the estimated distance from the current node to the target node.
    /// </summary>
    /// <param name="value"></param>
    public void SetH(float value)
    {
        H = value;
    }

    /// <summary>
    /// Resets the G and H values of this node.
    /// </summary>
    public void ResetCosts()
    {
        G = 0;
        H = 0;
    }
    #endregion
}
```

</div></details>

&nbsp;

<details><summary><b> Click here to expand PathfindingGrid.cs </b></summary>

<div markdown="1">
``` c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Numerics;

public class PathfindingGrid
{
    #region Grid Properties
    /// <summary>
    /// The list of nodes this grid is comprised of.
    /// </summary>
    public List<PathfindingNode> Nodes { get; private set; }

    /// <summary>
    /// The directions in which to search for neighbours. 
    /// </summary>
    private List<Vector2> _directions;

    /// <summary>
    /// The current cost to navigate a node.
    /// </summary>
    private float _currentMovementCost = 10.0f;
    #endregion

    /// <summary>
    /// Connects a grid of PathfindingNodes by caching the neighbours of all given nodes.
    /// </summary>
    /// <param name="nodes">The list of all PathfindingNodes to be included in this grid.</param>
    /// <param name="heuristic">The pathfinding heuristic to use when calculating distance. </param>
    public PathfindingGrid(List<PathfindingNode> nodes, PathfindingHeuristic heuristic = PathfindingHeuristic.Manhattan)
    {
        Nodes = nodes;

        switch (heuristic)
        {
            case PathfindingHeuristic.Manhattan:
                _directions = Manhattan;
                break;
            case PathfindingHeuristic.Chebyshev:
                _directions = Chebyshev;
                break;
        }

        foreach (PathfindingNode node in Nodes)
        {
            node.CacheNeighbours(GetNeighbours(node));
        }

    }

    #region Public API
    /// <summary>
    /// Finds the shortest path between the start node and the target node using the A* pathfinding algorithm.
    /// </summary>
    /// <param name="startNode">The starting node of the pathfinding search.</param>
    /// <param name="targetNode">The target node to path toward.</param>
    /// <returns>A list of PathfindingNodes representing the shortest path, or an empty list if no path is found.</returns>
    public List<PathfindingNode> FindPath(PathfindingNode startNode, PathfindingNode targetNode)
    {
        ClearPathfindingValues();
        List<PathfindingNode> openList = new List<PathfindingNode>() { startNode };
        List<PathfindingNode> closedList = new List<PathfindingNode>();

        // Iterate open list until path is found, or open list is empty.
        while (openList.Count > 0)
        {
            PathfindingNode currentNode = openList[0];
            UpdateCurrentNode(ref openList, ref closedList, ref currentNode);

            // Success condition - Path Found - Stop if target node is added to closed list 
            if (currentNode == targetNode)
            {
                PathfindingNode currentPathNode = targetNode;
                List<PathfindingNode> path = new List<PathfindingNode>();

                // Going backwards, add each parent of the previous node to the list to get the path.
                while (currentPathNode != startNode)
                {
                    path.Add(currentPathNode);
                    currentPathNode = currentPathNode.ParentNode;
                }

                return path;  
            }

            SearchNeighbourNodes(currentNode, ref openList, closedList, targetNode);
        }

        // No path found - return an empty path
        return new List<PathfindingNode>();
    }

    /// <summary>
    /// Finds and returns the PathfindingNodes within the given range from the start node using the A* pathfinding algorithm.
    /// </summary>
    /// <param name="grid">The PathfindingGrid to find a range of nodes on.
    /// <param name="startNode">The starting node for the range search.</param>
    /// <param name="maxMovementRange">The maximum movement range from the start node.</param>
    /// <returns>A list of PathfindingNodes that are within the specified range.</returns>
    public List<PathfindingNode> NodesInRange(PathfindingNode startNode, float maxMovementRange)
    {
        ClearPathfindingValues();    
        List<PathfindingNode> openList = new List<PathfindingNode>() { startNode };
        List<PathfindingNode> closedList = new List<PathfindingNode>();
        List<PathfindingNode> validNodes = new List<PathfindingNode>();

        // Iterate until open list is empty.
        while (openList.Count > 0)
        {
            PathfindingNode currentNode = openList[0];
            UpdateCurrentNode(ref openList, ref closedList, ref currentNode);

            // Success Condition - Add to valid movement nodes if F cost is within movement range
            if (currentNode.F <= maxMovementRange && !validNodes.Contains(currentNode))
            {
                currentNode.SetInRange(true);
                validNodes.Add(currentNode);
            }
            else
            {
                currentNode.SetInRange(false);
            }

            SearchNeighbourNodes(currentNode, ref openList, closedList);
        }

        return validNodes;
    }

    /// <summary>
    /// Resets pathfinding by clearing the F, G and H costs of each PathfindingNode.
    /// </summary>
    public void ClearPathfindingValues()
    {
        foreach (PathfindingNode node in Nodes)
            node.ResetCosts();
    }

    /// <summary>
    /// Updates the movement cost for navigating a single node.
    /// </summary>
    /// <param name="newValue"></param>
    public void SetPathfindingCost(float newValue)
    {
        _currentMovementCost = newValue;
    }
    #endregion

    #region Internal Pathfinding Functions
    /// <summary>
    /// Looks for the lowest F cost node on the open list, makes it the current node, and switches it to the closed list.
    /// </summary>
    private void UpdateCurrentNode(ref List<PathfindingNode> openList,
        ref List<PathfindingNode> closedList,
        ref PathfindingNode currentNode)
    {
        foreach (PathfindingNode node in openList)
        {
            if (node.F < currentNode.F || (node.F == currentNode.F) && (node.H < currentNode.H))
                currentNode = node;
        }

        // Switch current node to the closed list.
        closedList.Add(currentNode);
        openList.Remove(currentNode);
    }

    /// <summary>
    /// Finds valid neighbour nodes and searches for a better path.
    /// </summary>
    private void SearchNeighbourNodes(PathfindingNode currentNode,
        ref List<PathfindingNode> openList,
        List<PathfindingNode> closedList,
        PathfindingNode targetNode = null)
    {

        // Get neighbour nodes that are currently traversable and not on the closed list
        var validNeighbours = currentNode.NeighbourNodes.Where(node => node.Traversable && !closedList.Contains(node));

        foreach (PathfindingNode neighbourNode in validNeighbours)
        {
            float costToNeighbour = currentNode.G + GetEstimatedDistance(currentNode, neighbourNode);
            bool inOpenList = openList.Contains(neighbourNode);

            // If neighbour is not on the open list, or if neighbour is a better path (lower G), proceed
            if (inOpenList && costToNeighbour >= neighbourNode.G)
                continue;

            // Make current node the parent of the adjacent node and recalculate G
            neighbourNode.SetG(costToNeighbour);
            neighbourNode.SetParent(currentNode);

            // Target node supplied
            if (targetNode != null)
            {
                // Add neighbour to open list and calculate H
                neighbourNode.SetH(GetEstimatedDistance(neighbourNode, targetNode));
                openList.Add(neighbourNode);
            }
            // Target node not supplied
            else
            {
                // Add neighbour to open list and set H to 0
                neighbourNode.SetH(0);
                openList.Add(neighbourNode);
            }
        }

    }

    /// <summary>
    /// Gets the estimated distance cost between two nodes.
    /// </summary>
    /// <param name="startNode">The starting node.</param>
    /// <param name="targetNode">The target node.</param>
    /// <param name="currentMovementCost">The movement cost per node.</param>
    /// <returns>The estimated distance cost between this node and the target node.</returns>
    private float GetEstimatedDistance(PathfindingNode startNode, PathfindingNode targetNode)
    {

        Vector2 distance = new Vector2
    (Math.Abs(startNode.GridCoordinates.X - targetNode.GridCoordinates.X),
    Math.Abs(startNode.GridCoordinates.Y - targetNode.GridCoordinates.Y));

        float lowestDistance = Math.Min(distance.X, distance.Y);
        float highestDistance = Math.Max(distance.X, distance.Y);
        float horizontalMovesRequired = highestDistance - lowestDistance;

        return (lowestDistance * _currentMovementCost) + (horizontalMovesRequired * _currentMovementCost);
    }
    #endregion

    #region Internal Grid Setup
    /// <summary>
    /// Returns the neighbours of the given source node using the current directions.
    /// </summary>
    private List<PathfindingNode> GetNeighbours(PathfindingNode sourceNode)
    {
        List<PathfindingNode> neighbours = new List<PathfindingNode>();

        neighbours.AddRange(_directions.
    Select(direction => GetNodeAtPosition(sourceNode.GridCoordinates + direction)).
    Where(node => node != null));

        return neighbours;
    }

    /// <summary>
    /// Returns the PathfindingNode at the given grid position, if it exists.
    /// </summary>
    private PathfindingNode GetNodeAtPosition(Vector2 gridPosition)
    {
        return Nodes.Where(node => node.GridCoordinates == gridPosition).FirstOrDefault();
    }

    private readonly List<Vector2> Chebyshev = new List<Vector2>() {
                    new Vector2(1, 0),
                    new Vector2(-1, 0),
                    new Vector2(0, 1),
                    new Vector2(0, -1),
                    new Vector2(1, 1),
                    new Vector2(1, -1),
                    new Vector2(-1, 1),
                    new Vector2(-1, -1)
                };

    private readonly List<Vector2> Manhattan = new List<Vector2>() {
                    new Vector2(1, 0),
                    new Vector2(-1, 0),
                    new Vector2(0, 1),
                    new Vector2(0, -1)
                };
    #endregion
}
```
</div></details>

&nbsp;

<details><summary><b> Click here to expand PathfindingHeuristic.cs </b></summary>

<div markdown="1">
``` c#
/// <summary>
/// The pathfinding heuristic to use when calculating distance.
/// </summary>
public enum PathfindingHeuristic
{
    /// <summary>
    /// Allows movement in the cardinal directions.
    /// </summary>
    Manhattan,
    /// <summary>
    /// Allows movement in both cardinal and diagonal directions.
    /// </summary>
    Chebyshev
}
```
</div></details>
&nbsp;

<center><video src="https://user-images.githubusercontent.com/69112024/191861894-ecce2ee5-6163-455c-ac71-4c63a6b5778d.mp4" controls="controls" style="max-width: 1050px;">
</video></center>
&nbsp;

# Loot System

In *The Catacombs*, I created a loot system allowing the player to find and equip new gear as they explore the dungeon.

<center><video src="https://user-images.githubusercontent.com/69112024/288801611-6a5b33bf-2190-4d9e-b169-cb8eddb11378.mp4" controls="controls" style="max-width: 1050px;">
</video></center>
&nbsp;

The video shows the character *Mitzi* finishing off an enemy - you can see the attack radius of the unit is set to a 3x3 square. After clearing the round, the player chooses to take a new item - the Tome of Terror!
After equipping the Tome, the new stats afforded by the item are applied and *Mitzi's* range of attack is increased dramatically, but deals slightly less damage.

<p align="center">
  <img src="https://user-images.githubusercontent.com/69112024/288807337-504bc880-89db-4756-832e-512d49db6212.png"/>
</p>

The loot system is designed in a way that makes it incredibly easy to add new equipment to the game. A `LootGenerator` is responsible for deciding which gear to provide to the player from the library of available items, and takes into account various factors such as the items the player currently has and how far through the dungeon the player has progressed. The items exist as simple data containers (such as a `BaseWeapon`) which can be easily added to the `LootGenerator`. A designer can easily create a new item, or adjust an existing item, and modify it's properties without diving into the code and changing anything. When the loot is generated during gameplay, those modifications will be immediately present and ready to test, allowing for the rapid creation of extra game content. 

&nbsp;

# Player Controllers

In *Project Whimsy*, I used a finite state machine to compartmentalise movement and actions into individual states. 

<center><video src="https://user-images.githubusercontent.com/69112024/152352692-f6ee8042-9aa2-4a7e-8ee9-fdceab6ab3b8.mp4" controls="controls" style="max-width: 1050px;">
</video></center>

&nbsp;


# Dialogue systems

In *Fallen*, I created my own closed captioning system to display dialogue and provide text descriptions for sound effects. 

<center><video src="
https://user-images.githubusercontent.com/69112024/153856779-6cf8b766-5675-4564-863b-94b6058dc5b9.mp4" controls="controls" style="max-width: 1050px;">
</video></center>

In *Project Whimsy*, I utilised the [Ink narrative scripting language by Inkle](https://www.inklestudios.com/ink/) to display contextual dialogue options and responses. 

<center><video src="https://user-images.githubusercontent.com/69112024/153493429-8adbd973-761a-4d42-88f0-8c7ba92a63dc.mp4" controls="controls" style="max-width: 1050px;">
</video></center>

The Ink script contains all of the game's dialogue alongside the logic which determines which dialogue to display next. The Ink script is exported in **json** and accessed via the [Ink for Unity C# API](https://github.com/inkle/ink-unity-integration), allowing us to easily determine which dialogue to display on screen based upon the players previous choices and progress.

When we speak with a character, our `DialogueManager` defers to that characters `decider` section in the Ink script which contains the logic for deciding which text to display. 

<p align="center">
  <img src="https://user-images.githubusercontent.com/69112024/153217288-eac3fb80-074d-4d8d-9400-c32714c07b57.PNG"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/69112024/153217266-437a4c6e-108e-4e82-9177-7dfa6b3514a6.PNG"/>
</p>

The API returns the appropriate dialogue line (or choices). Our `DialogueManager` sends an event to the dialogue UI and lets the logic play out in the Ink script. Once there is no more dialogue to display, we close the UI and gameplay resumes.

## Animation

In *The Catacombs*, I created, rigged, and animated 2D sprites, making use of Inverse Kinematics to create some "bouncy" animations. These animations were created to suit the "quirky" nature of the cat characters in *The Catacombs*

<center><video src="https://user-images.githubusercontent.com/69112024/191863324-6f73b40a-6e17-47ad-bd7a-745baa6d29b8.mp4" controls="controls" style="max-width: 1050px;">
</video></center>


In *Fallen*, I imported animations from [Mixamo](https://www.mixamo.com) and used an `Animator` to transition between them as appropriate. I also used `Animations` for fading in and out during scene transitions.

<center><video src="https://user-images.githubusercontent.com/69112024/155396952-c836e898-dc3d-484a-a2e4-007956fd6d87.mp4" controls="controls" style="max-width: 1050px;">
</video></center>

Since this was a game jam project, I didn't commit to fully fleshing it out with animations such as rotating in position, moving diagonally, moving backwards and so forth - but it was a good introduction to blending between more complex `Animation` timelines.

I also used `Animators` in *Moonshot* for basic UI effects, such as when picking up collectibles.

<center><video src="https://user-images.githubusercontent.com/69112024/155396943-cff7ed08-8685-4cd4-a857-9af3f7fddcfc.mp4" controls="controls" style="max-width: 1050px;">
</video></center>

# Audio

In *Fallen*, I used a lot of spatial audio to contribute to the overall atmosphere.

<center><video src="https://user-images.githubusercontent.com/69112024/155536810-8cdeb24f-300e-4d5c-a0d2-964b1913174b.mp4" controls="controls" style="max-width: 1050px;">
</video></center>

<center><video src="https://user-images.githubusercontent.com/69112024/155536837-264a2cc7-ef18-4295-9b58-58c113f82d5d.mp4" controls="controls" style="max-width: 1050px;">
</video></center>

I also used various `AudioFilters` to create interesting effects, such as a `Low Pass Filter` to create this muffled-sounding argument.

<center><video src="https://user-images.githubusercontent.com/69112024/155536869-65668379-b58c-42bb-8790-0d12e43c34c9.mp4" controls="controls" style="max-width: 1050px;">
</video></center>

In all of these clips you can see that I tied sound effects to the player's `Animation` timeline to play sounds during walking animations. When the player's foot touches the ground during the `Walking` and `Running` states, an `AnimationEvent` is triggered. A script then checks for which type of ground we're currently walking over (wooden, concrete etc) and plays an appropriate sound effect. There's a good variety of sound effects for each different type of terrain in order to avoid repetition.


# Contact me

You can reach me via email at [ScottJamsDev@gmail.com](ScottJamsDev@gmail.com).

You can view my CV/resumé for further information [here](https://github.com/ScottJams/scottjams.github.io/files/13511751/ScottSimpson_CV_Nov23.pdf).

You can also check out my socials at [Twitter](https://twitter.com/ScottJamsDev) and [Github](https://github.com/ScottJams).