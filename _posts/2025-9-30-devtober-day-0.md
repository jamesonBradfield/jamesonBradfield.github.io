---
layout: post
title: "Day 0: messing with godot source"
date: 2025-9-30
categories: devtober
---
Today I kicked off by building godot engine from source.
I have been trying to get my plugin godot-debug feature complete for a while now, and have run against a problem of the pre compiled binaries in windows having linux paths in the debugger, and thus my editor (i use neovim btw) was trying to open invalid files when stepping through my code and into godot-monos source.

the accepted usual solutions iirc is that visual studio somehow reads from the dlls themselves, and vscode does some sort of sourceFilemapping wizardry with a extension plugin to handle this, my thought was if I compile the engine I'll get the source code, so I followed the guide here https://docs.godotengine.org/en/4.4/contributing/development/compiling/index.html and got started setting up scons. After that I cloned the godot source, and read through the docs.

the first problem came when I tried to compile with mingw, using `scons mingw=true platform=windows` this failed for one reason or another, but I had visual studio installed and by default scons uses the compiler from that so we tried that `scons platform=windows`, after about thirty minutes and another anime episode down we had a build.

## What I Built
- Godot Engine from source

## What I Learned
mingw is odd, and scons rocks!

## Tomorrow's Goals
- double check this does what I need and we have the whole engine including c# files/tools.
