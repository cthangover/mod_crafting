# Crafting — bancos de trabajo que convierten materiales en objetos

Crafting añade estaciones donde el jugador fabrica cosas: un banco de trabajo, la mesa de un alquimista, un costurero. Una estación ofrece una lista fija de recetas; cada receta toma unos objetos de la bolsa y entrega otros. Todo son datos —la estación, las recetas y objetos corrientes—, y un escenario abre la estación con una sola línea. Funciona en cualquier juego: desde una respuesta de diálogo en una novela visual, un clic en un objeto o un evento en un mapa de tiles.

## En el juego

RPG Plus tiene un banco junto al pozo del pueblo. El jugador ha reunido dos pieles plateadas de lobo y tres piezas de resina viva, y elige el manto del guardián:

![La ventana del banco del pozo sobre el mapa del pueblo: tres recetas a la izquierda — Woodland salve, Warden mantle (seleccionada), Ward lantern; a la derecha la imagen y la descripción del manto, INGREDIENTS · owned / needed: Silver wolf fur 2 / 2, Living resin 3 / 2; PRODUCTS: Warden mantle × 1; el botón Craft; abajo, "Ingredients are consumed only when you craft. Esc closes."](assets/workbench.jpg)

- **Recetas** a la izquierda; haz clic en una para verla.
- **Ingredientes** muestran cuántos tienes y cuántos hacen falta.
- **Craft** solo está disponible cuando lo tienes todo.

Tras **Craft**:

![El mismo panel tras fabricar: Silver wolf fur 0 / 2, Living resin 1 / 2 y "Done. The product is in your bag."](assets/crafted.jpg)

La bolsa antes y después — las pieles desaparecen, queda una resina y llega el manto:

![Dos filas de casillas de la bolsa. Antes: champiñón, monedas ×25, resina ×3, piel de lobo ×2. Después: champiñón, monedas ×25, resina, Warden mantle.](assets/bag-before-after.jpg)

Fabricar lleva su tiempo: el reloj del juego avanza lo que marca el `Time` de la receta, en minutos — 10 para el manto. El manto en sí es equipo (véase Equipment) y se puede poner de inmediato.

## Una estación y sus recetas

El banco de RPG Plus:

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

y una de sus recetas:

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

- Una estación lista las recetas por id, en el orden de presentación; puede compartir recetas con otras estaciones.
- `Input` se toma de la bolsa y `Output` se le añade; `Time` va en minutos de juego.
- Los nombres y las descripciones son claves de traducción de tu mod.

No hace falta aprender antes estas recetas: una estación ofrece todas las suyas.

## Abrir una estación

El evento del banco en el mapa del pueblo ejecuta:

```scenario
ptext "En el banco hay una instrucción: «No probar. No oler.»" key=rpg_plus/woodland/workbench/1
action craft.open station=rpg_plus/workbench
end
```

Mientras la ventana está abierta, el jugador no puede caminar; Esc la cierra.

## Seguro por diseño

Una fabricación se comprueba por completo antes de cambiar nada: cada ingrediente y producto debe existir, las cantidades deben ser positivas y la bolsa debe tener suficiente. Si algo falla, no se toma nada. Los objetos fabricados son objetos corrientes, así que se guardan con la bolsa.

## Si algo falla

1. **Craft se queda gris.** Falta un ingrediente; los números "tienes / hacen falta" dicen cuál.
2. **Una receta no aparece en la ventana.** La estación nombra en `Recipes` un id que ninguna receta tiene.
3. **La ventana no se abre.** El id de `station=` no coincide con ninguna estación; revisa el log.
