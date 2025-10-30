---
layout: post
title: "DevTober, The End."
date: 2025-10-30
categories: gamedev neovim rust
---

## What I Built

Going into detail about everything I did in October would take forever, but here are the highlights.

### godot-debug.nvim: The Breakthrough
Around the beginning of the month, I believed (and still believe) I solved an overarching problem in my godot-debug Neovim plugin. The issue was that godot-mono is built in Linux in a Docker container, and its built-in debug symbols point to paths that don't exist unless you have the container. This made debugging completely broken for native Windows users (Linux seems to work fine). Though I haven't touched it since (probably because I'm scared it was a fluke), I feel confident on that front. The fix was just setting the dotnet editor to Visual Studio in Godot. Simple, but finding it was the hard part.

### The Great Boid Experiment
This was the meat of the month. I wrote close to the same boid system in three languages: GDScript (baseline), C# (leveraging Godot-mono), and Rust (using gdext for maximum performance).

I didn't approach this with any grand plan or systematic methodology. Just pure grit and curiosity about how far I could push it. Each time I got one implementation working, I'd think "there's still more to get" and rewrite it in another language. That drive to squeeze out more performance, to see what was possible, kept me going.

I achieved around 10k boids at 60fps on my machine (specs: Ryzen 5 5600G, rx 7700 xt). At that scale, something interesting happened: the bottleneck shifted from compute to rendering. No matter how fast the boid calculations got, the engine's rendering pipeline couldn't keep up. So I rewrote the rendering system to interact with the RenderingServer directly. After that? 100k boids at 23fps, and the bottleneck is still compute! There's probably still more to get.

### Neovim Configuration Rewrite and Plugin Development
I rewrote my nvim config to deviate further from kickstart.nvim. Built out my own plugin management patterns and workflow. This gave me a much better understanding of lazy.nvim and how to structure complex configurations. The rewrite was necessary to support my growing collection of custom plugins and game development workflows.

During this process, I worked on several experimental plugins with Claude. Tab/buffer management experiments that didn't pan out (learned what doesn't work). Some Rust projects including a docs scraper that would have made zettelkasten notes from Jekyll docs using HTML parsing and pandoc for markdown conversion. GdUnit4 integration for running Godot tests from within nvim. Various workflow automation attempts.

Most of these were learning exercises rather than production tools, but they taught me a lot about structuring Lua plugins, handling async operations in Neovim, and the importance of keeping plugin scope focused. Not every experiment needs to ship. Sometimes the value is in understanding why something won't work.

### nav-groups.nvim: The One I'm Proud Of
This is the plugin I'm most excited about. It solves the issue I've had with grapple and harpoon from the beginning of trying to use them in game dev.

The problem: Game development means working on multiple disconnected systems simultaneously. You might be editing an FPS controller (PlayerController.cs + StateMachine.cs + multiple state files), then need to switch context entirely to work on an inventory system (UI + backend + item definitions). Grapple and Harpoon only give you one global list per project.

The solution: nav-groups gives you multiple independent navigation groups that are window-local. Each group (1, 2, 3...) contains its own list of files. Different windows can be in different groups simultaneously. You can have your FPS controller files in one vsplit and inventory system in another. Groups auto-expand as you add files (always maintains one empty group ahead). Visual feedback through statusline integration and a persistent floating window. Full keyboard-driven workflow for adding/removing files and cycling between groups.

Implementation wise, it uses window-local variables to track which group each window belongs to. Persistent floating window with real-time updates (using snacks.nvim or falling back to native). Adaptive sizing based on content. Clean keybind system (`<leader>ga` to add, `<leader>gn/gp` to cycle files, `<leader>g[/]` to cycle groups). No dependencies on project structure or file organization.

Check out the full implementation [here](link to repo).

## What I Learned

### Rust and the Borrow Checker
Learning Rust during a family reunion wasn't the most focused environment, but there's something about sitting in a cabin surrounded by family debugging borrow checker errors that really makes it stick in your brain. The ownership system initially felt like fighting the compiler, but about a week in, something clicked. Rust forces you to be explicit about who owns what data and for how long. It's like the language enforces the single source of truth principle.

The real revelation: The compiler is the best teacher I've ever had. Every error message points you exactly where you went wrong and usually suggests how to fix it. It's like pair programming with someone who never gets tired of your mistakes.

### Performance Discoveries Through Brute Force
Before this month, I would have told you "rewrite it in Rust" for any performance problem. Pushing the boid system to its limits taught me it's more nuanced than that. When you're actually trying to squeeze every last frame out of the system, you start noticing things.

Rust definitely wins in tight loops. The BVH queries and steering calculations got noticeably faster. But the real breakthrough moments came from algorithmic improvements: spatial hashing to reduce neighbor checks, limiting calculations to nearby entities, using MultiMesh for rendering. The language choice mattered, but not as much as I expected until I hit really high boid counts.

The "there's still more to get" mindset led me to try things I wouldn't have in a more planned project. Some worked, some didn't, but each rewrite revealed something new about where the bottlenecks actually were.

### Plugin Development Patterns
Working on nav-groups taught me a lot about structuring Neovim plugins. Window-local variables (`vim.w`) are perfect for context-specific state. Forward declarations in Lua for mutually recursive functions. Autocommands for reactive updates (BufEnter, WinEnter). Graceful degradation (snacks.nvim features when available, fallback otherwise). The importance of good defaults and minimal configuration.

### The Value of Stubbornness
Each of the three boid implementations took 2-3 days. I didn't plan to do a comparative study. I just kept thinking "I bet I can push this further" and rewrote it in a different language. That stubbornness to keep going, to refuse to accept "this is as good as it gets," ended up teaching me more than any systematic approach would have. You learn different things when you're chasing performance out of pure curiosity versus trying to prove a hypothesis.

## Goals

### Short-term (Next Week)
- Benchmark and document the boid implementations properly with graphs
- Publish nav-groups to a GitHub repo with comprehensive documentation
- Revisit godot-debug and confirm the fix wasn't a fluke, add tests
- Write blog posts detailing:
  - The nav-groups architecture and why window-local navigation matters
  - Performance deep-dive on the boid implementations
  - Rust vs C# in Godot: when each matters

### Medium-term (Next Month)
- Port spatial partitioning to Rust properly and measure the FFI overhead
- Integrate nav-groups with session management (vim-obsession or similar)
- Learn more about lifetimes in Rust, still confused when I need `'a` and when I don't
- Explore compute shaders for the boid system (GPU acceleration for 100k+ boids)

### Long-term (This Year)
- Release a small game using the tools I've built this month
- Contribute back to the gdext and godot-rust communities
- Write a comprehensive guide on Neovim for game development
- Keep building small, focused tools that solve real workflow problems
