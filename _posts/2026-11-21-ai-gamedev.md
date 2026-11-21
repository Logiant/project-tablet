---
title: "AI and Game Development"
date: 2026-11-21
number: 6
---

In this blog post we're going to discuss how AI (i.e., Large Language Models, not <i>classical methods</i>) can be used thoughtfully as part of game development, which is a controversial topic right now.
While LLM coding assistants are commonplace at large software companies, there are now a huge number of AI "indie" games released every day that are, essentially, garbage.
I think <i>AI slop</i> is just a new term for low-quality games, replacing <i>asset flip</i>; any poorly designed or built game is being called "slop" whether AI was used or not.

The idea for this post came from an AI coding workshop at my full-time job, where I work as a staff software engineer.
As a "hello world" example, we were tasked with using Claude Code to generate a simple app or game.
I prompted Claude Code to create the game "Snacman" which is a combination of Snake and Pacman.
It is an extremely simple concept, and it serves as a useful toy example to think about game design and how to stop AI from turning your game into slop.


I hope you will also agree with me that all (good) games need two things an LLM cannot produce: 1) a strong <b>vision</b> for the player experience, and 2) <b>taste</b> for what makes a game good -- preferably honed by playing games for thousands of hours.

## Snacman

To generate Snacman, I fed Opus 5.5 the following specifications:
<ul>
<li>Generate a web application that is a combination of Pacman and Snake</li>
<li>You play as Pacman, but as you eat pellets you gain a Snake-like tail</li>
<li>Surrounding an enemy with your tail and closing them off should defeat them</li>
<li>If an enemy touches your head, you lose a life; if an enemy touches your tail it should be cut off and disappear</li>
</ul>

Afterwards I asked Claude to take a second pass to "make the game more fun."
It found 6 issues and listed their fixes:
<ol>
<li><b>Trapping:</b> enclosing a region didn't help you win, it trapping multiple ghosts should multiply your score.</li>
<li><b>Late game:</b> it's a slog getting the last few dots, surrounding an area should pick them all up.</li>
<li><b>Tail loss:</b> losing the tail was pure punishment, it should at least stun the ghosts.</li>
<li><b>The opening:</b> all three ghosts leave the center box at once, this is unfair and should be staggered.</li>
<li><b>Difficulty:</b> all ghosts moved the same speed the whole game, the ghosts should speed up as pellets are consumed.</li>
<li><b>Visual feedback:</b> there is no visual juice!</li>
</ol>

I think this is particularly interesting because the words "slog" and "unfair" appeared verbatim in the response, none of which Claude can actually experience as an LLM.
If you're interested, the final result is embedded below.
No, I will not be releasing it on Steam.

<iframe src="{{ '/assets/games/snacman.html' | relative_url }}" title="Snacman" loading="lazy" style="display:block;width:100%;max-width:520px;aspect-ratio:21/22.6;border:0;margin:0 auto"></iframe>

## Game Design

I think this is a useful exercise, because the pattern of adding more AI-designed mechanics to improve "fun" is a common one -- especially if you're on the lookout for <a href="https://www.reddit.com/r/StrategyGames/new/">indie grand strategy games</a>.
My initial prompt already contained several design assumptions, and it's worth stating them explicitly to think about them for a while:
<ul>
<li><b>Level design:</b>
The level is a Pacman-like maze with lots of 1-tile wide corridors and tight turns; every space has a pellet.</li>
<li><b>Movement:</b>
Movement is Snake, so you cannot turn back through your own body.</li>
<li><b>Enemies:</b>
The enemies are loosely based on the <a href="https://pacman.fandom.com/wiki/Maze_Ghost_AI_Behaviors">Pacman ghosts</a> and will greedily chase the player's head.</li>
<li><b>Collisions:</b>
Your tail acts like a mobile wall, which stops your movement without damaging you; ghosts cut your tail short on collision.</li>
</ul>

Overall, I think this is an interesting set of rules; however, several pieces are not internally consistent.
Here are a few examples:

<b>Level design and the tail:</b> every space having a pellet means that you're constantly taking in pellets, and your tail grows rapidly from the beginning.
This makes navigating the maze challenging, and it's easy to get stuck by running into your own tail.

<b>Level design and movement:</b> the level is taken from Pacman, but the tail means you cannot backtrack away from an oncoming ghost.
This means every move through the maze is irreversible, and you're only making decisions at junctions.

<b>Movement and enemies:</b> every move is irreversible, but the ghosts don't telegraph their own motion.
Losing feels random and unfair, because at some point you will have to commit to a path without knowing where the ghosts will be over the next few seconds.


I think these are exciting problems to have, because each of them presents an obvious chunk of the design space that can be analyzed.
These may be intentional (e.g., level design and movement makes the game a lot more tactical) or unintentional (e.g., it doesn't feel like Snake if your tail grows every time you move).
How you solve these problems depends on the <b>subjective human experience</b> you want to evoke in the player, i.e., your <b>vision</b> for the game.

### LLM Game Design

For comparison, I asked Claude to list the biggest issues and what would make the game more fun.
The top items were: (1) game balance, (2) the maze shape doesn't suit wrapping things with the tail, (3) splitting is ambiguous if you are able to cut the maze exactly in half, (4) movement is stiff, (5) the ghosts are dumb with greedy movement, and (6) dying is harsh because you lose the entire tail.
These are useful points, however: (1), (4), and (5) are obvious low-hanging fruit -- the game should be balanced and <a href="https://youtu.be/Fy0aCDmgnxg?si=Z4PaWkhITPveFrR6">have juice</a>.
I don't think (3) is even mechanically possible to achieve, it's an example of Claude being overly-cautious.

This leaves (2) and (6) as legitimate game design concerns.
However, Claude frames (2) from the perspective of trapping ghosts as part of the high-level "game fantasy."
This doesn't give us anything actionable in terms of game mechanics -- do we redesign the level? change player movement? randomize enemy spawns?
In contrast, the suggestion to resolve (6) is a new mechanic: when the tail is cut, turn it into pellets.
However, the <b>level design and tail</b> interaction tells us this will be a bad design: the 50% of the spaces without pellets are locations where your tail is, and losing your tail fills these spaces back up with pellets.
That resets up to 50% of the world's empty spaces, which is way more punishing!


### The "Right Answer"

The above design issues can really only be answered by a human playing the game, experiencing it, and thinking deeply about the impact of design decisions on the player experience.
While LLMs can run the games, they're incapable of experiencing the game subjectively; the best they can do is guess based on the source code and their training data.
LLMs are also designed to be sycophants, so they would rather layer on additional features instead of challenging flaws in the user's perfect initial design.
This is, of course, subject to how you prompt the agent.


It's worth repeating: there is <b>no right answer</b> because games are <b>subjective experiences</b>!
Forcing the player to commit to a long corridor might be great design if you want to create the <i>Dark Souls</i> of Snacman-likes -- this would require other design changes, like slower enemy movement and clear telegraphing.
A more casual experience might use an open level with waves of enemies that spawn, where your tail simply acts like a <i>Tron</i> light trail.

The closest thing we can get to a "right answer" is this: you need to have a <b>vision</b> for what your game will be, and you need to use your human brain to understand the <b>subjective experience</b> that your game is creating.
Then you need to use your <b>taste</b> to critically evaluate whether your game is any good in its current iteration.
No LLM can do these for you.

As another example, I've embedded Darksnac below.
It's meant to feel more tactical: ghosts telegraph their motion, the level is a bit more open with sparse pellet placement, and you accelerate when moving in a straight line.
The vision is that the player commits to a direction in order to outsmart the ghosts, while accelerating movement keeps the game chaotic and allows for easier escapes.
I think it's a better game than Snacman, but it still doesn't have a lot of design thought put into it.

<iframe src="{{ '/assets/games/darksnac.html' | relative_url }}" title="Darksnac" loading="lazy" style="display:block;width:100%;max-width:520px;aspect-ratio:21/22.6;border:0;margin:0 auto"></iframe>

## Worth it?

I think there are many kinds of developers looking to adopt AI into their toolkit.
Here is my unsolicited advice based on your taste and vision levels:


If you have neither strong <b>taste</b> nor <b>vision</b> you will almost certainly fail.
This group includes AI enthusiasts and tech bros who think they can make a quick game with a swarm of AI agents.
If you have bad taste (because you don't play games), your game is going to suck and I don't want to play it.
If you make a post online asking "what do I add next?" then you have a weak <b>vision</b> and you're probably going to get flamed.
This group is core to the slop problem, and will hopefully go the way of NFTs and "play to earn" crypto games.


If you have strong <b>taste</b> but weak <b>vision</b>, game design is going to be a lot harder than you think -- even with AI assistance.
Anyone who games regularly will know if the output is good or bad, so you'll at least be able to evaluate what the AI created.
I think it's important to keep the scope tight, and use the AI agent to articulate your core vision that every change is measured against.
Making good design decisions that actually improve the game takes tremendous mental effort, and it's a totally separate set of skills that need to be learned.
It's common knowledge in game design that players know what to complain about, but they have no idea how to fix it in a way that's actually fun.
Improving the subjective experience is not something that an AI can help you with.

If you have strong <b>taste</b> and <b>vision</b> then you're probably already developing games, and hopefully AI can accelerate the process.
I would love to play your game, but if you use AI you'll need to have a clear workflow and keep a close eye on what the LLM is doing.
Just like generative AI for art struggles with <a href="https://www.kittl.com/blogs/ai-art-fails-dsi/#10-hilarious-ai-art-fails-and-how-to-fix-them">human anatomy</a> and <a href="https://www.icentricagency.com/insights/the-physics-don-t-lie-spotting-ai-generated-imagery-in-2025">physics</a>, LLMs make all sorts of weird coding decisions.
If you're not careful, you might end up in an endless bug-hunting loop, or waste your time refactoring a bunch of AI-generated slop code whenever the design changes.


At the end of the day games are a subjective human experience, which necessarily means a human designer has to be involved.

Speaking of subjective experiences, please reach out if you're interested in creating an artisanal human-crafted Steam capsule for <a href="https://karum.dev">Kārum</a>!