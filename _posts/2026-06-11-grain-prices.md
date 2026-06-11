---
title: "How About the Price of Grain in Mesopotamia?"
date: 2026-06-11
number: 1
excerpt: "Each city produces grain in the Private, Temple, and Crown markets. Grain is produced at the harvest, and trickles down through consumption and waste. This leads to some interesting economic properties."
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

      Examples:

      <div>
      $$\dot{G} = H(t) \;-\; c\,N \;-\; \delta G \;-\; B(t) \;-\; X(t)$$
      </div>

      <figure>
      <img src="{{ '/assets/img/price-spike.gif' | relative_url }}"
            alt="Grain price breaking the Temple's stability band after its reserves drain">
      <figcaption>
         Fig. 1 — The Temple defends the ±30% band until its reserves hit zero
         (dashed line), then the engineered spike goes through.
      </figcaption>
      </figure>

     ================================================================== -->


I am building a strategy game with a materialistic core.
This means no magic numbers; cold, hard economics determine outcomes at every level.
Every fundamental quantity, including grain, silver, and labor, is conserved and physically located in the world.
In this blog post we will explore some interesting behavior that emerges when the Crown and Temple compete for resources in a city.


## The Math of Prices

Let's call the price of grain $x$, in units of *annual consumption per capita*.
This means one laborer will consume one unit of grian every year.
I compute the price of any good using a symmetric hyperbola of supply $s$ and demand $d$,
<div>
$$x = b\,\frac{d}{s},$$
</div>
where $b$ is the *base price* (in this case, $1$), and $d$ is the current demand.
Now, if supply ($s$) equals demand ($d$), we recover the base price ($x=b$); as the supply increases (decreases) the price goes down (up).


## The Price of Grain

Grain is a unique resource, because it's produced all at once (i.e., during harvest) and consumed throughout the year. 
So we don't just want enough grain *now*, we need the stores to last until the end of the year.
Goods are processed in "ticks" throughout the year, so we can compute the demand as a function of the current tick $t$,
<div>
   $$d(t) = \frac{T-t}{T} \, p.$$
</div>
Here $p$ is the population, which consumes 1 grain per year, $T-t$ is the number of *ticks remaining* before harvest, and $T$ is the total number of ticks in a year.
So after harvest ($t=0$) we need enough grain to feed everyone, and just before harvest ($t=T-1$) the demand signal drops to almost zero.

Supply is consumed evenly over $T$ ticks by the population, plus some monthly losses due to spoilage, rodents, etc.
For each tick, the forecasted supply is,
<div>
  $$s(t) = q(t)\,\alpha^{(T-t)}$$
</div>
where $q(t)$ is the quantity of grain and $\alpha$ captures 1% loss per month
<label for="sn-1" class="sidenote-toggle sidenote-number"></label>
<input type="checkbox" id="sn-1" class="sidenote-toggle" />
<span class="sidenote">I compute losses monthly instead of per-tick, but this still captures the behavior on average.</span>.
We can substitute all of these terms into our price formula to compute the price of grain (where $b=1$) at each tick,
<div>
   $$x(t) = \frac{T-t}{T\,q(t)\,\alpha^{(T-t)}} \, p$$.
</div>
This gives us the price for the $q(t)$, the quantity at the current tick.
This is everything we need to compute market prices!

## A Price vs Time Curve

If we want a price curve, we need the dynamics of $q(t)$,
<div>
   $$q(t_{k+1}) = \left(q(t_k) - \frac{p}{T}\right)\alpha.$$
</div>
We can identify the fixed point of this system by substituting a constant $q$,
<div>
    $$q = \left(q - \frac{p}{T}\right)\alpha \implies q = -\frac{\alpha p}{T(1-\alpha)} := -K.$$
</div>
So, $q(t)$ exponentially decays to the fixed point $-K$,
<div>
   $$q(t) = (q_0 + K),\alpha^t - K.$$
</div>
Notice that plugging in $t=0$ recovers $q(t=0)=q_0$ and letting $t\to\infty$ givese $q(t\to\infty) = -K$, as we expect.

Finally, we can compute some useful price quantities.
The final value of $q$ is,
<div>
   $$q(T) = (q_0 + K)\,\alpha^T-K.$$
</div>
The forecasted supply becomes,
<div>
   $$s(t) = q(T) + K(1-\alpha^{T-t}).$$
</div>

Finally, the grain price curve is,
<div>
   $$x(t) = \frac{T-t}{T\Big(q(T) + K(1-\alpha^{(T-t)})\Big)}\,p.$$
</div>
Note that, since $q(t)$ can't go negative, I saturate $q(T)$ at a minimum of $0$, and impose a maximum price bound so that $x(t)$ doesn't shoot to infinity.


## Price Regimes

The price model is simple, and we never need to compute it in the game.
So why derive it?
It turns out the curve has some interesting dynamics that the player can exploit.

Our goal is to take a derivative to see if prices go up or down.
We can compute the time derivative of $x$, $\dot{x}$, with the chain rule,
<div>
  $$\dot{x} = \frac{\dot{d}{s} - d\dot{s}}{s^2}, $$
</div>
where $\dot{d} = -\frac{p}{T}$, and $\dot{s} = K\ln(\alpha)\alpha^{T-t}.
Then this simplifies to,
<div>
  $$\dot{x} = \frac{p}{T} \frac{s + (T-t)K\ln(\alpha)\alpha^{T-t}}{s(t)^2}. $$
</div>


We want to compute when $\dot{x} = 0$, where the price of grain is constant, so we can understand when it will rise or fall.
This is,
<div>
   $$\dot{x} = 0 \to K(-\ln(\alpha))(T-t)\alpha^{T-t} = s(t) = s(T) + K - K\alpha^{T-t}.  $$
</div>
Rearranging yields,
<div>
   $$s(T) = K\left( K \alpha^{T-t}(1 + -\ln(\alpha)(T-t)) - K.  \right)$$
</div>
For $\alpha \approx 1$, the logarithm goes to zero and we get the condition,
<div>
   $$s(T) \approx 0.$$
</div>
So, when $s(T) = 0$ the price is approximately constant--note that it will still increase, since spoilage will decrease supply.

Similarly, if $s(T) < 0$, we get a strictly positive $\dot{x}$ and we expect prices to increase everywhere.
In a bad harvest, the prices start high and continue to climb as more grain is consumed.

Conversely, if $s(T) > 0$, we expect a strictly negative $\dot{x}$ as supply eclipses demand all year.
This means prices will start low and decrease until the harvest.

## So what?

As the Crown, the player can allocate labor to growing crops, among other duties.
This gives them some level of control over $s(T)$, which determines how grain prices play out each year.
Many strategy games encourage the player to sit in the $s(T) >> 0$ regime: produce excess grain, keep the population fed, and export excess grain for silver.
Karum is a game built inside an economic engine, so suppressing grain prices can have unexpected consequences.

Each city in Karum contains the Three Estates: the Crown, controlled by the player, the Temple, and the Private market.
The Temple's is a self-sustaining economic agent, with a duty to stabilize grain prices.
If the player grants farmland to the Private market (a future devlog topic), they can keep production near $s(T)=0$.
This also means the temple will buy low on good years, sell high on bad years, and accumulate silver--possibly eclipsing the wealth of the Crown!

On the other hand, the player might allocate a large number of Private estates, dropping the price.
This will drain the Temple's silver into the Private market, as they buy up under-priced grain to stabilize the price.
This also incentivizes nearby cities to buy up the grain for export, freeing them up for growth, manufacturing, and warfare.
This creates a real risk of economic dominance, where the city remains an agrarian backwater.

There is a third option, which will be the topic of the next devlog.
Spike the price of grain, sell to the Private and Temple estates out of Crown stores, and accumulate all of the silver in the city.


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
