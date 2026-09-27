---
title: "Our Age of Bronze is Collapsing"
date: 2026-09-27
number: 4
---

This devlog explore trade between cities, and how resource nodes and trade route topology can lead to different city specializations.
The Crown can't control whether raw copper deposits exist, and we've already covered how caravans transport goods with <a href="https://karum.dev/2026/06/11/grain-prices.html">high value density</a> automatically -- and how producers of these goods accumulate silver.
As the Crown, your main influence will be through trade network topology and policy decisions.
Recall, each of these are effective because caravans must eat the grain that they are carrying,
<ul>
<li>Reduce the food cost of a trade route by building way stations.</li>
<li>Improve the speed of a trade route by improving roads.</li>
<li>Collect tolls and tariffs on a trade route.</li>
<li>Embargo certain goods/neighbors/routes to stop the flow entirely.</li>
<li>Subsidize grain in winter months to ensure caravans continue trading.</li>
</ul>
Guiding the flow of goods will enable you to strengthen your allies, weaken your enemies, and subdue your neighbors.


## Trade in the Bronze Age

The title of this post is a reference to one of the less subtle lines in Nolan's The Odyssey, referencing the <a href="https://en.wikipedia.org/wiki/Late_Bronze_Age_collapse">Bronze Age Collapse</a>.
This was a time where long-distance trade routes connecting civilizations disappeared, with catastrophic results that can be seen in the archaeological record.
The Trojan Wars are hypothesized to have taken place around this time, with some circumstantial evidence found in the <a href="https://en.wikipedia.org/wiki/Historicity_of_the_Iliad#The_Iliad_as_partly_historical">archaeological record of Troy</a>.
It's surprising to learn just how interconnected and trade-savvy the ancient near-east was, and Eric H. Cline's "<a href="https://www.goodreads.com/book/show/18730589">1177 B.C.</a>" is an incredible read on the topic, and one of the major inspirations for Kārum.
Surviving a collapse is a <a href="https://totalwar.fandom.com/wiki/Realm_Divide">divisive</a> <a href="https://ck2.paradoxwikis.com/Sunset_Invasion">game</a> <a href="https://wiki.twcenter.net/index.php?title=Total_War:_Pharaoh_Sea_Peoples_invasions">mechanic</a> in many strategy games, but the Bronze Age also offers a lot of unique flavor and interesting ideas that I think will be both fun and compelling.


One of the great Assyrian kings <a href="https://en.wikipedia.org/wiki/Tiglath-Pileser_I">Tiglath Pileser I</a> ruled around this time, and the Assyrians are a good mental model for the economy of Kārum.
It turns out the Assyrians have a long history of trade, going back at least to the historical Kārum of <a href="https://en.wikipedia.org/wiki/K%C3%BCltepe#K%C4%81rum_Kane%C5%A1">Kanesh</a> almost one thousand years earlier.
The book
<a href="https://www.goodreads.com/en/book/show/61918856-assyria">Assyria</a> by Eckart Frahm has a great description of Assyrian's long history of international trade, and documents their advantageous positioning as a trade hub.

This has been a long tangent on the history of deep trade networks during the Bronze Age, and I think Nolan's Odyssey has a much more fitting quote that captures the spirit of the game: "_This is Agamemnon’s excuse to break Troy’s control of the trading routes. He won’t let it pass. Ever._"

## The Testbed

Kārum is a systems-driven game, the best way to tune the system's behavior is to run many simulations and measure how the world evolves.
This was also how the <a href="https://www.indiedb.com/news/pine-february-recap-a-tapestry-of-growth">Pine</a> developers were able to balance their complex ambient simulation.
For this blog post we will look at a hub-and-spoke network, and we will see how wealth and goods flow through the central city Anar to the peripheries.
This requires some basic AI and a logging system to capture transactions, which are good topics for a future devlog.

<figure class="panel">
      <img src="{{ '/assets/img/star-network.png' | relative_url }}"
            alt="Star graph trade network.">
  <figcaption>
      A hub-and-spoke network, where Gasur, Annaka, and Sipir produce unique wool, tin, and copper that propagate through trade.
  </figcaption>
</figure>


So far, we have three categories of resources: 
<ul>
<li> Created resources, pulled out of the ground: </li>
<ul>
<li> Grain (farms) </li>
<li> Goods (workshops) </li>
<li> Copper (mines) </li>
</ul>
<li> Imported resources, purchased with barter: </li>
<ul>
<li> Wool (from nomads) </li>
<li> Tin (from foreign traders) </li>
</ul>
<li> Manufactured resources, produced in a city: </li>
<ul>
<li> Textiles (from wool) </li>
<li> Bronze (from tin and copper) </li>
</ul>
</ul>

Created and manufactured resources are produced directly from labor, where anything manufactured also consumes an input.
Imported items, on the other hand, are essentially sold into the market at their base price using <a href="https://karum.dev/2026/06/11/grain-prices.html">barter rules</a>.


## Initial Conditions and Expectations

For this devlog, I've configured the sim to try and identify the emergence flow of goods through the network.
To start, each city is configured to be self-sufficient with food and goods production, Gasur has wool import buildings, Sipir copper mines, and Annaka a tin port.
To make things interesting, I've placed a weaver in Gasur that converts wool into textiles, and a forge in Anar to convert tin and copper into bronze.
We should expect to see wool and textiles flowing out of Gasur and into the network, and hopefully copper and tin flowing through Anar where copper is produced.

## Plots/Results/Analysis

To enable simulation debugging, I've added a tool that prints out the history of each city as a timeseries.
This will allow us to open up the simulation and understand why it behaves a certain way.
We can start with the wool and textiles timeseries in Figs. 2 and 3.


<figure class="panel">
      <img src="{{ '/assets/img/plots/wool.png' | relative_url }}"
            alt="Wool timeseries.">
  <figcaption>
      Wool stocks over time in each city.
  </figcaption>
</figure>
<figure class="panel">
      <img src="{{ '/assets/img/plots/textiles.png' | relative_url }}"
            alt="Textiles timeseries.">
  <figcaption>
      Textile stocks over time in each city.
  </figcaption>
</figure>

Figure 2 shows the stocks of wool in each of the 5 cities.
You can see that Gasur, the wool importer, immediately purchases 0.5 units of wool on the first tick, and maintains a stock of 2-3 units throughout the simulation.
A similar pattern emerges with Textiles in Fig. 3, with an interesting dip and recovery around tick 250 (~year 10).

Both of the timeseries are spiky due to caravans moving commodities between cities.
We can see that Anar is highly successful at this, with wool stocks exceeding Gasur around tick 250 and remaining high throughout.
The orange, red, and purple curves show that wool and textiles are passing through Anar to reach the other hub cities.
Thus, we expect Anar to gain a lot of wealth by importing relatively cheap items from Gasur and exporting them across the network.


<figure class="panel">
      <img src="{{ '/assets/img/plots/goods.png' | relative_url }}"
            alt="Goods timeseries.">
  <figcaption>
      Goods stocks over time in each city.
  </figcaption>
</figure>

The goods plot in Fig. 4 shows interesting dynamics in goods production and transfer.
Population, construction sites, and constructed buildings all consume goods each tick.
We can see Gasur and Annaka, the wool and tin producers, seem to specialize away from goods production.
At the same time, the hub Anar sees almost an order of magnitude more goods pass through it.



<figure class="panel">
      <img src="{{ '/assets/img/plots/bronze.png' | relative_url }}"
            alt="Bronze">
  <figcaption>
      Bronze stocks over time in each city.
  </figcaption>
</figure>

Bronze is the final resource we will take a look at in Fig. 5.
It takes 200 ticks, almost 10 years, for Bronze production to spin up.
This is because bronze requires tin and copper to produce, which means Annaka and Sipir need to over-produce enough that Anar is able to import it.
Bronze production increases the demand for tin and copper; this propagates upstream and stimulates increased production.
This is a positive feedback loop, and has some interesting design implications that we will explore at the end of this post.



<figure class="panel">
      <img src="{{ '/assets/img/plots/debt.png' | relative_url }}"
            alt="Debts owed across different cities">
  <figcaption>
      How much debt each pair of cities owes at the end of the simulation.
  </figcaption>
</figure>

The final interesting set of data is on a special "debt" resource, which accrues whenever a trade happens and one party doesn't have enough silver to pay the value difference.
As expected, the central trade hub Anar is owed the most outstanding debt, which is almost certainly due to their high bronze production.
Debt is meant to fulfil two main purposes: 1) it allows trade to continue if a market runs out of silver, and 2) it leads to interesting emergent diplomacy driven by the economy.
For example, what happens when the Crown of Anar demands repayment of the outstanding $56,491$ silver from Gasur?

## So What?

We've explored a little bit of the Kārum simulation, and seen how goods propagate through the network.
Exploring how the system work has led to some interesting design insights.
For this, recall from <a href="https://karum.dev/2026/06/11/grain-prices.html">the first blog post</a> that market price is computed with,
<div>
 $$ p = \text{clamp}\left(p_{\min}, b\frac{d}{s}, p_{\max}\right), $$
</div>
where $p$ is the price, $b$ is the base price, $d$ is the demand, and $s$ is the supply.


### Seasonality of Trade

One of the first things I noticed is that trade would ramp up just after harvest, then dwindle down to zero until the start of the next year.
This is because each caravan consumes grain on its journey, and this is priced into their profit calculation.
Just after harvest, when grain is cheap, caravans can chase a slim profit and make many trips.
As the price of grain increases, only higher-profit trades become economical.
This has led to a policy the player can enact: _Grain Subsidies_.
By selling grain at a fixed price from the royal stores, the player can encourage year-round exports even when the market shows a shortage.

This seasonality can further be reduced by improving roads and building waystations.
These will reduce travel time and per-tick grain consumption, which means caravans travelling on the route can move more goods faster and cheaper.
This directly influences the trade network topology.



### Persistent Excitation of Commodities

So what happens when a good has zero (or near-zero) demand?
When $d\approx0$, then a very small supply $s$ will immediately drop the price to $p_{\min}.$
This is directly applicable to copper; which has a very low rate of consumption outside of the forge.
So how do we "excite" Sipir to increase copper production without flooring the price?
We need to propagate the copper demand from Anar's forges through the network.
This increase in demand will stabilize the price above the minimum, and will incentivize the production of more copper.


### Trade Flow Rate

When a caravan enters a town, it automatically picks up the most profitable commodities for export.
Each city tries to keep $s\approx d$, so at most the caravan can pick up one tick of supply.
Let's consider a caravan with origin $O$,  trade destination $D$, and a round-trip travel time of $T$ ticks.

If $D$ produces a good, like textiles, that $O$ doesn't produce, the ideal caravan will need to cary $T d_O$ units.
Using the induced demand we just discussed, the destination will produce $d_D + d_O$ units per tick, which will accumulate up to $d_D + d_O T$ between caravan visits.
Thus, the commodity price will gradually decrease, then skyrocket as the caravan loads $T$ ticks worth of production.
The caravan then dumps $d_O T$ units of commodities into the origin market, cratering the price.

The results is wild swings in the price as caravans carry off multiple ticks worth of consumption.
To overcome this, I've allowed cities to send a caravan *per tick* on each route, which smooths out the spikes -- at least to the "smoothness" of Figs. 2--5.

### Consumption Rate vs Price Stability

Another results that's obvious in hindsight is that the stability of a price depends directly on the demand.
When I talk about "price stability" I mean, how much can we buy/sell before hitting the minimum/maximum price.
Consider the two curves below, with a minimum price of 1, maximum price of 2, and sensitivities of 1 (red) and 2 (blue), respectively.

 <iframe src="https://www.desmos.com/calculator/vjjowjbllx" width="100%" style="min-height:200px"></iframe> 

We can see that for the lower demand commodity ($b\,d=1$), avoids the price bounds for $s\in(0.5, 1)$, while higher demand ($b\,d=2$) gives a range of $s=(1,2)$.
So, the higher the demand the more we can produce without cratering the price -- and the more we can export and trade for profit.

### Balancing Textiles

How does this affect luxury goods like textiles?

One answer is to add a per-capita consumption rate, which increases the demand $d$ and stabilizes the price.
This is what allows goods to have an interesting and (relatively) smooth supply curve in Fig. 4, however, that kind of defeats the purpose of having a luxury good.
This also necessarily consumes more of the commodity, meaning you aren't actually creating a surplus to trade.

We could also crank up the base price $b$, since stability is the product $b\times d$.
This makes more sense for an expensive luxury good, but I'm not convinced that this will scale reasonably.
If we want textiles $s \approx 100$ units (Fig. 3) to match goods $s \approx 500$ units (Fig. 4), we need to $5\times$ the price.
That corresponds to a base price of $75$ silver, or about $5\%$ of a city's starting wealth per unit. 

A third option is to adjust the price formula, and try to satisfy demand over $t$ ticks via,
<div>
 $$ p = \text{clamp}\left(p_{\min}, t\,b\frac{d}{s}, p_{\max}\right).  $$
</div>
Now we could, in theory, match the sensitivity of goods by storing $t=10$ ticks worth of goods.
This also creates extra capacity in the market that can be sold off via trade.

I think increasing the base price and adding a price horizon makes the most sense for a scarce luxury good.
This also means our production AI can no longer try to match $s\approx d$, and will need to incorporate the horizon $t$ explicitly.
This effectively makes our production AI look like a <a href="https://en.wikipedia.org/wiki/Closed-loop_controller">feedback control system</a>, which I think will be an interesting topic for a future devlog.



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

