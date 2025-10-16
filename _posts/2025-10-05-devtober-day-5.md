---
layout: post
title: "Day 5: Timeskip"
date: 2025-10-05
categories: devtober
---
I have forgot to document the last 3 days, a couple of which were spent trying to get documentation working in neovim with gdscript, I believe I am near the tail end of that problem, and thought I would catch you up on the changes to my project.
I had claude help me rewrite my project to use data oriented principles and installed a unit testing framework(gdunit4). Instead of laying out the changes here, I will instead give a brief overview of why, and the thought process behind the change.

Usually when working in godot my first instinct is to build atomic systems that do one or two things to my code and pass the data along via signals (kinda like a chain) this has the benefit of rapid iteration at the cost of complexity/technical debt. To avoid this since I was already using a multimesh and experimented with godots PhysicsServer two days ago, I thought I could write the whole thing with just one DataHandler node, defining our functions as "black boxes" that take in data and return some data. IE every function is data agnostic!

this approach not only avoids scene clutter for the massive undertaking of rendering a bunch boids in godot, but also lets me focus on lifecycle.

what I settled on was this layout
 DataHandler - Node3D
  PhysicsHandler (custom physics layer)
  MeshHandler (mesh initialization script using godots RenderServer)
  UIHandler (Handles updating/meshing any ui elements I have setup for interfacing with systems)
  BoidHandler (Currently handles boid behaviors (separation,cohesion,alignment), and neighbor finding) I'd like to setup a simple spatial grid that splits simulation space based on boid number, or a octTree(have one written just gotta find it).

I wrote some unit tests for my physicsHandler just to test/benchmark godot's random generation system, I haven't noticed it as a bottleneck, but I wanted to give myself a win in the unit testing department.



## What I Built

## What I Learned

## Tomorrow's Goals
I've wrote this system before in C#, and already love the performance I am getting with gdscript, it really isn't much different performance wise.


