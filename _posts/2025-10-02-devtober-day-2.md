---
layout: post
title: "Day 2: Spawning a bunch of cubes"
date: 2025-10-02
categories: devtober
---

Today I kicked off devtober by rebuilding mono, and deciding that it was too much work after it complained about uncompiled assemblies,

so I went to work on a boid system, or more generally a responsive mesh spawning system where I could see changes in editor.

I started off with a simple script for spawning below, and realized that for my use case MestInstance3D nodes would be too expensive!
```gd
@tool
extends Node3D

@export_range(0, 99999, 1) var count: int:
	set(value):
		update_count_dynamically(value)
		count = value
	get:
		return count
@export var bounds: Vector3
@export var mesh: Mesh


func update_count_dynamically(target_value: int):
	var current_child_count: int = get_child_count()
	var delta = target_value - current_child_count
	if target_value != current_child_count:
		if target_value < current_child_count:
			for i in range(current_child_count + delta, current_child_count):
				get_child(i).queue_free()
		else:
			for i in range(current_child_count, current_child_count + delta):
				var mesh_instance: MeshInstance3D = MeshInstance3D.new()
				mesh_instance.mesh = self.mesh
				mesh_instance.name = "boid " + str(i)
				mesh_instance.position = random_vector(-bounds, bounds)
				add_child(mesh_instance)
				mesh_instance.owner = get_tree().edited_scene_root


func random_vector(min: Vector3, max: Vector3) -> Vector3:
	var random_X = randf_range(min.x, max.x)
	var random_Y = randf_range(min.y, max.y)
	var random_Z = randf_range(min.z, max.z)
	return Vector3(random_X, random_Y, random_Z)
```

I could get about 5000 cubes on screen before we started to chug.

so I looked into MultiMeshInstance3D nodes, after a bit of fiddling and the below code, we can now max out our cap at 99,999 cubes in space, with minimal reload time (basically seamless with small stutters when you change the spawn size).

```gd
@tool
extends MultiMeshInstance3D

@export_range(0, 99999) var instance_count: int = 0:
	set = set_instance_count,
	get = get_instance_count

@export_range(0, 99999) var visible_instance_count: int = 0:
	set = set_visible_instance_count,
	get = get_visible_instance_count

@export_range(0, 300) var spawn_x_bounds: float = 0:
	set = set_spawn_x_bounds,
	get = get_spawn_x_bounds

@export_range(0, 300) var spawn_y_bounds: float = 0:
	set = set_spawn_y_bounds,
	get = get_spawn_y_bounds

@export_range(0, 300) var spawn_z_bounds: float = 0:
	set = set_spawn_z_bounds,
	get = get_spawn_z_bounds


func set_spawn_x_bounds(new_value: float) -> void:
	spawn_x_bounds = new_value
	randomize_instance_positions()


func set_spawn_y_bounds(new_value: float) -> void:
	spawn_y_bounds = new_value
	randomize_instance_positions()


func set_spawn_z_bounds(new_value: float) -> void:
	spawn_z_bounds = new_value
	randomize_instance_positions()


func set_instance_count(new_value: int) -> void:
	multimesh.instance_count = new_value
	instance_count = new_value


func set_visible_instance_count(new_value: int) -> void:
	multimesh.visible_instance_count = new_value
	visible_instance_count = new_value
	randomize_instance_positions()


func get_spawn_x_bounds() -> float:
	return spawn_x_bounds


func get_spawn_y_bounds() -> float:
	return spawn_y_bounds


func get_spawn_z_bounds() -> float:
	return spawn_z_bounds


func get_instance_count() -> int:
	return instance_count


func get_visible_instance_count() -> int:
	return visible_instance_count


func randomize_instance_positions():
	for i in range(visible_instance_count):
		var _position = Transform3D()
		_position = _position.translated(
			Vector3(
				randf() * spawn_x_bounds - (spawn_x_bounds / 2),
				randf() * spawn_y_bounds - (spawn_y_bounds / 2),
				randf() * spawn_z_bounds - (spawn_z_bounds / 2)
			)
		)
		multimesh.set_instance_transform(i, _position)
```

after all that I realized I had been working for about 3 hours straight (crazy I know but I'm not used to gdscript, and it seems it actually is pretty performant at least right now).



## What I Built
- responsive cube spawning in editor(up to 99,999 on my machine).

- documented the today taking a break at 12:18 AM

## What I Learned
gdscript is really performant, like others have said most performant code is just wrappers for c functions, this will work great for me! at the moment, I would like to think on collision, I was starting off with a boids system thinking I could create a rule for player following, but after seeing a megabonk devlog, and seeing how simple vedinad (most likely dani from youtube) made his, I believe for now that's the way to go.

I am not going for crazy flocking behavior, just a mindless horde,
some things I can look into for solving collisions. are baking a navmesh and looking into creating a "leader" navmesh agent that each "cube"/enemy flocks around, then we can look into spatial partitioning of some sort for detecting collisions,raycasting etc...

this worked well with boids, and I believe a simple boid system could work for this as well, but so could particles.

TLDR kinda overwhelmed with next steps I think I need to solidify my goals for this month and think about what I want to achieve.

## Tomorrow's Goals
- I'd like to implement some performant physics layer that actually handles moving my cubes, ideally we implement the following.
- update staggering (we stagger enemy/cube updates by a frame processing maybe 5 at a time).
- pathfind at an interval (if we want to get a bunch of enemies pathfinding towards the player we just need to update a batch every X seconds) where X is some time. (we can also update the ones closer to the player quicker).
- these might need some sort of partitioning code, a Level Grid would be easiest, with a bvh for finer partitioning?

