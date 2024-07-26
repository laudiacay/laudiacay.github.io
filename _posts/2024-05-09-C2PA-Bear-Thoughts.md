---
layout: post
title: C2PA Bear Thoughts
collection: posts
tags:
  - c2pa
  - provenance
  - decentralized-storage
  - writing
  - work
---
This is a [Twitter thread](https://twitter.com/_laudiacay/status/1788594843360416242) I posted about C2PA, reproduced and edited here for posterity because my Twitter auto-deletes.

Today, TikTok joined the CAI (content authenticity alliance). What does this mean for blockchain people trying to make money from AI safety/data/provenance? Tl;dr, it's not a great situation, but we already sort of knew that.
### Who is the CAI?
For some background: what do you need to know about the CAI? Really, the below screenshot says plenty. It's Adobe-led (which means most creative production), using specifications whose development was incubated by the Linux Foundation (C2PA).![Image](/assets/images/C2PA.png)

Note the market share of social media / content delivery platforms, camera brands (Sony/Canon/Nikon), media and PR institutions (NHK/BBC/Publicis Groupe), large market share of devices and software (Intel/ARM/Google/Microsoft), and more. They're all in the C2PA. Check the [members list](https://c2pa.org/membership/).
### What are they selling?
They're not selling anything, they're just promoting open-source technology. Now let's look at the technology involved here: w3c verified credentials, and chains thereof. C2PA is more or less a format for passing around, creating, and consuming stacks of attestations about content.

If you want to deep-dive, this document is very helpful because it shows "C2PA in context" instead of making you comb through piles of "the first three bytes MAY BE reserved" RFC bullshit: [https://c2pa.org/specifications/specifications/1.2/guidance/Guidance.html…](https://t.co/fIam3rlNkw) 
### Worked C2PA Example
For your ease of mindless scrolling, I've written a worked example through the entire data supply chain, with UX consideration at every step. 

You snap a photo. Your Canon generates a hardware signature using an onboard PUF (physically unforgeable function) or an SGX/TEE with a certificate chain back to the manufacturer. This is encoded into a verifiable credential and added to the attestations.

You transform the photo into a PNG. The app (let's say it's Imagemagick?) either signs some attestation that it performed the transformation in SGX, or generates a ZK proof of the transformation (see this blog post by Dan Boneh for more detail [https://medium.com/@boneh/using-zk-proofs-to-fight-disinformation-17e7d57fe52f…](https://t.co/WoIvUvoKhw)).

You use Adobe Photoshop to add your brother to the image. Adobe summarizes the transformations you performed to get from the PNG to the PNG with your brother, and adds them to the C2PA record when you save the image.

You put the image into a TikTok or share it on Twitter. These interfaces display the C2PA record of your image, probably in a format with highly simplified UX that only reveals the relevant information to the user: this is a real image, with some substantial changes to the content in Photoshop.

Another creator stitches or quote-tweets your image. C2PA credentials may have additions (with stitches) or may just be displayed as a stack of credentials.
### Moat? Noat for you.
Eventually, C2PA could become like HTTPS, where the lock in the browser eventually goes away to be replaced with scary warnings when the proper certifications are not present.

So- this all seems pretty opt-in, which is great for C2PA taking over the world, and less great for moat of a business attempting to make money off of being a C2PA protocol.

It also looks like everyone who matters in terms of relevant market share is in, except maybe Apple who is probably doing some bitchy uncompetitive shenanigans in the background. This is not great for a startup trying to promote a competing standard or introduce a new solution with any sort of moat at all.
### Where can a blockchain fit in?
#### Q1. You mentioned ZK earlier. What about all our ZK tech? 
A1: Nobody in the real world will care about the security difference between ZK and SGX until it's incredibly fast. And probably not even then. SGX is practical and safe enough for now. 

Simple transformations like resizing an image (like that Dan Boneh paper above) are one thing, and ZK proving these is still not at an appropriate speed for these applications. genAI image editing workflows are getting popular, and even if you assume the generative parts of the editing flow are just signed with OpenAI's keys instead of proven, it's still just laughably complex and slow to prove right now. 

Anyway, a few companies here in blockchain land have the runway to maybe survive until ZK GTM is practical (if they're wise with their obscenely large VC rounds), but I'm not entirely sold that ZK will *ever* be practical for this. There is a lower bound on the computational complexity of ZK proving a computation that is strictly greater than the complexity of the origin computation, and image/video editing flows are the meatiest possible compute workloads that ever happen at the edge... so this is one of the last places I'd expect to see ZK market successes.

#### Q2: Timestamping? A blockchain proves that something existed at a point in time. You could sign a hash of the C2PA! And put it on chain so everyone can see everything! 
A2 (bear/devil's advocacy): What normal person doesn't trust an MPC run by Google, Microsoft, a nation-state, and Intel, all signing and broadcasting timestamps? 

A2 (less bear, and what I actually believe): See [https://opentimestamps.org](https://t.co/h49XE9eUJu). This could be sped up over using Bitcoin. Writing a hash to Ethereum costs thousandths of a cent. You'd want to merkleize the hashes to compress them and bring down the cost per hash. Honestly, there might be something here, the amount of content being produced is absolutely staggering. You'd need a shortish block time, but ten seconds would probably be plenty, and you don't need much capacity if you merkleize. Ethereum could handle this. 

However, note the presence of Polygon on the C2PA website. This is CERTAINLY what they're doing... I'm not bullish on a startup for getting this right and pulling the BD off, because you'd need to embed a blockchain client on every device. 

Unfortunately, I think the only place that value will reliably accrue for this one is gas fees, and maybe a sequencer-esque moat for the merkleization (but there could be multiple competing sequencers posting these timestamps to the same contract. so just kidding, no moat here).
#### Q3: Decentralized STORAGE!??!? Our FAVE?!? 
A3: I am sorry to be the bearer of bad news. Timestamping/publishing anything more than the proof of time-locked existence of the hash of (data + certificate chain) is unnecessary. Keeping the certificate chain publicly available is only useful when the data needs to be publicly available. This is a small percentage of all data, and the vast majority of use cases are happy to serve the data and its C2PA log from S3.
#### Q4: Decentralized compute that does verified transformations (like rendering, generation, or transcoding) over the data and perhaps even signs off on it in an MPC context? 
A4: You can spend your one precious life attempting to get PMF with this, but I will not be doing so. Most users' security models will be quite pleased with the semi-centralized guarantees they get with signatures from OpenAI and AWS. The comparable efficiency of getting tasks accomplished in the workplace will be even more delight-inspiring.
#### Q5: Decentralized attestation networks? Like decentralized community notes? 
A5: Again, mostly quite bearish. As you see in the workflow above, most data that needs to be attested is generated as the content is generated or transformed, which means that the user will do it locally. This means there won't be much of a market for decentralized validation- there's not much information for parties outside the supply chain to add, so there's nowhere for a protocol to insert itself. The two slivers of exception might be: 
1. location: absolutely needs an external validator, easy to forge without the right protocol design, is possible to do securely, would need to rely on in-camera SGX + onboard clocks with monotonicity guarantees that only sign at photograph-time. this is the use case of FOAM network, which one of you degenerate token fiend Value Adders should really fund, damn it. 
	- rating: could probably make money with really freaking good GTM 
2. decentralized community notes across the entire internet for attestations that can only be made by a real human: I think one of the media focused or NGO C2PA members is basically guaranteed to regulatory-capture this one. creds to [@mattigags](https://twitter.com/mattigags) though for coming up with it. 
	- rating: only investable if the team has deep state/NGO/media/C2PA/social media connections, which very few of us web3 clowns possess

### Conclusion
Everyone here has done a bunch of yelling about Blockchain And AI!!! AI safety is finally our time to shine! Oh my god!!!!!!! We found a real use case!!!! 

To this I say: Hmm... maybe! If we have good GTM and execute. But... calm down, this is probably not a big enough wave to return your underwater fund. 

Unfortunately: unless your name is Jaynti Kanani, I think the probability that you GMI off of this narrative is vanishingly small. 

Enjoy!