# Crafting without a map dependency

This mod needs `items_base` and `ui_base`. It can be opened from a visual novel choice, a scene interactive or an RPG event. It does not depend on a game, tiles, stats or a battle core.

1. Define ordinary `item` entities for materials and products.
2. Define a recipe (`Kind: recipe`).
3. List it in a station (`Kind: craft_station`).
4. Run `action craft.open station=my_game/bench` from any scenario.

```json
{
  "Kind": "recipe",
  "Items": [{
    "Id": "my_game/salve",
    "Name": "my_game/recipe/salve/name",
    "Description": "my_game/recipe/salve/desc",
    "Time": 5,
    "Input": [{"Id": "my_game/herb", "Count": 2}],
    "Output": [{"Id": "my_game/salve", "Count": 1}]
  }]
}
```

```json
{
  "Kind": "craft_station",
  "Items": [{
    "Id": "my_game/bench",
    "Name": "my_game/station/name",
    "Recipes": ["my_game/salve"]
  }]
}
```

Names and descriptions are locale keys. Add them to your mod's `locale/ru.properties` and `locale/en.properties`. The Data Editor discovers both kinds from `schemas/crafting.json`; the command palette knows `craft.open` and validates the station reference. No C# is needed to add recipes or stations.

The window shows materials held/needed, products and live availability. Esc closes it. The shared overlay host blocks walking while it is open. A successful craft advances the game clock by `Time` minutes. Ingredients and products use the existing items save slot; there is no second crafting inventory or separate save format.

All raw ingredients and outputs must resolve, counts must be positive and totals must fit in an integer. Duplicate ingredient rows count together. A refused craft changes no stack. This deliberately does not use RecipeFactory's tolerant display resolution, which can omit unknown ingredients.

`CraftLedger` exposes batch arithmetic for other interfaces. `CraftPanel` rebinds its inventory on show after a new game or load. The station is an explicit recipe list; discovery/unlock rules belong to the game's scenarios (offer another station after a quest or flag).

Run `--run-tests --mod=crafting` for ledger and JSON round-trip tests. RPG Plus's `woodland` and `woodland_pact` suites exercise the window, quests, real victory loot, crafted equipment and save/load together.
