---
layout: post
title:  "Bitmite Blitz -- A Sort of Post Mortem"
date:   2014-05-03 09:00:00 -0500
categories: unity updates bitmite-blitz post-mortem
permalink: /blog/bitmite-blitz/:title
---
> This post was originally from ludumdare.com, intended as a “how-to”.

![Logo](/assets/img/posts/bitmite-blitz-gj-logo.png)

Hey, folks. Coin Flip Games here to tell you about our entry for LD29, Bitmite Blitz. We’re incredibly proud of this game and wanted to make sure YOU got a chance to play it. If you’re not convinced, here’s some of our favorite reviews we’ve gotten so far!

<!--break-->

> “Super fun game. I have highscore! for now :P” – tehryanx (Still has the high score, btw)

> “I love games that put me in a perspective I never would have thought to explore. Great game!” – greysphere

> “This is definitely one of the most polished games I’ve seen in the LD. Having an achievement system is an impressive addition, given the time constraint.” – StencylTuo

> “Good game, it gives me the same feeling as Flappy bird which is a good thing.” – Sparrow

[So go give it a shot already!](http://www.ludumdare.com/compo/ludum-dare-29/?action=preview&uid=31135)

Back? Okay. I wanted to talk a bit about how we accomplished the global leaderboard. I’d love to see more games with this feature because I think it adds a whole new dimension.  So, to start, you can implement this in any language or game engine you would already be using. Some (like Unity) will make it easy than others, however. You need 3 things, a server to host the scoreboard api and scores, you game and a communication layer between them. Ideally, you want to have something in place to prevent people from adding fake scores as well. Screen Shot 2014-05-01 at 2.46.38 PM

Set up a database of some sort. Use whatever you would feel comfortable with and if you’re not comfortable with anything, use MySQL or sqlite. You can get away with, as a bare minimum, a single table: scores, but that’s not so much fun, is it? You can easily use this in a bunch of games if you do it right the first time.

![Setup 1](/assets/img/posts/bitmite-blitz-pm-1.png)

0. Set up your database engine (mysql in our case)
0. Add a database and user. Make sure that the user permissions make sense. Don’t give them GRANT access to anything. Ever.
0. Add the tables as below:

![Setup 1](/assets/img/posts/bitmite-blitz-pm-2.png)

Alright! Done with the database for now. Now we need to setup the API. I’m going to leave the actual implementation up to you (as an exercise or something), but the functionality you’ll need is…

To receive input from the web in some well defined format. I used PHP to get data as JSON. You could use Ruby, Python or whatever fancy language you want. You could use XML too if that’s what you’re into.
Check the input to ensure that it’s valid. (Did this come from a legitimate source? Does the input make sense?)
Sanitize the input. (Can’t stress this one enough)
Perform some action on the data. (Run the command ‘getScores’)
Return results in some well defined format. (Package up the results of the command as JSON and pass it back)
And on the client-side (game) we need to do the same thing, but in reverse. The client needs to build these requests and be able to parse the responses. Since I used JSON, I’ll show our request as an example.

```json
    {
        "cmd": "Leaderboard/getScores",
        "api_key": "...",
        "signature": "...",
        "time": "..."
    }
```

And the server would spit back

```json
    [{
        "name": "Awesome Bitmite Blitz Player",
        "score": 100000
    }, {
        "name": "Awesome Bitmite Blitz Player 2",
        "score": 10000
    },
        // ...
    ]
```

Easy enough. The trickiest part is ensuring that the scoreboard can’t be tricked into allowing scores that aren’t legitimate. That’s where the API key and secret come in. For every game that will use this leaderboard, generate some random string for the API key and some random, hard to guess string for the secret. The database knows what these are and your API can check them out no problem. You’ll also need to let your game know what these are and if you’re lucky enough to have a compiled language, they’ll be safe from curious eyes. (Not strictly true thanks to decompilers, but it definitely makes things more difficult).

So now the server API and the game both know the API key and secret. Great. Now let’s use them to generate a signature for all the requests that the game client will make.

![Setup 1](/assets/img/posts/bitmite-blitz-pm-3.png)

So the idea is that both the client and server can construct the signature independently and compare them, but prying eyes cannot. The key, secret and a time/token are needed so that requests to the same API resource (getScores) won’t always generate the same signature. It’s secure because the secret is… well… secret.

(There might be some of you that recognize this pattern. It’s basically the same model that Amazon Web Services uses.)

So that’s the basic idea. Here’s some resources to use if you want to implement it yourself:

- [MySQL](http://www.mysql.com/)
- [PHP](http://php.net) ([PDO](http://www.php.net/manual/en/book.pdo.php))
- [Unity SimpleJSON](http://wiki.unity3d.com/index.php/SimpleJSON)
- [Unity WWW](https://docs.unity3d.com/Documentation/ScriptReference/WWW.html)
- [Unity crossdomain.xml](https://docs.unity3d.com/Documentation/Manual/SecuritySandbox.html)
- [Matching PHP md5() to C#](http://stackoverflow.com/questions/5821677/md5-hashing-does-not-match-in-c-sharp-and-php)

Cheers! I hope someone can get some use out of this!