---
layout: post
title: "HaxeFlixel Signals"
date: "2015-12-17"
category: HaxeFlixel
comments: true
tags: [Haxe, HaxeFlixel]
---

I really like the signal [API][761cae1b] from AS3 and use it all the time. I even ported it to Swift, JS and Dart in order to apply it in other environments. To use signals in HaxeFlixel you just need to define an object, that will represents your signal, and its callback type. So, for example, if you want to make a signal, that will need one String, you have to create a signal like that:
{% highlight haxe %}
var signal = new FlxTypedSignal<String->Void>();
{% endhighlight %}

To use it you need to call `dispatch` method:
{% highlight haxe %}
signal.dispatch("Hi");
{% endhighlight %}
In order to receive this message you need to add a listener (callback). Signal's listener is a function that will be triggered, when method `dispatch` is called. Signature of a callback and a signal's signature must be equal. So if you created a signal, that sends 1 String, you need a callback, that will accept 1 String.
A callback for the previous signal will be:
{% highlight haxe %}
function someListener(id:String):Void{}
{% endhighlight %}
There are 2 possibilities to add a listener to a signal. You can attach it permanently using code
{% highlight haxe %}
signal.add(someListener);
{% endhighlight %}
or you can add it only for a one trigger using a function
{% highlight haxe %}
signal.addOnce(someListener);
{% endhighlight %}
Method `addOnce` is a shortcut for the removing a listener after the first received message.

To remove a listener from a signal you need to use
{% highlight haxe %}
signal.remove(someListener);
{% endhighlight %}
Alternatively you can remove all listeners from a signal by calling method `removeAll()`.

In order to add more parameters to your signal, you can add them in the signature. For example, this signal will send a String and an object of type Dynamic
{% highlight haxe %}
var signal = new FlxTypedSignal<String->Dynamic->Void>();
{% endhighlight %}

You can also create a signal without parameters by using shortcut class:
{% highlight haxe %}
var signal = new FlxSignal();
{% endhighlight %}
It works exactly like FlxTypedSignal, just without any parameters.

As all other HaxeFlixel objects you can destroy a signal using
{% highlight haxe %}
signal = FlxDestroyUtil.destroy(signal);
{% endhighlight %}
if it is not needed anymore.

<b>P.S</b> At the moment (Version 3.3.12) `FlxDestroyUtil.destroy` doesn't work on signals (see more [here][a72d4395]), also a manual way doesn't work
{% highlight haxe %}
if(signal!=null)
{
  signal.destroy();
  signal=null;
}
{% endhighlight %}
so you have to remove your listeners using a method `removeAll()`, if you don't need them anymore.

More information about HaxeFlixel Signals you can find on [Cheat Sheet][f8d83d90], [documentation][37e33b01] or in [source][d072e893].

One side note, if you are new to Signals. Compared to events, signals are dispatched synchrony, that means your callbacks will be trigged instantly after `dispatch()` is called. Take it into account, as you can get some very wild behaviors, if you use signals improperly.

Happy coding :)

  [761cae1b]: https://github.com/robertpenner/as3-signals "https://github.com/robertpenner/as3-signals"
  [a72d4395]: https://github.com/HaxeFoundation/haxe/issues/4237 "https://github.com/HaxeFoundation/haxe/issues/4237"
  [f8d83d90]: http://haxeflixel.com/documentation/cheat-sheet/ "HaxeFlixel - Cheat Sheet"
  [37e33b01]: http://api.haxeflixel.com/flixel/util/FlxTypedSignal.html "FlxSignal Documentation"
  [d072e893]: https://github.com/HaxeFlixel/flixel/blob/dev/flixel/util/FlxSignal.hx "FlxSignal source"
