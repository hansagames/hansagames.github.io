---
layout: post
title: HaxeFlixel button
date: '2016-02-21'
tags:
  - Haxe
  - HaxeFlixel
  - button
category: HaxeFlixel
comments: true
---

In this post I will explain how you can build interactive objects in HaxeFlixel.
There are 2 great buttons in HaxeFlixel ([FlxButton][f19f733b] and [FlxUIButton][9a81c39a]), so if you need a standard button you can use them. As I wanted to use a texture from atlas for buttons, I couldn't use them, so I searched for alternative. Thanks to [HaxeFlixel community][f5fb6786] I got some other way to create buttons in HaxeFlixel. You can't add event listeners directly to FlxSprite, but you can use `flixel.plugin.MouseEventManager` for that.

The basic example of using it would be:
{% highlight haxe %}
class ButtonBase extends FlxSprite
{

	public function new(X:Float=0, Y:Float=0, ?SimpleGraphic:Dynamic)
	{
		super(X, Y, SimpleGraphic);
		MouseEventManager.add(this, onDown,onUp, onOver, onOut);
	}

	function onOut(target:FlxSprite)
	{

	}

	function onOver(target:FlxSprite)
	{

	}

	function onUp(target:FlxSprite)
	{

	}

	function onDown(target:FlxSprite)
	{

	}

	override public function destroy():Void
	{
		super.destroy();
		MouseEventManager.remove(this);
	}

}
{% endhighlight %}

You can override these methods for your concrete buttons to react on events. As a big fan of signals I made a button for my project using them.

{% highlight haxe %}
class ButtonBase extends FlxSprite
{
 var dispatcher(default,null):FlxTypedSignal <ButtonEvent,?Dynamic->Void>;

public function new(X:Float=0, Y:Float=0, ?SimpleGraphic:Dynamic)
{
  super(X, Y, SimpleGraphic);
  dispatcher = new FlxTypedSignal <ButtonEvent,?Dynamic->Void>();
  MouseEventManager.add(this, onDown,onUp, onOver, onOut);
}

function onOut(target:FlxSprite)
{
  dispatcher.dispatch(ButtonEvent.OUT, this);
}

function onOver(target:FlxSprite)
{
  dispatcher.dispatch(ButtonEvent.OVER, this);
}

function onUp(target:FlxSprite)
{
  dispatcher.dispatch(ButtonEvent.UP, this);
}

function onDown(target:FlxSprite)
{
  dispatcher.dispatch(ButtonEvent.DOWN, this);
}

override public function destroy():Void
{
  super.destroy();
  MouseEventManager.remove(this);
}

}

enum ButtonEvent
{
  UP;
  DOWN;
  OVER;
  OUT;
}
{% endhighlight %}

Now you can listen to a signal from outside. To do that you need to add a listener for the dispatcher:

{% highlight haxe %}
var btn = new ButtonBase();
    btn.dispatcher.add(onButton);

function onButton(event:ButtonEvent,?target:Dynamic)
{

}
{% endhighlight %}

Also you can make one signal for each event and then listen to it, but I am more used to one listener with event types :)

Happy coding :)

  [f19f733b]: http://api.haxeflixel.com/flixel/ui/FlxButton.html "http://api.haxeflixel.com/flixel/ui/FlxButton.html"
  [9a81c39a]: https://github.com/HaxeFlixel/flixel-ui/blob/dev/flixel/addons/ui/FlxUIButton.hx "https://github.com/HaxeFlixel/flixel-ui/blob/dev/flixel/addons/ui/FlxUIButton.hx"
  [f5fb6786]: http://haxeflixel.com/forum/?place=msg%2Fhaxeflixel%2F5xwsCMtM38c%2FYCeQNOuCBQAJ "FlxButton with texture atlas"
