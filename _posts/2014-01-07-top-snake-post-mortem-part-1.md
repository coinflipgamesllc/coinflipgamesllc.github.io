---
layout: post
title:  "Top Snake Post-Mortem, Part 1"
date:   2014-01-07 09:00:00 -0500
categories: love2d updates top-snake post-mortem
permalink: /blog/top-snake/:title
---
Heya! So now that we’ve released [Top Snake](http://www.desura.com/games/top-snake), it’s time we started talking about what went into the game. As of right now, I’m not sure what all topics I’ll touch on, but I do know that I’d like to cover some technical stuff (how we did it) and some of the difficulties we had in the process. So, let’s dive in…

<!--break-->

## Some stats…
Top Snake was written in Lua using the Löve Framework. We tacked on a bunch of other libraries and wrapped our own initializer around it called ‘Bahamut’. For better or worse, this is how the game came to be. It all started with [this](https://github.com/rnewton/Snaaake); a quick prototype I made in about 12 hours. The premise since then has more or less remained the same, but the overall mechanics and polish definitely took some additional work.

We started working on Top Snake in May 2013 (really it was the last day, let’s call it June). Seven months and 383 commits later, we have version 1.0. The entire game only uses one spritesheet, with 11 sprites. Before the graphics overhaul, we were only using shape primitives (you can see this in the open sourced version above). There’s 7 songs and 21 sound effects. All of them are either CC0 or self made. Overall, very light game on assets. That was the goal.

But that’s not the fun part about Post-Mortems. The fun part is finding out how things work, what algorithms we used, what blew up in our faces.

## Some Technical Details…
I’m going to save the most complex piece of the game, enemy AI, for its own post. Instead I’m going to just touch on some of the helpful functions that we used throughout the game.

### Easing (Linear Interpolation or Lerp)

```lua
function lerp(a,b,t)
    return a+(b-a)*t
end

function colorlerp(one, two, t)
    return {
       lerp(one[1], two[1], t),
       lerp(one[2], two[2], t),
       lerp(one[3], two[3], t)
    }
end
```

These are dead simple in Lua. The idea is that jerky switches from one value to the next always looks bad. It’s better to ease into the desired value over time. So that’s what lerp() is for. Starting value (a) moves towards ending value (b) in time (t) seconds. Works with colors as well, the given function will lerp between RGB or HSV. It would be trivial to add the alpha channel to this function, but we never needed it.

### Polygon vertices

```lua
--[[
Returns a table with vertices to draw a n-sided polygon centered on
x,y with rotation (degrees) t.
@param number x - x coor
@param number y - y coor
@param number r - radius
@param number t - rotation in degrees
@param number n - number of sides
@return table for use in love.graphics.polygon
]]
function polygonVertices(x, y, r, t, n)
    local v = {}
    local step = 360 / n -- degrees

    for i = t + 359, t, -step do
        local radian = i * math.pi / 180
        table.insert(v, math.cos(radian) * r + x)
        table.insert(v, math.sin(-radian) * r + y)
    end

    return v
end
```

The idea for this function was to return all the vertices (2D coordinates, really) that it would take to create a n-sided polygon at (x,y) with rotation t and radius r. This function originally did a lot more heavy lifting with the hexagonal heads of the snakes, but now is only responsible for the rotation on the world selection screen. The table returned from this function could be plugged in directly to love.graphics.polygon() and create a shape primitive. Perfect for vector games!

### Sorted table Iteration

```lua
-- pairs, but sorted. Default is by key, provide order function otherwise
function spairs(t, order)
    -- collect the keys
    local keys = {}
    for k in pairs(t) do keys[#keys+1] = k end

    -- if order function given, sort by it by passing the table and keys a, b,
    -- otherwise just sort the keys 
    if order then
        table.sort(keys, function(a,b) return order(t, a, b) end)
    else
        table.sort(keys)
    end

    -- return the iterator function
    local i = 0
    return function()
        i = i + 1
        if keys[i] then
            return keys[i], t[keys[i]]
        end
    end
end
```

I lifted this code from a stackoverflow post, but it couldn’t hurt to get this out there. Iterating over a table in lua can seem to defy logic sometimes. If you are used to php arrays, tables are downright broken. If you’d like to gain some of that functionality, you can use spairs to iterate over a table in a sorted manner. You can either use the default, sort by key, or provide a sorting function.

I’m going to cut off here. The following posts will cover enemy AI, distribution on Desura and Itch.io, and whatever else we can think of.

Cheers!