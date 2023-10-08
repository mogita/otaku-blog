---
date: 2023-10-08T15:06:19+08:00
title: Godot 2D Wall Occlusion
slug: godot-2d-wall-occlusion
category: tech
tags: [godot, devlog]
---

In Godot, to let the player occlude the wall when in the front and the wall block the player when in the back, there are 2 approaches. The built-in and easier way is Y Sort. Another approach is coding a "trapdoor trigger" that makes the wall visible.

For both approaches, one would need to clip the "wall" out from the background image and put it in the same position to "fake" a consistent look of the map. The bottom line is there are two layers to achieve the occlusion effect.

<div style="display: flex; justify-content: space-between;">
    <img style="width: 49%;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/scr-20231008-lmlh.png" />
    <img style="width: 49%;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/scr-20231008-lreb.png" />
</div>

# With Triggers

{{< youtube xhtQhED24MA >}}

Two collision nodes are used. One is the `CollisionShape2D` node that signals the root node to "show" the wall sprite, thus impeding the player standing behind. Another one is the `CollisionPolygon2D` node, which signals the root node to "hide" the wall when the player is standing in the front.

This approach requires delicate adjustments on the collision nodes to make the final look correct and smooth. But you have total control of how the environments should interact with the character. With a large scene set, it could easily go into a mess. Use Y Sort whenever possible.

# With Y Sort

Y Sort is robust and more convenient. It doesn't require additional collision nodes. In Godot 4, check the Y Sort Enabled option on the parent node. In Godot 3, there's a node type called [YSort](https://docs.godotengine.org/en/3.5/classes/class_ysort.html). In my case, I have enabled Y Sort by checking the box on the root node named "Level."

<div style="display: flex; justify-content: center; align-items: center;">
    <img style="width: 100%; max-width: 500px;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/scr-20231008-mrdf.png" />
</div>

To start with, put all the elements in place and enable the "Y Sort Enabled" option in the root node. All of its child nodes inheriting `CamvasItem` are Y Sorted. This means when the player sprite moves to a lower position than the wall sprite (with a larger "Y" value on the axis), Y Sort makes the player occlude the wall, and vice-versa. There's only one catch.

By default, the origin is at the center when an image is dragged into a `Sprite2D` node. Adjust the "Y" value in the offset. Usually, one moves the image to the "above" by decreasing "Y" or moving the origin point to the bottom. Depending on the exact image used in the scene, the origin might need to be set to a different position. Then, adjust the player character's origin, placing it around the foot of the character's image. Now, Y Sort should function properly.

<div style="display: flex; justify-content: space-between;">
    <img style="width: 49%;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/origin-at-center.png" />
    <img style="width: 49%;" src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/origin-at-bottom.png" />
</div>

One thing to notice is that the Y Sorted nodes should be on the same Z Index. In my case, the wall and the character are both on Z Index 0, while the backdrop (all buildings) is on -1, making it not involved in the Y Sorting among the wall and the character to always stay behind them both.

The result of using Y Sort:

<video src="https://static.mogita.com/blog/assets/2023-10-08-godot-2d-wall-occlusion/godot-2d-occlusion-with-y-sort.mp4" controls="yes" type="video/mp4" width="100%"></video>

# Credits

- Inspired by https://www.reddit.com/r/godot/comments/1109shm/ysort_godot_35_vs_4/
- Background Illustration https://headtopics.com/us/photo/waitress-at-kieler-woche-2023-132326
- RPG Character Builder https://store.steampowered.com/app/1154430/RPG_Character_Builder/
