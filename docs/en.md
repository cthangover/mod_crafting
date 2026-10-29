# Crafting — workbenches that turn materials into items

Crafting adds stations where the player makes things: a workbench, an alchemist's table, a sewing kit. A station offers a fixed list of recipes; each recipe takes some items from the bag and gives others. Everything is data — a station, recipes and ordinary items — and a scenario opens the station with one line. It works in any game: from a dialog answer in a visual novel, a click on an object or an event on a tile map.

## In the game

RPG Plus has a workbench by the village well. The player has gathered two silver wolf furs and three pieces of living resin and picks the Warden mantle:

![The well workbench window over the village map: three recipes on the left — Woodland salve, Warden mantle (selected), Ward lantern; on the right the mantle's picture and description, INGREDIENTS · owned / needed: Silver wolf fur 2 / 2, Living resin 3 / 2; PRODUCTS: Warden mantle × 1; the Craft button; at the bottom "Ingredients are consumed only when you craft. Esc closes."](assets/workbench.jpg)

- **Recipes** on the left; click one to see it.
- **Ingredients** show how many you have and how many are needed.
- **Craft** is available only when everything is there.

After **Craft**:

![The same panel after crafting: Silver wolf fur 0 / 2, Living resin 1 / 2, and "Done. The product is in your bag."](assets/crafted.jpg)

The bag before and after — the furs are gone, one resin is left, the mantle has arrived:

![Two rows of bag cells. Before: mushroom, coins ×25, resin ×3, wolf fur ×2. After: mushroom, coins ×25, resin, Warden mantle.](assets/bag-before-after.jpg)

Crafting takes time: the game clock moves on by the recipe's `Time` in minutes — 10 for the mantle. The mantle itself is gear (see Equipment) and can be worn at once.

## A station and its recipes

The RPG Plus workbench:

```json
{
  "Kind": "craft_station",
  "Items": [
    {
      "Id": "rpg_plus/workbench",
      "Name": "rpg_plus/station/name",
      "Recipes": [ "rpg_plus/salve", "rpg_plus/mantle", "rpg_plus/lantern" ]
    }
  ]
}
```

and one of its recipes:

```json
{
  "Kind": "recipe",
  "Items": [
    {
      "Id": "rpg_plus/mantle",
      "Name": "rpg_plus/recipe/mantle/name",
      "Description": "rpg_plus/recipe/mantle/desc",
      "Time": 10,
      "Input": [ { "Id": "rpg_plus/wolf_pelt", "Count": 2 }, { "Id": "rpg_plus/resin", "Count": 2 } ],
      "Output": [ { "Id": "rpg_plus/fur_mantle", "Count": 1 } ]
    }
  ]
}
```

- A station lists recipes by id, in display order; it may share recipes with other stations.
- `Input` is taken from the bag, `Output` added to it; `Time` is in game minutes.
- Names and descriptions are translation keys of your mod.

The player does not need to learn these recipes first: a station offers all of its own.

## Opening a station

The workbench's event on the village map runs:

```scenario
ptext "On the workbench lies an instruction: 'Do not taste. Do not sniff.'" key=rpg_plus/woodland/workbench/1
action craft.open station=rpg_plus/workbench
end
```

While the window is open the player cannot walk; Esc closes it.

## Safe by design

A craft is checked completely before anything changes: every ingredient and product must exist, counts must be positive, and the bag must hold enough. If anything is wrong, nothing is taken. Crafted items are ordinary items, so they are saved with the bag.

## If something goes wrong

1. **Craft stays grey.** An ingredient is missing; the owned / needed numbers show which.
2. **A recipe is missing from the window.** The station's `Recipes` names an id no recipe has.
3. **The window does not open.** The `station=` id does not match a station; check the log.
