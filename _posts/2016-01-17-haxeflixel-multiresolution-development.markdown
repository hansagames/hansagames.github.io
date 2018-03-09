---
layout: post
title: HaxeFlixel multi-resolution development
date: '2016-01-17'
tags:
  - Haxe
  - HaxeFlixel
  - multi-resolution
category: HaxeFlixel
comments: true
---

Happy New Year and Christmas to all :) Now its time for the first post in 2016. Today it will be about my approach on multi-resolution development in HaxeFlixel.

Multi resolution support in games is a topic, that comes over and over again, as it heavily depends on a project type and supported platforms. In this post I will show an example, how I do it for my HaxeFlixel games. (In my previous projects I mostly used Starling framework. It has a good guide on multi resolution support [here](http://wiki.starling-framework.org/manual/multi-resolution_development).)

Approach, that I will describe, is designed for a static screen games like Board or Puzzle games, where you don't have scrolling backgrounds.

At the beginning you need to choose the minimum supported size of the game. The minimal size I used is 480px x 320px. All calculations are based on it. After that I made a list of device resolutions to determine needed texture sizes. Based on it you can calculate a layout size. I usually work with a 6x resolution in my layouts and with 5x in my games, as that should be OK also for future devices with bigger screens. (The width of my layout I calculate proportionally from the height, because one needs an extra space for wider screens. So I usually use 2 times of height).

To easer texture scaling I would suggest to use [TexturePacker](https://www.codeandweb.com/texturepacker), as it really helps to organize your textures.

To auto scale your images use these ratios in TexturePacker:

Scale | Ratio
:---- | :----
1x    | 0.17
1.5x  | 0.25
2x    | 0.33
2.5x  | 0.42
3x    | 0.5
3.5x  | 0.58
4x    | 0.67
4.5x  | 0.75
5x    | 0.83

My game sizes are like that (px):

Scale | Game Width | Game Height | Layout Width
:---- | :--------- | :---------- | :-----------
1x    | 480        | 320         | 640
1.5x  | 720        | 480         | 960
2x    | 960        | 640         | 1280
2.5x  | 1200       | 800         | 1600
3x    | 1440       | 960         | 1920
3.5x  | 1680       | 1120        | 2240
4x    | 1920       | 1280        | 2560
4.5x  | 2160       | 1440        | 2880
5x    | 2400       | 1600        | 3200
5.5x  | 2640       | 1760        | 3520
6x    | 2880       | 1920        | 3840

Based on calculations you should make a layout 3840px x 1920px in your favorite design software. Than make guide lines from a center to determine safe zones for your art. For doing that take a Game Width from a table and center it in your layout. Everything outside a safe zone is for wide devices only so most of users will not see it.

When your layouts are ready, export images and use TexturePacker to make atlases.

I divide background images in 2 or more parts and then build them together, scale and center it based on a height of the screen (in the landscape mode).

Now I will show you how I determine an atlas type based on devices info.

The first of all you need to add a game info class that will hold data.

```
package;
import lime.math.Rectangle;
@:final
class GameInfo
{
	public static var SCALE:Float;

	public static var GAME_SIZE:Rectangle; // size of stage
	public static var GAME_REAL_SIZE:Rectangle; // full size that is GAME_SIZE*SCALE
	public static var GAME_STANDARD_SIZE:Rectangle = new Rectangle(0,0,480,320);

	public static var COMPACT_WIDTH:Bool;
	public static var COMPACT_HEIGHT:Bool;

	public static inline var MIN_SCALE:Float = 1;
	public static inline var MAX_SCALE:Float = 5;
}
```

Next you need to gather an information and calculate a scale and a game size. To do it you should use two methods: one for a size based scale and second for a DPI based scale. A width and a height you can get from `Lib.current.stage.stageWidth` and `Lib.current.stage.stageHeight`.

This method calculates atlas scale based on size of the screen:

{% highlight haxe %}
private function getScaleFactor(value:Float, minValue:Float, minScale:Float, maxScale:Float):Float
{
  if (value <= minValue)
    return 1;

  var scale:Float = maxScale;
  var currentValue:Float = value;

  while (scale > minScale)
  {
    currentValue = value / scale;

    if (currentValue >= minValue)
    {
      break;
    }
    else if (scale < minScale || currentValue == minValue)
    {
      break;
    }
    else
    {
      scale -= 0.5;
    }

  }

  return scale;
}
{% endhighlight %}

This method calculates atlas scale based on DPI of the screen:
{% highlight haxe %}
private function getDPIScale(dpi:Float):Float
{
  if (dpi <= 120)
  {
    return 1;
  }
  else if (dpi <= 240)
  {
    return 2;
  }
  else if (dpi <= 480)
  {
    return 3;
  }
  else if (dpi <= 640)
  {
    return 4;
  }

  return 5;
}
{% endhighlight %}

Than you can calculate real scale and sizes based on that info:
{% highlight haxe %}
function initGameSize(w:Float,h:Float):Void
{
  var tempSizeScale:Float = getScaleFactor(h, GameInfo.GAME_STANDARD_SIZE.height, GameInfo.MIN_SCALE, GameInfo.MAX_SCALE);
  var tempDpiScale:Float = getDPIScale(Capabilities.screenDPI);

  GameInfo.SCALE = (tempSizeScale > tempDpiScale) ? tempSizeScale : Math.min(tempSizeScale, tempDpiScale);
  GameInfo.GAME_SIZE = getStageSize(w, h, GameInfo.SCALE);
  GameInfo.GAME_REAL_SIZE = getViewPort(GameInfo.GAME_SIZE, GameInfo.SCALE);

  zoom = 1;
  gameWidth = Std.int(GameInfo.GAME_REAL_SIZE.width);
  gameHeight = Std.int(GameInfo.GAME_REAL_SIZE.height);

}

private function getStageSize(w:Float, h:Float, scale:Float):Rectangle
{
  return new Rectangle(0, 0, Math.floor(w / scale), Math.floor(h / scale));
}

private function getViewPort(stageSize:Rectangle, scale:Float):Rectangle
{
  return new Rectangle(0, 0, stageSize.width * scale, stageSize.height * scale);
}
{% endhighlight %}

Now we can start HaxeFlixel as usual.
{% highlight haxe %}
addChild(new FlxGame(gameWidth, gameHeight, initialState, zoom, framerate, framerate, skipSplash, startFullscreen));
{% endhighlight %}

Now you can use data from `GameInfo` class to place elements dynamically on the stage. For example, I define margins and all other values in scale of 6x (as in the layout) and then divide them with the `SCALE` from the GameInfo to get an actual value. You can also define them in 1x and multiply by `SCALE`.

That concludes my first topic in `Starling to HaxeFlixel` series.
I hope it will be helpful for you. See you soon.
