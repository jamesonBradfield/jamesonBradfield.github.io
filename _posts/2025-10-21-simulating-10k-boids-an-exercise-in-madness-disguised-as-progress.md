---
layout: post
title: "Simulating 10k boids: An exercise in insanity"
date: 2025-10-21
categories: "blog"
---
Insanity is doing the same thing over and over and expecting a different result.

I've heard that phrase throughout my life and thought "of course, if something isn't working, you're obviously not trying the right thing." The follow-up thought is always something like "Edison didn't fail to make the lightbulb hundreds of times, he learned hundreds of ways not to make one." That sounds great until you're learning those hundred ways with AI whispering in your ear. It makes you painfully aware of what you don't understand. Even in languages I know well, collaborating with AI feels like pulling teeth. The process that should streamline development often takes longer than just cracking open the docs and figuring it out myself.

I've always had the itch to push a boid simulation to its limits. After writing one in GDScript and being unsatisfied with the performance, I thought I'd push it further. I recently found myself with time and the opportunity to learn Rust, and thought "this beats reading docs any day." What started as a simple learning exercise turned into rebuilding the same simulation across GDScript, C#, and eventually Rust, not because I planned to, but because each implementation taught me where the previous one was hitting walls.

<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden;">
  <iframe style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" src="https://youtu.be/Mp5upiIqIiQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

So what are boids, and why are they so demanding? Boids are an emergent simulation where every entity follows three simple rules: separation (move away from neighbors), alignment (match neighbors' direction), and cohesion (move toward the group's center). These three rules create flocking behavior like a flock of birds or, if you'll forgive my Midwest hunting reference, chuckers taking flight.
The problem? With the naive approach where every boid checks every other boid, you bottom out around 200 entities. That's not a hardware limit; it's a computational wall. At 10,000 boids checking neighbors every frame, you're looking at 100 million distance calculations per second at 60fps.

The solution is spatial partitioning. Think of it like this: if you were in marching band, you'll remember those dot sheets showing where everyone stands on the field. Imagine every band member has to find their dot on a sheet with the entire 200-person band, Now imagine the band director spends a couple nights making individualized sheets showing only your nearby dots and their spacing. That's spatial partitioning: pre-calculating neighborhoods so each boid only searches a small, relevant set instead of the entire swarm.

I'm a firm believer in instinct. I tend to jump into problems and research as I go, exercising my skills as much as I need to. But there's a ledge, a point where my knowledge drops off, where years of programming foundations built over countless projects no longer support the climb. It's at this moment I turn to what I can only describe as one of the greatest technological discoveries of the 21st century: Large Language Models, or LLMs, known more commonly as AI.

Despite its usefulness and technological achievement, I feel a strange sense of madness when using it. Whether it's me refusing to pay for tokens on principle and not having Claude in my editor, or something deeper about the actual process itself, I'm not sure. What I can say is this: if you understand the fundamentals of programming and debugging, AI can make an inexperienced Rust dev with days of experience learn at an exponential rate. But that learning is brutal, and it reminds me of something that happened in marching band.

Trumpet players are known for having massive egos, and I was no exception—yelling at the tubas to play louder when I got called out for being too loud (obviously everyone else was too quiet). I'd always come away from practice with some quote someone wrote down because I was so carefree and confident in my wrongness. One day we were learning our show and our band director gets on the megaphone: "JAMESON, WHY THE HECK AREN'T YOU ON YOUR DOT?" I looked at my sheet. I was on my dot. I was confident in my logic, my math, my eyes. He kept organizing people, moving them around, not realizing he was on the wrong page of the set book. Everyone was confused. It was late, people were ready to go home. So I yelled back: "YOU'RE ON THE WRONG SET, BLANCHARD!" A brief yelling match ensued, more people joined in, and eventually he realized I was right.

Working with AI feels like that, except I'm never quite sure who's holding the wrong set book. That book the director has is invaluable—it contains the entire show, every formation, every transition. But if you aren't constantly checking yourself, if you don't have the confidence (or the ego) to push back when something feels wrong, it can derail you fast. The difference is, in band I knew my dot because I'd marched it. With Rust and AI, I'm still learning what my dots even are. So when the AI confidently tells me I'm wrong, I don't always know if I should trust my instinct or defer to the authority holding the complete reference. Most of the time it works. But that nagging feeling never quite goes away—the sense that maybe we're both on the wrong page, marching confidently toward a formation that doesn't exist.

In one afternoon, I went from 9 FPS with my naive implementation to 116 FPS in Rust by implementing spatial partitioning with BVH trees, single-pass neighbor queries using query_range, and parallel processing with Rayon for steering force calculations. AI helped me navigate Rust's ownership system to build recursive tree structures, showed me how to use into_par_iter() for parallelizing the neighbor search, and caught subtle issues like forgetting to normalize direction vectors. Did I understand every nuance of the borrow checker? No. But I understood the concepts well enough to know when AI was steering me wrong, and that made all the difference.

Whether or not this learning is breadth (learning of something) or depth (learning about something), I don't believe it matters. Having immediate access to even a glorified autocomplete with all the world's knowledge crammed into its brain is a crazy thing.
The Three Implementations
GDScript: [X FPS] - Easy to write, hit the wall fast
C#: [Y FPS] - Comfortable middle ground
Rust: 116 FPS - Worth the learning curve
[Video/Screenshots/Graph comparing all three]
(ADD: Technical breakdown - what made each one fast/slow? BVH implementation differences? Parallel processing? GC pauses?)
What I Actually Learned
Technical stuff:

Spatial partitioning: BVH trees reduce O(n²) to O(n log n)
Parallelization tradeoffs: 10x speedup, but adds complexity
Structure of Arrays optimization: separating position and direction data improves cache performance
Rust ownership: the borrow checker forces you to think about data flow upfront
Single-pass queries: combining neighbor searches across different ranges eliminates redundant traversals

Meta-learning stuff:

Understanding concepts matters more than memorizing syntax
Iterating through implementations teaches you the tradeoffs
AI is a tool—you still need to know what questions to ask
Sometimes the "wrong" way teaches you more than the "right" way
Instinct and fundamentals let you catch AI's mistakes before they become problems

The Numbers
Initial: 9 FPS (unoptimized)
After single BVH query: 14 FPS
After parallelization: 116 FPS
13x improvement in one day
[Link to GitHub repos for all three implementations]
Final Thoughts
Was it cheating? I'm not sure I care anymore. I built something that works, learned concepts that transfer, and have three implementations to prove I understand the tradeoffs. That feels like progress.
