---
layout: post
title: Side zk work
collection: posts
tags: [projects, work, zk]
---
While I was at Zuzalu, I got pretty into writing Rust code for ZK / specifically folding.

I worked with Lev Soukhanov on implementing his "folding endgame" [here](https://github.com/levs57/Moon-Moon). This design allows folded ZK circuit steps to pass verified information around without expensive lookup gates, memory accesses, or adding other state to verify to a ZKVM. You "leak" part of the witness and pass it around, then do a consistency check on the reads and writes from the "leaked witness bits". It's a modification to the Nova codebase.

I worked on and community-managed contributions to a Rust rewrite of Aztec's Barretenberg [here](https://github.com/laudiacay/barustenberg) for a while afterwards.

Also see my post about p1nch.