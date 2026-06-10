---
title: "The Temple Is a Feedback Controller"
date: 2026-06-09
number: 1
excerpt: "My economy sim has no unrest meters and no influence points — just grain, silver, and labor. So when I needed an antagonist for the player's market manipulation, I didn't write an AI. I wrote a controller, and gave it a finite actuator."
---

<!-- ==================================================================
     This post is your authoring template. Things to know:

     1. Front matter (above): title, date, number, excerpt. The date in
        the FILENAME must match. Excerpts show on the blog index.

     2. Math: inline math uses single dollars, like $G$ or $p$ — keep
        inline math free of underscores/subscripts. Any equation with
        subscripts or anything fancy goes in a display block wrapped in
        a <div>, like the examples below. The <div> stops the Markdown
        engine from mangling underscores before KaTeX sees them.

     3. GIFs: drop files in /assets/img/ and use the <figure> pattern
        shown at the bottom. Every post should open with one.
     ================================================================== -->

I'm building a strategy game with one rule: no magic numbers. No unrest meter,
no influence points — every quantity is grain, silver, or labor, conserved and
physically located somewhere. The current prototype is three cities, three
estates, and a live grain market. This post is about the moment the design
started fighting back.

## The plant

Each city's grain stock $G$ evolves the way you'd expect from a mass balance:

<div>
$$\dot{G} = H(t) \;-\; c\,N \;-\; \delta G \;-\; B(t) \;-\; X(t)$$
</div>

where $H$ is harvest (seasonal, lumpy), $cN$ is consumption by population $N$,
$\delta$ is spoilage, $B$ is grain diverted to brewing, and $X$ is net exports.
Price emerges from scarcity on a single shared market where the Crown (the
player), the Temple, and the private sector all trade:

<div>
$$p_t \;=\; \bar{p}\left(\frac{D_t}{S_t}\right)^{\varepsilon}$$
</div>

Nothing exotic. The interesting part is who's allowed to push on it.

## The controller

The Temple tries to hold price inside a band around the customary price — sell
from its stores when grain is dear, buy when it's cheap:

<div>
$$u_t \;=\; \begin{cases} -\min\!\big(k\,(p_t - \bar{p}),\; R_t\big) & p_t > 1.3\,\bar{p} \\[4pt] \;\;\;\min\!\big(k\,(\bar{p} - p_t),\; \sigma_t\big) & p_t < 0.7\,\bar{p} \\[4pt] \;0 & \text{otherwise} \end{cases}$$
</div>

That $\min$ with $R_t$ — the Temple's physical reserves — is the whole game.
The stabilizer's control authority isn't a rule; it's a granary. It can defend
the price band exactly as long as it has grain to throw at the problem, and
not one day longer.

## Why this matters for play

The player's signature move in this design is deliberately spiking the grain
price in their own city. The Crown can't tax barter, so forcing transactions at
a dear price is how it pulls silver out of the other estates. With the Temple
in the loop, that move stops being a button and becomes a *campaign*: you can't
break the band until you've drained the actuator. Induce exports. Quietly buy
out the Temple's stores at the customary price. Time the squeeze for the hungry
months after sowing. Saturate the controller, *then* spike.

The antagonist isn't an AI with a hidden aggression stat. It's a feedback loop
with a finite actuator, and beating it feels like outsmarting an institution —
because that's literally what it is.

## The failure mode I'm watching

Three controllers now share one price signal: the Temple's stabilizer, the
player's sell threshold, and the autonomous traders doing arbitrage between
cities. Threshold controllers on a shared signal are a textbook recipe for
limit cycles — and in a game, a price sawtooth doesn't read as "emergent
dynamics," it reads as *broken*. The toolbox is the usual one: hysteresis in
the trigger bands, transaction costs as damping, and reaction delays — which
are also thematically free, because an institution that responds a month late
is both more stable and more exploitable in a way that feels like statecraft.

Next post: the wool chain, and why paying workers in fiber means raising an
army makes your textiles uncompetitive.

<!-- Example figure block — replace with a real GIF of the price chart:

<figure>
  <img src="{{ '/assets/img/price-spike.gif' | relative_url }}"
       alt="Grain price breaking the Temple's stability band after its reserves drain">
  <figcaption>
    Fig. 1 — The Temple defends the ±30% band until its reserves hit zero
    (dashed line), then the engineered spike goes through.
  </figcaption>
</figure>
-->
