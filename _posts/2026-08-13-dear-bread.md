---
title: "Dear Bread: Spiking Grain Prices for Profit"
date: 2026-08-23
number: 3
---

This devlog will look inward, and explore some of the city production and inter-estate conflicts that arise in Kārum's early game.
As a reminder, you are playing the Crown, one of the three estates alongside the Temple and Market.
Each estate has a specific role; you are free to cooperate or compete with them, justified by your divine right to rule.
In general:
<ul>
<li>The Crown: Controls an exclusive pool of crown goods; builds architectural wonders; handles all foreign diplomacy.</li>
<li>The Temple: Runs a closed-economy to maintain the temple; stabilizes the price of grain.</li>
<li>The Market: Determines supply/demand/price of commodities; facilitates inter-estate and foreign trade.</li>
</ul>
So, while it's possible to dominate the Temple and Market with a strict command economy, you may end up reigning over a fragile city of mud.

## Dear Bread


Today we'll take a look at production, shortfall, and internal trading of grain and goods, the two fundamental commodities that every city runs on.
We've already covered the math of <a href="https://karum.dev/2026/06/11/grain-prices.html">pricing</a> in a previous devlog, so we won't need many equations.
Figure 1 shows a machiavellian approach to city control that emerges out of Kārum's materialist framework: engineer a famine and set the Crown's reserve grain price high.


<figure class="panel">
      <img src="{{ '/assets/img/dear-bread.png' | relative_url }}"
            alt="Graphs showing silver share skyrocketing as the price of grain increases.">
  <figcaption>
      During a short harvest the Temple sells grain into the market to stabilize the price at 1.3.
      The Crown stabilizes the market when the Temple runs out of grain, which creates an opportunity for silver extraction when the reserve price is high.
  </figcaption>
</figure>


The Temple sells grain into the market at a fixed reserve price of $1.3$ silver.
While this lasts, the Temple will extract a trickle of silver out of the Market.
However, once the Temple runs out of surplus, the Crown will sell grain at a reserve price set by the player.
Figure 1 shows the result of setting this price at $4.5$ silver; both the Temple and Market buy the over-priced grain and silver floods into the treasury.
This is a viable (and historically authentic) strategy to centralized silver, and more interestingly, it emerges organically from the game systems.
Grain shortages are antithetical to success in most strategy games, where the goal is to maximize grain production (and minimize price) for maximum growth and happiness.
The closest I've seen is the <a href="https://vic3.paradoxwikis.com/Corn_Laws">Corn Laws</a> event in Victoria 3, which you can trigger as a one-time event to liberalize your government by spiking the price of food -- and this has been heavily nerfed.

Let's dive into the counter-intuitive strategies, including dear bread, that may look foolhardy to a wise monarch such as yourself.
We'll start by justifying the existence of the private market given the historical taxation method.


## Labor and Corvée

If you've read a recent book on ancient Egypt or the Inka you may be familiar with the concept of <a href="https://en.wikipedia.org/wiki/Corv%C3%A9e">Corvée labor</a>, where a worker pays taxes with physical labor.
In Kārum I've set the corvée rate to $12.5\%$, although this will be a player-driven policy eventually.
This converts to about $46$ days of labor per year, or about $3.5$ days per month.
For simplicity, I'm assuming the crown has access to the average-size labor pool,
<div>
$$ L_c = \frac{\lambda \times L}{100}, $$
</div>
so in a city with $L = 1,000$ laborers and a corvée rate $\lambda = 12.5$, the Crown has access to $L_c = 125$ workers.
Labor allocation is a key Crown policy, and it will be prominent within the city details UI (see Fig. 1; bottom-right).


The Crown's labor allocation must add up to the total Corvée pool $L_c$.
Available labor is a fraction of the total labor pool $L$, which we can write,
<div>
$$L = p - d - L_t - L_m, $$
</div>
where $p$ is the total population and $d$ are dependents (e.g., young children and the elderly).
The $L_t$ and $L_m$ terms are the temple workers and unavailable market laborer, who are tax exempt.
We will cover the Temple in a future devlog; for now, think of $L_t$ as a small pool of self-sustaining workers.

The relevant quantity is the unavailable Market labor $L_m$.
These are private farmers that have been granted land by the Crown (a player decision) and are exempt from tax.
While this seems like bad business, the player actually has an economic incentive to grant these private farms.


### Why Privatize Farmland?

At first glance, privatizing farm land seems counter-productive.
Why would a monarch decrease their control of the food supply and empower potential rivals with rich land?
This was a factor in the fall of <a href="https://en.wikipedia.org/wiki/Coptos_Decrees#Political_implications">Old Kingdom of Egypt</a>, so it's obviously dangerous.
There must be some benefit that's worth the risk.


Consider a fixed labor pool of $L$ workers, and a corvée rate of $\lambda$.
If the crown assigns $1$ laborer to farming, the remaining available labor is,
<div>
$$ L_c = \lambda L - 1. $$
</div>
Privatizing the land removes the worker from $L$ while producing the same amount of grain,
<div>
$$ L_c' = \lambda(L - 1). $$
</div>
So how much does the Crown's available labor change?
Expanding the difference yields,
<div>
$$ L_c' - L_c = \lambda L - \lambda - \lambda L + 1 = 1 - \lambda. $$
</div>
So, every privatized farmer frees up $(1-\lambda)$ labor for non-farming work<!--
--><label for="sn-1" class="sidenote-toggle sidenote-number"></label><!--
--><input type="checkbox" id="sn-1" class="sidenote-toggle" /><!--
--><span class="sidenote">At $\alpha=0.125$m instead of $8$ farmers working part-time on one field, one farmer works it full-time and is exempt from the corvée -- freeing up the remaining 7 part-time farmers.</span>.
This can be put put towards monument building, war, and other expressions of the Crown's might.
In exchange, the Crown moves part of the grain harvest into the private market -- weakening royal control over the grain supply.


## Resources and Inter-Estate Trade

We've now justified the existence of the private Market beyond gameplay.
Aside from producing grain, the market also produces a majority of "goods" in-game, these are pots, pans, baskets, bricks, etc.
As with grain, the Crown could produce these goods; or they can buy goods from the market and spend more corvée labor on construction and security.
Purchasing construction goods becomes the trickle of silver from the Crown into the Market, which the Crown will need to recoup to avoid becoming insolvent.

In parallel. the temple runs a self-sufficient estate.
In a bad harvest year, they will stabilize the price of grain at $1.3\times$ the base price; this creates a trickle of silver from the Market to the Temple.
So, in the worst case, the Crown pays the Market for goods, the Market pays the Temple for grain, and all of the Crown's silver gets locked up in the Temple.


If the Crown controls most of the grain production, then bad harvest years are an opportunity to liquidate grain stores to extract silver from the Market and Temple.
The process looks like:
<ul>
<li>Just after harvest: price of grain is low, but above the base price of $1$ because of the short <a href="https://karum.dev/2026/06/11/grain-prices.html">supply</a>.</li>
<li>Mid-year: Temple sells its surplus grain at a price of $1.3$ to stabilize the market.</li>
<li>End of year: price of grain is high, the Crown sells their surplus at a price set by the player.</li>
</ul>
Setting the reserve price of grain is just one lever the player has to control the flow of silver between the three Estates.
The early game is a balancing act, where the Crown needs to maximize the useful productivity of the corvée labor pool while remaining solvent.
As the city grows in prosperity, the Crown can focus on bronze and textile production for international trade, while the Temple acts as a stabilizing force within the city.


## So What?


This devlog has been less systems and math heavy, and instead we've explored some of the incentives that arise from these systems.
I did not design the perverse incentive to spike grain prices into the grain, I actually discovered it by accident while play testing.
I think this is a huge benefit of systems-driven design, and ultimately Kārum is going to generate way more interesting scenarios than I could ever make by hand.
I think this is also a good justification for an eventual early access release, since it's going to take a large group of players many hours to find interesting (and potentially broken) strategies.


This also convinces me that the estates system was worth implementing, because it's leading to apparently irrational behavior that we see in the historical record.
I hope that this creates the feeling of playing the Crown in a real economy, and it creates interesting player decisions that go against the incentives of traditional strategy games.
This could also be a great opportunity for achievements that make the player pause and think momentarily.
A popup saying the Market now produces more grain than the crown might be an interesting way to draw the player deeper into the historical fantasy.

A third direction, which I haven't discussed here, is the use of debt as a first-class resource.
This could work domestically, for example, debtors may add additional labor to the Crown or Temple labor pools.
A <a href="https://en.wikipedia.org/wiki/Debt_jubilee">debt jubilee</a> might reduce obligations to the Temple, weakening them temporarily.
Perhaps most interestingly, traders accruing foreign debt might justify an armed intervention.
If your neighbor owes you a small fortune, that may justify the cost of raising an army to recover it by force.


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

