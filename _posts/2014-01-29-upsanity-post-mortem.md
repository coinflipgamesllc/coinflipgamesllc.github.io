---
layout: post
title:  "Upsanity -- Post Mortem"
date:   2014-01-29 09:00:00 -0500
categories: unity updates upsanity post-mortem
permalink: /blog/upsanity/:title
---
![Global Game Jam](/assets/img/posts/ggj-300x163.jpg)

Just a quick post today to talk about our entry for the Global Game Jam 2014. First off, great experience. If you haven’t done a global game jam and you’re even marginally interested in game development, do it. I mean, we have to break this year’s record next year anyway. (There were more people participating and more games created this last weekend than in any other game jam ever).

<!--break-->

Anyway, our entry is called [Upsanity](http://coinflipgames.co/games/upsanity/). We don’t see things as they are, we see them as we are… and some see themselves exactly how they want to. Upsanity is a game about a insane asylum patient with delusions of being an elevator. Who are you to judge? Help the patient reach their dreams to move people from floor to floor!

Go play it. No, really. We’ll talk about the technical details after you’ve beaten it.

Have fun? Alright, now down to business. Upsanity was a joy to make. The process went incredibly smooth despite us being out of our elements. The biggest frustrations would be:

Unity Physics
Not finding an artist to join us
That’s it! We pitched our idea to the crowd at the jam and got a few laughs, but no artists. That’s alright, we made do just fine.

![Colliders](/assets/img/posts/upsanity-colliders.png)

So here’s the solution we came up with, after the game jam ended, of course. Forget the colliders, we need a single trigger. Set a trigger in the middle of the elevator and let the passengers run into it. At that point, lock their y-position to that of the elevator. Both sprites have centered pivots so they line up exactly how you would expect. The ability for passengers to collide with one-another means that they locked into the y-position of the elevator, but still move dynamically on the x-axis.