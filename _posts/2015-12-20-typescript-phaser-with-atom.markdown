---
layout: post
title: "Typescript Phaser with atom"
date: "2015-12-20"
category: Phaser
comments: true
tags: ['typescript','phaser','atom.io']
---

For JavaScript development I use [atom.io](https://atom.io/) and I am very satisfied with it. Starting a  [Phaser](http://phaser.io/) project I decided to use a Typescript for it, as it increases development speed a lot. Luckily there is a Typescript [plug-in](https://atom.io/packages/atom-typescript) for [atom.io](https://atom.io/).

After installing the plug-in you need to configure your project.
You can structure your project as you like, I usually structured my project as follows:
{% highlight javascript %}
source - Typescript files
libs - JavaScript files
assets - game assets
css - CSS files
index.html
{% endhighlight %}
To use the Phaser with the Typescript you need definition files. You can get them [here](https://github.com/photonstorm/phaser/tree/master/typescript). Just download them and put inside the source folder.

After that you can initialize your project. To do it click `crtl/cmd+shift+p` and chose `Typescript: Create Tsconfig.json`. Then you should have the `Tsconfig.json` in your source folder. You can change it to fit your needs. For the full information just look into official [documentation](https://github.com/Microsoft/TypeScript/wiki/Compiler-Options).

For this example I will compile all Typescript files to `game.js` file inside `libs` folder. To do so set `"out": "../libs/game.js"` compiler option.

Now create your project files. When you open one of them, you will see the Typescript toolbar. To build your project just press `F6`.

<b>One side note:</b> you need at least one `ts` file in your path to initialize `Tsconfig.json`.

Hopefully this guide will help you to get up and ready for Typescript development with Phaser.
