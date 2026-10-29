# Crafting——材料をアイテムに変える作業台

Crafting は、プレイヤーが物を作るステーションを追加します: 作業台、錬金術師の机、裁縫セット。ステーションは固定のレシピ一覧を提示し、各レシピはかばんからいくつかのアイテムを取って、別のアイテムを渡します。すべてがデータ — ステーション、レシピ、普通のアイテム — で、ステーションを開くのはシナリオの 1 行です。どのゲームでも動きます: ビジュアルノベルの選択肢からでも、オブジェクトのクリックからでも、タイルマップのイベントからでも。

## ゲームの中では

RPG Plus には村の井戸のそばに作業台があります。プレイヤーは銀狼の毛皮 2 枚と生きた樹脂 3 個を集め、森の守人のマントを選んだところです:

![村のマップの上に開いた井戸の作業台ウィンドウ: 左にレシピ 3 つ — Woodland salve、Warden mantle（選択中）、Ward lantern。右にマントの絵と説明、INGREDIENTS · owned / needed: Silver wolf fur 2 / 2、Living resin 3 / 2。PRODUCTS: Warden mantle × 1。Craft ボタン。下部に "Ingredients are consumed only when you craft. Esc closes."](assets/workbench.jpg)

- 左が**レシピ**。クリックすると内容が見えます。
- **材料**には、持っている数と必要な数が出ます。
- すべてそろったときだけ **Craft** が押せます。

**Craft** を押したあと:

![製作後の同じパネル: Silver wolf fur 0 / 2、Living resin 1 / 2、そして "Done. The product is in your bag."](assets/crafted.jpg)

前後のかばん — 毛皮は消え、樹脂は 1 個残り、マントが届きます:

![かばんの 2 列。前: キノコ、硬貨 ×25、樹脂 ×3、狼の毛皮 ×2。後: キノコ、硬貨 ×25、樹脂、Warden mantle。](assets/bag-before-after.jpg)

製作には時間がかかります: ゲームの時計はレシピの `Time` だけ進みます — マントは 10 分。マント自体は装備（Equipment を参照）なので、すぐに身につけられます。

## ステーションとそのレシピ

RPG Plus の作業台:

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

そしてそのレシピのひとつ:

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

- ステーションはレシピを id で、表示順に列挙します。ほかのステーションとレシピを共有してもかまいません。
- `Input` はかばんから取り、`Output` はかばんへ加わります。`Time` はゲーム内分です。
- 名前と説明はあなたの mod の翻訳キーです。

これらのレシピを先に覚える必要はありません: ステーションは、自分の持っているレシピをすべて提示します。

## ステーションを開く

村のマップでの作業台のイベントは、このシナリオを実行します:

```scenario
ptext "作業台には注意書きが置いてあります:「味見するな。嗅ぐな。」" key=rpg_plus/woodland/workbench/1
action craft.open station=rpg_plus/workbench
end
```

ウィンドウが開いている間、プレイヤーは歩けません。Esc で閉じます。

## 仕組みとして安全

製作は、何かが変わる前に完全に検査されます: すべての材料と完成品が存在し、数が正であること、かばんに在庫が十分あること。どこかひとつでもおかしければ、何も取りません。作られたアイテムは普通のアイテムなので、かばんと一緒にセーブされます。

## うまくいかないとき

1. **Craft が灰色のまま。** 材料が足りません。「所持 / 必要」の数字がどれかを教えてくれます。
2. **ウィンドウにレシピが出ない。** ステーションの `Recipes` が、どのレシピも持たない id を挙げています。
3. **ウィンドウが開かない。** `station=` の id がどのステーションとも一致しません。ログを確認してください。
