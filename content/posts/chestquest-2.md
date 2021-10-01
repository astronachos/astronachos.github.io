---
title: "Chest Quest — Record #2"
date: 2023-05-06
tags: [chestquest, defold]
summary: "What line should be drawn now? I've finished the first version of the game core module. It looks, um, something like this..."
cover:
  image: "/images/vscode.png"
---

## Console

What line should be drawn now? I've finished the first version of the game core module. It looks, um, something like this:

```lua
... Many turns above ...

-= LEVEL 6, POINTS 16 =-
--
[4c] [4a] [  ] [1b] [1a]
[2c] [  ] [  ] [2b] [  ]
[  ] [5b] [C2] [  ] [4d]
[**] [  ] [3a] [3c] [  ]
[6a] [7a] [  ] [3d] [6d]

[__]-[RR]-[__]-[**]-[6b]
--

<- Place [**] to 3:1
-> BOARD_UPDATE.PLACEMENT positions: 3:1
-> BOARD_UPDATE.MATCH positions: 1:1 2:1 4:1 5:1 3:1
-> GAME_POINTS.MATCH amount: 10
-> HAND_UPDATE.PLACEMENT indexes: 4

... Many turns below ...
```

This is how a silly bot can play a game in the console, while I watch the result of his moves.

## Game Module

What about the code?

```lua
local levels = {
  [1] = '>>>>^^^^####cccvvvvvv',
  [3] = 'cu',
  [5] = 'cuddrr',
  [7] = 'cbbss',
  [10] = '>^#cv',
}

local player = Player {
  coins = 100
}

local game = Game {
  levels = levels,
  player = player,
}
```

`levels` - something like a difficulty curve. At which levels and what exactly to add to the game items pool. When a player has used all the items from his hand, the level goes up. Right now it's a simple table bundled locally, but in the future it will be better to load it from the server.

- `c` - add a new color;
- `c` - add a new value;
- `>` - expand the game board in width;
- `^` - expand the game board in height;
- `#` - expand the hand capacity;
- `u` - an universal item matching any values and colors;
- etc.

`player` - at the moment this is the container of virtual coins and purchased items.

`game` - an instance of the game session. We can read the current state of the game components with `game.player`, `game.points`,`game.board` and `game.hand`. To nicely display the state of the game in the console, I have implemented a meta-table method `__tostring` in each component, which allows me to get a textual representation by calling `tostring(game)`.

```lua
local item = game.hand.slots[slot_index]
local valid_positions = game:get_valid_positions(item)
local events = game:make_turn(slot_index, valid_positions[1])
```

To make a turn, we choose an item in a player's hand and check what game board positions are available for it, consistent with the rules of the game. We make a move by telling the game module the item index in the hand and the target position on the game board.

```lua
-> BOARD_UPDATE.PLACEMENT positions: 3:1
-> BOARD_UPDATE.MATCH positions: 1:1 2:1 4:1 5:1 3:1
-> GAME_POINTS.MATCH amount: 10
-> HAND_UPDATE.PLACEMENT indexes: 1 2 3 4
```

In response, the game returns a list of events that happened to it. Reading the state of the game directly, ignoring events, is quite expensive and creates the risk of missing intermediate events that require consistency in animation. Such event responses help keep that sequence. For example:

1. An item is beautifully placed on the board emitting light sparks.
2. A line of items explodes with thunder and flashes.
3. The player's score counter numbers spinning, stopping.
4. The empty hand is filled with new items.

## A little more

Some more examples. Here the player buys an item from the store and uses it during the game.

```lua
local shop = Shop {
  reeroll = { price = 10 },
  bomb = { price = 20 },
  double = { price = 10 },
  universal = { price = 5 }
}

if shop:can_afford(player, 'reroll', 1) then
    shop:buy(player, 'reroll', 1)
end

if player.items['reroll'] > 0 then
    local item = player:pop_item('reroll')
    local valid_positions = game:get_valid_positions(item)
    local events = game:use_item(item, valid_positions[1])
end
```

And here's an example of a special `bomb` item that allows you to clear the game board.

```lua
local bomb = { }

function bomb:init()
  -- Nothing special for a bomb
end

function bomb:debug_description()
  return '[BM]'
end

function bomb:can_be_placed_with(other_item)
  return true
end

function bomb:is_self_destructive()
  return true
end

function bomb:affect_on_game(game)
  local items = game.board:clear_all()
  game.master:return_to_pool(items)

  game:add_event_to_stack {
    event = events.BOARD_UPDATE,
    reason = event_reasons.BOMB,
  }
end

return kit.wrap_to_class(bomb)
```

## Ready to go

Now everything is ready to start a new project in Defold and implement the interaction between the game module and the UI. To create the first prototype, I'm going to start with as neutral and primitive graphics as possible. And then I will gradually replace them with some specific graphic style.
