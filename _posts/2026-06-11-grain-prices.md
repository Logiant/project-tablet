---
title: "How About the Price of Grain in Mesopotamia?"
date: 2026-06-11
number: 1
---

I am building a strategy game with a materialist core.
Each city produces grain in the Private market and Temple/Crown estates at harvest time.
The supply trickles down through consumption and waste, which leads to some interesting economic strategies.
In this blog post we will explore how the pricing mechanism leads to a behavior bifurcation, and how that can affect the balance of power both internally and externally.

<!-- ============================================================
     Interactive figure: grain price over one year.
     Paste this whole block into the post markdown, right after the
     intro paragraph (kramdown passes raw HTML blocks through
     untouched, and KaTeX ignores <script> content).
     Model: q_{k+1} = (q_k - p/T) * alpha, q >= 0
            d(t) = (T-t)/T * p ;  s(t) = q(t) * alpha^(T-t)
            x(t) = d/s, capped at X_MAX.   p = 1 (per-capita units)
     ============================================================ -->
<figure class="panel" id="grain-sim">
   <label style="display:flex;align-items:center;gap:.5rem;">
      Normalized Harvest: <input type="range" id="gs-q0" min="0.5" max="2.0"
        step="0.01" value="1.05" style="accent-color:#1f4e79;width:11rem;">
      <span id="gs-q0v" style="min-width:3.2ch;font-variant-numeric:tabular-nums;">1.05</span>
    </label>
    <label style="display:flex;align-items:center;gap:.5rem;">
      Spoilage Rate: <input type="range" id="gs-loss" min="0" max="3"
        step="0.25" value="1" style="accent-color:#1f4e79;width:7rem;">
      <span id="gs-lossv" style="min-width:2.2ch;font-variant-numeric:tabular-nums;">1</span>
   </label>
  <div style="display:flex;flex-wrap:wrap;gap:1.2rem;justify-content:center;
              align-items:center;margin-top:.7rem;font-size:.92rem;">
    <span id="gs-regime" style="font-weight:bold;min-width:14ch;"></span>
  </div>
  <canvas id="gs-canvas" width="900" height="460"
          style="width:100%;height:auto;border:1px solid var(--paper-edge);
                 box-shadow:0 2px 10px rgba(40,20,10,.12);background:#fff;"></canvas>
  <figcaption>
    Grain prices computed over one year (T = 12 ticks). 
    Adjust the normalized harvest (q(t=0)/n) and spoilage rate (α) to see the three price trajectory regimes.
  </figcaption>
</figure>
<script>
(function () {
  var T = 12, P = 1, X_MAX = 5;
  var canvas = document.getElementById("gs-canvas");
  var ctx = canvas.getContext("2d");
  var q0El = document.getElementById("gs-q0");
  var lossEl = document.getElementById("gs-loss");
  var INK = "#1a1a1a", GOLD = "#c9a227", LAPIS = "#1f4e79",
      CLAY = "#8f5d42", GRID = "#e4ddd2";

  function simulate(q0, alpha) {
    var q = q0, xs = [];
    for (var t = 0; t < T; t++) {
      var d = (T - t) / T * P;
      var s = q * Math.pow(alpha, T - t);
      xs.push(Math.min(s > 1e-9 ? d / s : X_MAX, X_MAX));
      q = Math.max((q - P / T) * alpha, 0);
    }
    return { xs: xs, qT: q };
  }

  // device-pixel-ratio crispness
  function fit() {
    var dpr = window.devicePixelRatio || 1, w = canvas.clientWidth || 900;
    canvas.width = w * dpr; canvas.height = w * (460 / 900) * dpr;
    ctx.setTransform(dpr * w / 900, 0, 0, dpr * w / 900, 0, 0);
  }

  var M = { l: 64, r: 20, t: 18, b: 46 }, W = 900, H = 460;
  function X(t) { return M.l + t / (T - 1) * (W - M.l - M.r); }
  function Y(x, ymax) { return H - M.b - x / ymax * (H - M.t - M.b); }

  function draw(frac) {
    var q0 = parseFloat(q0El.value);
    var alpha = 1 - parseFloat(lossEl.value) / 100;
    var sim = simulate(q0, alpha);
    var ymax = Math.max(2, Math.ceil(Math.max.apply(null, sim.xs) * 1.15 * 2) / 2);

    ctx.clearRect(0, 0, W, H);
    ctx.font = "15px Georgia, serif"; ctx.fillStyle = INK;

    // gridlines + y labels
    ctx.strokeStyle = GRID; ctx.lineWidth = 1; ctx.textAlign = "right";
    for (var v = 0; v <= ymax + 1e-9; v += 0.5) {
      var y = Y(v, ymax);
      ctx.beginPath(); ctx.moveTo(M.l, y); ctx.lineTo(W - M.r, y); ctx.stroke();
      ctx.fillText(v.toFixed(1), M.l - 8, y + 5);
    }
    // axes
    ctx.strokeStyle = INK; ctx.lineWidth = 1.5;
    ctx.beginPath(); ctx.moveTo(M.l, M.t); ctx.lineTo(M.l, H - M.b);
    ctx.lineTo(W - M.r, H - M.b); ctx.stroke();
    // x labels
    ctx.textAlign = "center";
    for (var t = 0; t < T; t++) ctx.fillText(t, X(t), H - M.b + 22);
    ctx.fillText("tick (month)", (M.l + W - M.r) / 2, H - 8);
    ctx.save(); ctx.translate(16, (M.t + H - M.b) / 2); ctx.rotate(-Math.PI / 2);
    ctx.fillText("grain price x(t)", 0, 0); ctx.restore();

    // base price reference
    ctx.strokeStyle = LAPIS; ctx.setLineDash([7, 7]); ctx.lineWidth = 1.4;
    ctx.beginPath(); ctx.moveTo(M.l, Y(1, ymax)); ctx.lineTo(W - M.r, Y(1, ymax)); ctx.stroke();
    ctx.setLineDash([]); ctx.fillStyle = LAPIS; ctx.textAlign = "left";
    ctx.fillText("base price b = 1", M.l + 8, Y(1, ymax) - 7);

    // price curve, drawn up to frac (animation)
    var n = Math.max(2, Math.round(frac * T));
    ctx.strokeStyle = GOLD; ctx.lineWidth = 3.5;
    ctx.lineJoin = "round"; ctx.lineCap = "round";
    ctx.beginPath();
    for (var i = 0; i < n; i++) {
      var px = X(i), py = Y(sim.xs[i], ymax);
      i ? ctx.lineTo(px, py) : ctx.moveTo(px, py);
    }
    ctx.stroke();
    ctx.fillStyle = GOLD;
    for (var i = 0; i < n; i++) {
      ctx.beginPath(); ctx.arc(X(i), Y(sim.xs[i], ymax), 4.5, 0, 7); ctx.fill();
    }

    // readouts
    document.getElementById("gs-q0v").textContent = q0.toFixed(2);
    document.getElementById("gs-lossv").textContent = lossEl.value;
    var lab = document.getElementById("gs-regime");
    if (sim.qT > 0.02) { lab.textContent = "Glut: prices fall"; lab.style.color = LAPIS; }
    else if (sim.xs[T - 1] >= X_MAX - 1e-6 || sim.qT <= 1e-9 && sim.xs[T-1] > 1.6)
         { lab.textContent = "Dearth: prices climb"; lab.style.color = CLAY; }
    else { lab.textContent = "Knife-edge: s(T) ≈ 0"; lab.style.color = INK; }
    ctx.fillStyle = INK; ctx.textAlign = "right";
    ctx.fillText("x(0) = " + sim.xs[0].toFixed(2), W - M.r - 6, M.t + 16);
  }

  // animate the curve drawing whenever inputs change
  var animId = null;
  function animate() {
    if (animId) cancelAnimationFrame(animId);
    var start = null;
    function step(ts) {
      if (!start) start = ts;
      var f = Math.min((ts - start) / 900, 1);
      draw(f);
      if (f < 1) animId = requestAnimationFrame(step);
    }
    animId = requestAnimationFrame(step);
  }

  q0El.addEventListener("input", function () { draw(1); });
  lossEl.addEventListener("input", function () { draw(1); });
  window.addEventListener("resize", function () { fit(); draw(1); });
  fit(); animate();
})();
</script>

Let's call the price of grain $x,$ in units of *annual consumption per capita*.
This means one laborer will consume one unit of grain every year.
I compute the price of any good using a symmetric hyperbola of supply $s$ and demand $d,$
<div>
$$x = b\,\frac{d}{s},$$
</div>
where $b$ is the *base price* (in this case, $1$ unit of silver buys one year of grain), and $d$ is the current demand.
Now, if supply equals demand, we recover the base price; as the supply increases (decreases) the price goes down (up).


## Computing Grain Price

Grain is a unique resource, because it's produced all at once (i.e., during harvest) and consumed slowly. 
So we don't just want enough grain *now*, we need the stores to last until the end of the year.
Commodities are processed in "ticks" throughout the year, so we can compute the demand as a function of the current tick $t,$
<div>
   $$d(t) = \frac{T-t}{T} \, n.$$
</div>
Here $n$ is the population, which consumes 1 grain per year each, $T-t$ is the number of *ticks remaining* before harvest, and $T$ is the total number of ticks in a year.
So after harvest ($t=0$) we need enough grain to feed everyone, and just before harvest ($t=T-1$) the demand signal drops to almost zero.

Supply is consumed evenly over $T$ ticks by the population, plus some monthly losses due to spoilage, etc.
For each tick, the forecasted supply is,
<div>
  $$s(t) = q(t)\,\alpha^{(T-t)},$$
</div>
where $q(t)$ is the current quantity and $\alpha$ captures 1% loss per month<!--
--><label for="sn-1" class="sidenote-toggle sidenote-number"></label><!--
--><input type="checkbox" id="sn-1" class="sidenote-toggle" /><!--
--><span class="sidenote">I compute losses monthly instead of per-tick, but this still captures the behavior on average.</span>.
We can substitute all of these terms into our price formula to compute the price of grain (where $b=1$) at each tick,
<div>
   $$x(t) = \frac{T-t}{T\,q(t)\,\alpha^{(T-t)}} \, n.$$
</div>
This gives us the price for $q(t),$ the quantity at the current tick.
This is everything we need to compute live market prices!

## A Price vs Time Curve

If we want a price curve over time, we need the dynamics of $q(t).$
From tick $t_{k}$ to the next at $t_{k+1}$, exactly $\frac{n}{T}$ grain is consumed,
<div>
   $$q(t_{k+1}) = \left(q(t_k) - \frac{n}{T}\right)\alpha.$$
</div>
We can identify the fixed point of this system by substituting a constant $q,$
<div>
    $$q = \left(q - \frac{n}{T}\right)\alpha \implies q = -\frac{\alpha n}{T(1-\alpha)} := -K.$$
</div>
Because of the problem structure, $q(t)$ exponentially decays from an initial value $q_0$ at harvest to the fixed point $-K,$
<div>
   $$q(t) = (q_0 + K)\,\alpha^t - K.$$
</div>
Notice that plugging in $t=0$ recovers $q(t=0)=q_0$ and letting $t\to\infty$ gives $q(t\to\infty) = -K,$ as we expect.

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
   $$x(t) = \frac{T-t}{T\Big(q(T) + K(1-\alpha^{(T-t)})\Big)}\,n.$$
</div>
We want $q(t)$ to be well-behaved, so in-game I saturate it at a minimum of zero.
I also saturate $x(t)$ at a maximum price, so that the price is never actually infinity; this would cause problems downstream.
This is exactly the curve in the interactive price calculator at the top of the post<!--
--><label for="sn-1" class="sidenote-toggle sidenote-number"></label><!--
--><input type="checkbox" id="sn-1" class="sidenote-toggle" /><!--
--><span class="sidenote">This isn't necessarily the *market* price, it's what a royal scribe or treasurer would record. This fits directly into the Three Estates economic model.</span>.

## Price Regimes

The price model is simple, and we never need to compute it in the game.
So why derive it?
It turns out the curve has some interesting dynamics that the player can exploit.

To identify the regimes, we will take a time derivative to see if prices go up or down.
We can compute the time derivative $\dot{x}$ with the chain rule,
<div>
  $$\dot{x} = \frac{\dot{d}\,{s} - d\,\dot{s}}{s^2}, $$
</div>
where $\dot{d} = -\frac{n}{T},$ and $\dot{s} = K\ln(\alpha)\alpha^{T-t}.$
This simplifies to,
<div>
  $$\dot{x} = -\frac{n}{T} \frac{s + (T-t)K\ln(\alpha)\alpha^{T-t}}{s(t)^2}. $$
</div>


We want to compute when $\dot{x} = 0,$ where the price of grain is constant, so we can understand when it will rise or fall.
This can only happen when the numerator is zero,
<div>
   $$\dot{x} = 0 \to -K\ln(\alpha)(T-t)\alpha^{T-t} = s(t) = s(T) + K(1-\alpha^{T-t}).  $$
</div>
Rearranging yields,
<div>
   $$s(T) = K\left( \alpha^{T-t}\left(1 - \ln(\alpha)(T-t)\right) - 1 \right).$$
</div>
For $\alpha \approx 1,$ the logarithm goes to zero and $\alpha^{T-t} \approx 1$.
This yields the condition,
<div>
   $$s(T) \approx 0.$$
</div>
So, when $s(T) = 0$ the price is approximately constant.
Note that it will still increase, since spoilage will decrease supply throughout the year.

This also means that when $s(T) < 0,$ we get a strictly positive $\dot{x}$, and we expect prices to increase at every tick.
In a bad harvest, the prices start high and continue to climb as more grain is consumed.

Conversely, if $s(T) > 0,$ we expect a strictly negative $\dot{x}$ as supply eclipses demand all year.
Grain prices start low, and they continue to fall as we approach the next harvest, which will add even more grain to the market.


We will look at how these two regimes can drive trade between grain-rich and grain-poor cities in a future devlog.


## So what?

As the Crown, the player can allocate labor to growing crops, among other duties.
This gives them some level of control over $s(T)$ through the harvest, via 
<div>
$$\begin{align*}
  s(T) &= q(T) + K(1-\alpha^0) = q(T), \\
  s(0) &= q(T) + K(1-\alpha^{T}) = s(T) + K(1-\alpha^T),
\end{align*}$$
</div>
where $\alpha$ and $K$ are known quantities.
This determines how grain prices play out each year.
Many strategy games encourage the player achieve $s(T) \gt \gt 0$: produce excess grain, keep the population fed, and export excess grain for silver.
Karum is a game built inside an economic engine, so suppressing grain prices can have unexpected consequences.

Each city in Karum is split into the *Three Estates*: the Crown, controlled by the player, alongside the Temple and the Private market.
All three can produce grain (and other commodities).
The Temple is a self-sustaining economic agent with a duty to stabilize grain prices.
If the player grants modest farmland to the Private market (a future devlog topic), they can keep grain levels near $s(T)=0$ automatically.
This also means the temple will buy low on good years, sell high on bad years, and accumulate silver.
The Temple's growing wealth could easily eclipse the Crown!

On the other hand, the player may significantly over-allocate farm labor to the Private market, dropping the price of grain.
This will drain the Temple's silver as it buys up cheap grain to stabilize the price.
This also incentivizes nearby cities to buy up the grain for export, freeing their own labor pools for growth, manufacturing, and warfare.
This creates a real risk of external economic domination, where a city with cheap grain becomes over-reliant on exports and develops into an agrarian backwater.

There is a third option, which will be the topic of the next devlog.
Allocate very little Private farm labor and spike the grain demand beyond what the Temple can provide.
This will require both the Private market and Temple to purchase grain from the Crown, accumulating all of their silver in your pocket.


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

