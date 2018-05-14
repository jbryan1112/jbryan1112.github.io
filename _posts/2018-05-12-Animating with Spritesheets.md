---
layout: page
title:  "Animating with Spritesheets"
date:   2018-05-12 01:01:01 -0600
categories: Animation
---
<style>
.monster {
  margin: 0 auto;
  width: 70px;
  height: 94px;
  background: url('/assets/img/bluemansprite.png') left center;
  animation: play 0.8s steps(6) infinite;
}
@keyframes play {
 100% { background-position: -429px; }
}
img {margin: 0 auto;}

</style>

A spritesheet is a bitmap image file that contains several smaller graphics in a tiled grid arrangement. By Compiling several graphics into a single file, you are able to animate graphics while only needing to load a single file.

<img src="/assets/img/bluemansprite.png">

There is a timing function in CSS animations that allows developers to break an animation into steps. We are able to display each sprite image as a frame without any code to help with transitioning.

We are able to control the rendering count in an animation using steps(). This powerful function allows the progressment of animation in steps based on the values we set.

By using illustrator artboards and taking advange of spriting software such as Compass to quickly generate spritesheets containing all exported images.

To animate the sprite, we can define the dimensions of the DOM element and set the sprite as a background image.
```
.yourDomElement {
  width: 300px;
  height: 800px;
  background: url('your-sprite.png') left center;
}
```

NExt, we can create a keyframe rule that animates the position of the spritesheet. For example, if the total width of the sprite is 2000px, we can set the final background position of -2000px.
```
@keyframes play {
   100% { background-position: -1900px; }
}
```

We are almost done. At this point, we can create a @keyframe property of CSS to bind the animation sequence to .yourDomElement with a duration - we will call the @keyframe play using 0.8 seconds per frame. We will also need to include the steps() timing funciton in the animation value; the steps() function depends on the number of steps in your spritesheet. If your spritesheet contains 7 images, we can say that it is made up of 7 frames.
```
.yourDomElement {
  ...
  animation: play 0.8s steps(7);
}
```

Now, the animation will run 7 frames in its 0.8 second duration.

<div class="monster">
</div>
