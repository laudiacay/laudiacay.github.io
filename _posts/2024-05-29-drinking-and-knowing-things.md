---
tags:
  - writing
  - decentralized-storage
  - crypto
  - work
  - research
  - talks
  - privacy
title: The Decentralized Cloud Master-Document
collection: posts
layout: post
---
## What is this?
This is a... literature review? Startup guide? Market analysis? Engineering decision documentation? All of the above!

If you, your employer, or one of your portfolio companies is looking into building something in the zone of IPFS, Filecoin, decentralized compute, Arweave, "AWS but decentralized", etc... This document will be extremely helpful for reasoning about your decisions. Let others make the expensive mistakes for you!
## Context

Unfortunately, over the past several years of my short and fragile life, I've become one of the most knowledgeable people on the planet about the design space of decentralized cloud architectures. Why is this unfortunate? It's not the game theory, cryptography, and decentralized systems—that part was fun. The issue is that the go-to-market is just an incredibly fraught situation, about which I've also (significantly more painfully) become a top 10 most knowledgeable person on the planet.

Here, I present a cautionary and informative postmortem for those who may follow in my footsteps, contemplating the creation of a decentralized competitor to AWS, Vercel, or Dropbox. Much of this design space has been tried, and you can read what happened here. But once it's been built, many of these ventures, for various go-to-market reasons that I'll delve into later, may not be advisable. This advice is distilled from my numerous talks and discussions across various online platforms, thoughts I've kept to myself, and other valuable resources around the internet.

Why is this a cautionary postmortem? Banyan still exists! As I'll dive into later, Banyan is no longer attempting to partake in building a decentralized version of AWS or other mature cloud services (compute, data, distribution, permissioning, Google Drive) and go directly to market with it. That is a caveman approach, which I only attempted because I was a junior engineer who only started looking up from my IDE six months after our first funding round. When you do this, you are competing with an incredibly mature industry as a tiny seed-stage startup, you have zero differentiators that matter to any sizable majority of the market, and you are engineering with both hands tied behind your back as you attempt to respect trustlessness and decentralization without sacrificing usability. 

What are we doing now (my investors ask, trembling, sweating bullets)? Jiu-jitsu. We are tearing the tech apart to tastefully apply its real benefits over web2 approaches to areas where it can improve high-assurance, abstracted, local-first/zero-trust software running on unreliable, insecure networks. This shows up in a way people will pay money for in the most extremophile cases of computer networking, where the client-server model is completely falling apart. I'll write more about this soon, but for now, that's all I need to say.
## Why/Scoping?
The topics I will cover here are in three sections: 
1. First, to discuss the full stack of core incentives for basic "computational legos" (store, compute, transmit) that have been invented throughout the industry, and their pros and cons. 
2. Next, to discuss some reliability innovations and places where reliability innovations or "extra layers on the protocol" are sorely needed.
3. Finally, I'll discuss the market prospects for the technology as we've built it, advise against various species of trying to cargo-cult web2 cloud success, and explain why most of our current GTM narratives are simply unrealistic.
# Core Protocol
## Storage: making sure the data stays there
The first component that people have attacked is "ensuring the data is there". This is the core incentive of Filecoin, Sia, Arweave,
- longevity, durability
### The obvious approach: pay as you go
note that this is what DA layers basically do!
#### to PoRep or not to PoRep

#### Addressing the stupid option: PoRep with an extra copy

#### FOAM/random oracles porep (my favorite option lately)

### SPoRA

## CDN and Retrieval: ensuring you can get the data back
note that your DA layers haven't figured this shit out
### Reputation (is informative, isn't in-band)

### pokepokepoke systems like saturn: why this sucks

### slap a contract on it (banyan mode)

### Retriev (with rehypothecation)

### cryptographic retrieval: the a16z protocol

### Tit for Tat (skynet)

### Where does the data need to be? incentivized network topology and load-balancing

### My favorite combo :)

## Computation over the data: verification

### ZK

### optimistic

### just trust me bro (reputational, or slap a contract on it)

# Layers

## Financial instrumentation, commodities over decentralized cloud services?

volatility hedging issues with filecoin and arweave

## Data DAOs...

## Data preparation
data wallets! of various kinds :)
## Job routing, onboarding data, initiating incentives
Spade with intents. spoiler: pricing is hard!
## Read/write oracles (using decentralized cloud on a blockchain)
This was Banyan's original pitch.
## Cybersecurity and Compliance and Privacy: the things you can't prove.
you can prove location but what else

# Market Prospects

## The AI Safety/Sovereignty Case

## Decentralization is not a differentiator!

## Content addressing is barely a differentiator!
- YOU MUST SEPARATE: content addressing (looking up by hash) from 
- what do people want to query by? not a hash.
- blockchains want to query by a hash (MAYBE), but not people, and generally not databases
- hash can be validated after looking up the data by a faster method
- what is content addressing actually doing? abstracting over where things are stored. what do we want when we do a lookup? FAST, exploiting locality properties...
- IPLD: oh my god, a migraine.
	- small blocks are not fitted
	- cid borders should be application dependent
	- long streams are okay
	- file hash doesn't match
	- multiple gets to get a single file, always.
	- could have been good, ended up being bad
- the main place where content addressing is SOMEWHAT useful is for very specific types of blockchain-based programmatic storage, where you need the high security and have massive space/cost-of-writing constraints.
- what can maybe be useful? abstracting over where computation happens, assuming you have privacy...
## Anti-censorship and Privacy: realistically a very small TAM.

## Can abstraction and commoditization be a differentiator?