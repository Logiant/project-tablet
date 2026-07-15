---
title: "The Silver Must Flow"
date: 2026-07-15
number: 2
---

Resources in Kārum live physically in the world, and merchants are key to moving goods through it.
Each city develops resources based on its environment -- a copper-rich city might export bronze tools, while a city on the plains may specialize into textiles.
In this blog post, we are going to expand on last month's discussion on pricing, and crack open inter-city trade.
We will pick up the internal Three Estates conflict in the near future.

<!-- ============================================================
     Video figure: a central hub with multiple small villages feeding grain into it via trade
     ============================================================ -->
<figure class="panel">
      <video class="media" autoplay loop muted playsinline
           poster="/assets/img/caravan-poster.png">
          <source src="/assets/clips/silver_sink.mp4" type="video/mp4">
      </video>
  <figcaption>
      The player city, Gasur, overproduces high-value goods. Se'um and Kuru produce almost exclusively low-value grain, and their silver reserves drain to zero.
      Kuru is larger, and their drain rate decreases as they ramp up domestic goods production.
  </figcaption>
</figure>

Today we'll take a look at grain and goods, two of the basic commodities used to grow and build.
Like last time, we will consider a base price of $1$ silver for $1$ unit of grain, which feeds a laborer for a full year.
Similarly, it's convenient to define $1$ unit of goods as enough *stuff* (baskets, rope, bricks, pots) to sustain a person for one year; in Kārum I've set this to a base price of $5$ silver.
Finally, since commodities are physically transported on the backs of donkeys, it's necessary to track the weight of everything.
As a first-order approximation, each donkey can carry $75$kg while one unit of grain and goods weigh $480$ kg and $20$ kg each.
We can compute a *value density*, which will come in handy later,
<div>
$$\rho = \frac{\Delta x}{w},$$
</div>
where $\Delta x$ is the price difference; this means $\rho_{grain} = 0.002$ silver/kg and $\rho_{good} = 0.05$ silver/kg.
This means goods are almost $25\times$ more valuable than grain, and a much better use of caravan space.
The actual price difference $\Delta x$ depends on the supply and demand between cities, of course, which the player can influence through policies.


## Buying and Selling

The first challenge to tackle is buy and sell prices.
Recall that price $x$ follows a parabolic model,
<div>
$$ x = b \frac{d}{s} = \frac{\kappa}{s}, $$
</div>
where $b$ is a constant base price, $d$ is the demand, $\kappa$ is a convenient "sensitivity" variable, and $s$ is the current supply.
A naive approach is to multiply the current price $x$ by quantity.
Selling a batch of goods $\Delta s$, then immediately buying it back, yields,
<div>
$$ v = \underbrace{\Delta s \, \frac{\kappa}{s}}_{\text{sell}} - \underbrace{\Delta s \frac{\kappa}{s + \Delta s}}_{\text{buy back}} = \kappa \left(\frac{(\Delta s)^2}{(s + \Delta s)s}\right),  $$
</div>
where $v$ is the generated value, i.e., silver moved into the player's inventory.
Since each term is positive, this moves $v$ additional silver into the player's pocket.
This is a classic "infinite money glitch" that shows up in <a href="https://fable.fandom.com/wiki/Fable_Trading_Guide" target=_blank>Fable</a> and many other classic games.


This would completely break the economy of Kārum, and we need to compute the true price that includes *slippage*.
For a quantity of goods $\Delta s$, we can compute the value in silver $v$ with integration,
<div>
$$ v = \int_{s}^{s+\Delta s} \frac{\kappa}{s}\,ds =  \kappa \big( \ln(s+\Delta s) - \ln(s) \big).$$
</div>
Since $\kappa$ is constant per tick we can pull it out of the integral, and if we buy and sell a bunch of goods in the same tick,
<div>
$$ v = \underbrace{\kappa\big(\ln(s+\Delta s)-\ln(s)\big) }_{\text{sell}} + \underbrace{\kappa\big(\ln(s) - \ln(s+\Delta s)\big)}_{\text{buy back}} = 0.  $$
</div>
Note that the buy back term is negative, so the net change in silver is a positive sell price plus a negative buy price.
The integral automatically handles the sign, and we have patched the infinite money glitch.

## Profitable Trade Routes

Identifying profitable trade routes involves several steps:
<ol>
 <li>Determine profitable imports/exports</li>
 <li> Compute grain required to complete the route</li> 
 <li> Estimate buy/sell profit</li>
 <li>Compare projected profit with expenses</li>
</ol>


Determining profitable imports/exports requires connecting the price integral back to the *value density* of goods.
We can start with the current price difference $\Delta x$ between two locations.
If $\Delta x > 0$ then it may be profitable to export a good, whereas $\Delta x < 0$ means it may be profitable to import a good.
We can find the best routes by computing $\Delta x$ for each commodity, then sorting it.
The first elements will be the best to export, while the last elements will be the best to import.
This completes the first step.

If a route takes $t$ ticks to travel, and grain is consumed at a rate $\alpha$ units per tick, then the trader must buy $\alpha\cdot t$ units of grain. Per leg, this costs
<div>
$$ v_g = \kappa_g\Big( \ln(s_g - \alpha\cdot t) - \ln(s_g) \Big). $$
</div>
The trader has a maximum weight capacity $W$, which leaves
<div>
 $$ W' = W - \alpha\cdot t\cdot w_g $$
</div>
capacity for trade goods, where $w_g$ is the per-unit weight of grain.
If a tick is each day, and a trade caravan eats $3.5$ units of grain per year, then a ballpark estimate is 
<div>
$$\frac{3.5}{365}\,t \text{ units} \cdot 480 \text{ kg/unit} \approx 4.6\, t  \text{ kg/tick}. $$
</div>
With a total capacity of $75$ kg, this sets a hard limit of $16$ days of travel; after which the trader will make no profit, because there is no space in the caravan for goods to sell!

The caravan fills its baggage with $\Delta s$ units of goods to maximize profit,
<div>
$$ v = \underbrace{\kappa_D \Big( \ln(s_D+\Delta s) - \ln(s_D) \Big)}_{\text{sell at destination}} - \underbrace{\kappa_O \Big( \ln(s_O-\Delta s) - \ln(s_O) \Big)}_{\text{buy at origin}}, $$
</div>
where we purchase (subtract) $\Delta s$ at the origin, then sell (add) it at the destination.
The subscripts $_O$ and $_D$ correspond to the origin and destination markets<!--
--><label for="sn-1" class="sidenote-toggle sidenote-number"></label><!--
--><input type="checkbox" id="sn-1" class="sidenote-toggle" /><!--
--><span class="sidenote">The caravan knows $\kappa_O$, $s_O$ because it starts at the origin, $\kappa_D$ and $s_D$ can only be *estimated* by *trade rumors*, which trickle in from neighboring cities.</span>.
We get $\Delta s$ to maximize profit by setting the derivative equal to zero,
<div>
$$ \frac{dv}{d\Delta s} = \frac{\kappa_D}{s_D + \Delta s} - \frac{\kappa_O}{s_O - \Delta s} = 0, $$
</div>
i.e., the caravan will transfer exactly enough goods to equalize the price -- limited by the remaining capacity of the caravan's donkeys and the quantity of the commodity available at the origin.
We can also verify that this is a maximum with the second derivative test, which I will omit here.

For multiple commodities (e.g., grain, goods, and textiles), this becomes a constrained optimization problem, where the objective is to maximize total profit subject to the total weight and capacity of each commodity<!--
--><label for="sn-1" class="sidenote-toggle sidenote-number"></label><!--
--><input type="checkbox" id="sn-1" class="sidenote-toggle" /><!--
--><span class="sidenote">Instead of solving this problem, I've implemented a heuristic: sell the good(s) with the highest *profit density* until it reaches the profit density of the next best good. Repeat until the caravan is full or the optimal quantity of each good is loaded.</span>.
We can compute this for each trade leg, and in Kārum I require the first leg of the trade to achieve a minimum profit threshold.
The optimization is,
<div>
$$ \max_{\Delta s_i} \sum_i v_i \\~\\
\text{subject to:} \\~\\
\sum_i \Delta s_i w_i \leq W', \\~\\
0 \leq \Delta s_i \leq s_O, \quad \forall i,
$$
</div>
where $i$ indexes the commodities and $W'$ is the free weight left after grain rations.
This maximizes the trade value while enforcing weight and capacity as strict constraints.

## Executing Trades

The final step in the trade system is actually executing a trade.
As an example, let's consider a grain-poor city $O$ that exports goods to a grain-rich city $D$.
Further, let's assume a caravan of 5 donkeys is loaded up to its maximum weight of $300$ kg (after accounting for ration weight).
In the Three Estates model, the merchant receives the goods for "free" at $O$, because the merchant is part of the Private market.
This is a bit strange, but it's a quirk of only transferring the movement of items *between estates* in Kārum.

The merchant arrives at $D$, and wants to sell $300$ kg of goods, which comes out to 15 units.
On a good day, this might have a value $v_g = 120$ silver.
Before returning, the merchant purchases $300$ kg of grain, or $0.625$ units, which has an absolute maximum value of $6.25$ silver.
This means the trader will bring a small amount of grain and $113.75$ silver back to $O$.
Conversely, a trader starting at $O$ will need to spend $113.75$ silver at $D$ to fill up with goods.
In either case silver pools at $O$, where the goods with high *value density* are cheap.

The only wrinkle is barter, when the side spending silver comes up short.
Let's say a trader with cargo valued at $v_t$ arrives at the destination $D$, where they want to purchase a desired basket of commodities with value of $v_d$.
There are three cases to consider:
<ol>
<li> $v_t < v_d$: The trader exchanges everything for a fraction of the desired commodity basket.</li>
<li>$v_t = v_d$: The trader exactly exchanges their cargo for the desired commodity basket.</li>
<li>$v_t > v_d$: The trader exchanges just enough of their goods to purchase grain for the return trip and fill their capacity to 100%.</li>
</ol>

We can compute the exact values $v_t$ and $v_d$ using the value integral; this makes it trivial to check which case we're in.
In the first case, we need to invert the integral to determine the quantity $\Delta s$ that has the same value as $v_t$ from the desired goods basket<!--
--><label for="sn-1" class="sidenote-toggle sidenote-number"></label><!--
--><input type="checkbox" id="sn-1" class="sidenote-toggle" /><!--
--><span class="sidenote">I've written this for a single good, but we can use the same heuristic as above for the multi-good version of the problem.</span>,
<div>
$$ v_t = \kappa_d\Big( \ln(s+\Delta s) - \ln (s)  \Big)\\
\quad\quad\to \frac{v_t}{\kappa_d} + \ln(s) = \ln(s+\Delta s)\\
\quad\quad\to \Delta s =\exp{\left(\frac{v_t}{\kappa_d} + \ln(s)\right)} - s.
$$
</div>
This looks complicated, but we can use the natural log and exponential functions built into the <i>mathf</i> library.
So, this is one line of C# code.

The second case is a straightforward inventory exchange, while the third case is the most challenging.
The trader will sell $\Delta_1$ goods such that their value $v_1$ exactly covers their grain rations for the return journey.
This opens up cargo space $\frac{\Delta_1}{\rho_g}$, which the trader can fill with grain by selling a very small portion of goods $\Delta_2$.
This repeats to infinity and gives us a convergent geometric series, however the derivation is complicated when the goods have similar value density.
Instead, if the trader has a very high-value good for sale, such as textiles, we can use a simple approximation:
<ol>
<li>Compute the weight and value of grain required to return home, called $w_1, v_1$.</li>
<li>Sell trade goods of value $v_1$, with weight $w_2$.</li>
<li>Receive grain of weight $w_1 + w_2$, which is an overall loss.</li>
<li>Return to the origin, and mark this route as unprofitable.</li>
</ol>
We can compute each of these steps easily using the formulae above.

## So What?

Deriving economic relations for inter-city trade has given us some interesting gameplay mechanics for free.

First, we've rediscovered the <a href="https://acoup.blog/2022/07/15/collections-logistics-how-did-they-do-it-part-i-the-problem/">Tyranny of the Wagon Equation</a>, where a donkey eating its own cargo has a hard limit on travel time.
This sets a hard limit for trade route length, and naturally makes shorter routes more valuable.
This will be expanded on when we look at trade barges, which can transport huge quantities of goods in one direction -- downstream.
It also makes trade cities viable as intermediaries between larger cities; enabling them to pool neighbors' resources while caravans resupply.

Second, we've worked out that silver is going to accumulate wherever luxury goods are the cheapest.
This is going to be whichever city controls the textile mills, forges, and foundries.
This can make cities rich just from geography: a city with copper deposits can export luxury metalware for a significant profit.

Third, we learned that traders with high value-density goods will avoid cities with low value-density goods and no silver.
So, we can expect one-directional trade from poor farming settlements to luxury-producing cities.
Large cities that produce luxury goods will primarily trade with each other, which is a useful economic shape for gameplay.


For the player, it means there are two economic phases (so far): Early game they need to economically dominate smaller neighbors and turn them into compliant grain sources. 
Late game they need to contend with large neighboring cities and balance cheap imports versus retaining their silver.
The player can influence trade, and profit off it, by setting up tariffs and embargoes on different routes and goods.
This could set the Crown up to make significant profit by skimming off the top of a thriving Private economy.
However, intervening too tightly could make military intervention economically feasible -- loosening a neighbor's grip on trade might be worth an expensive war.
The player's control over tariffs, and the broader geopolitical layers of Kārum, will be the subjects of future devlog.

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

