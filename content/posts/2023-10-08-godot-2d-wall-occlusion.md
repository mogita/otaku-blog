---
date: 2023-10-08T15:06:19+08:00
title: Godot 2D Wall Occlusion
slug: godot-2d-wall-occlusion
category: tech
tags: [godot, devlog]
---

In Godot, to let the player occlude the wall when in the front, and the wall occlude the player when in the back, there are 2 approaches. The built-in and easier one is Y Sort (there used to be a YSort node in Godot 3, now the node is gone and Y Sort became an option for every “CanvasItem” based node). Another approach is by coding a “trapdoor tigger” that makes the wall visible or not.

For both approaches, one would need to clip the “wall” out from the background image and put it on the same position to “fake” a consistent look of the map. While in fact there are two layers to achieve the occlusion effect.

<div style="display: flex; justify-content: space-between;">
    <img style="width: 49%;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/scr-20231008-lmlh.png" />
    <img style="width: 49%;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/scr-20231008-lreb.png" />
</div>

# With Triggers

{{< youtube xhtQhED24MA >}}

Two collision nodes are used. One is the `CollisionShape2D` node that signals the root node to “show” the wall sprite, thus occluding the player standing behind. Another one is the `CollisionPolygon2D` node that signals the root node to “hide” the wall when player is standing in the front.

This approach requires delicate adjustments on the collision nodes to make the final look correct and smooth. But you have total control of how the environments should interact with the character. With large scene set, it could easily go into a mess. Use Y Sort whenever possible.

# With Y Sort

Y Sort is robust and more convenient. It doesn’t require the additional collision nodes. Simply check the `Y Sort Enabled` option on the parent node in Godot 4. In my case, it’s the root node named “Level”. If you're using Godot 3, there’s a node type called [YSort](https://docs.godotengine.org/en/3.5/classes/class_ysort.html).

<div style="display: flex; justify-content: center; align-items: center;">
    <img style="width: 100%; max-width: 500px;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/scr-20231008-mrdf.png" />
</div>

To start with, put all the elements in place, and just enable the “Y Sort Enabled” option in the root node. All of its child nodes inheriting “CanvasItem” are Y Sorted. Meaning when the player sprite moves to a lower position of the wall sprite (with a bigger Y value in position), Y Sort makes the player occlude the wall, and vice-versa. There’s only one catch.

By default, when an image is dragged into a `Sprite2D` node, the origin is at the center of the image. Adjust the “Y” value in the offset, usually one moves the image to the “above” by decreasing Y, or to say moving the origin point to the bottom of the image. Depending on the exact image used in the scene, the origin might need to be set to different position. Then, adjust the player character’s origin too, moving it around the foot of the character’s image. Now Y Sort should work correctly.

<div style="display: flex; justify-content: space-between;">
    <img style="width: 49%;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/origin-at-center.png" />
    <img style="width: 49%;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/origin-at-bottom.png" />
</div>

One thing to notice, the Y Sorted nodes should be on the same Z Index. In my case, the wall and the character are both on Z Index 0, while the backdrop (all of the buildings) is on -1, making it not involved in the Y Sorting among the wall and the character, to always stay behind them both.

The result of using Y Sort:

<video src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/godot-2d-occlusion-with-y-sort.mp4" controls="yes" type="video/mp4" width="100%" />

# Credits

- Inspired by https://www.reddit.com/r/godot/comments/1109shm/ysort_godot_35_vs_4/
- Background Illustration https://headtopics.com/us/photo/waitress-at-kieler-woche-2023-132326
- RPG Character Builder https://store.steampowered.com/app/1154430/RPG_Character_Builder/
