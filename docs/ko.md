# Crafting — 재료를 아이템으로 바꾸는 작업대

Crafting은 플레이어가 무언가를 만드는 작업대를 추가합니다: 작업대, 연금술사의 탁자, 바느질 세트. 작업대는 고정된 레시피 목록을 제시하고, 각 레시피는 가방에서 몇 개의 아이템을 가져가 다른 아이템을 돌려줍니다. 전부 데이터입니다 — 작업대, 레시피, 평범한 아이템 — 그리고 작업대를 여는 것은 시나리오 한 줄입니다. 어떤 게임에서든 동작합니다: 비주얼 노벨의 대화 선택지에서, 오브젝트 클릭에서, 타일 맵의 이벤트에서.

## 게임 속에서

RPG Plus에는 마을 우물 옆에 작업대가 있습니다. 플레이어는 은빛 늑대 털가죽 두 장과 산 나무진 세 조각을 모은 뒤 수호자 망토를 고릅니다:

![마을 맵 위의 우물 작업대 창: 왼쪽에 레시피 셋 — Woodland salve, Warden mantle(선택됨), Ward lantern. 오른쪽에 망토 그림과 설명, INGREDIENTS · owned / needed: Silver wolf fur 2 / 2, Living resin 3 / 2. PRODUCTS: Warden mantle × 1. Craft 버튼. 아래에 "Ingredients are consumed only when you craft. Esc closes."](assets/workbench.jpg)

- 왼쪽이 **레시피**입니다. 하나를 클릭해 보세요.
- **재료**에는 가지고 있는 수와 필요한 수가 나옵니다.
- 모두 갖춰졌을 때만 **Craft**를 누를 수 있습니다.

**Craft**를 누른 다음:

![제작 후 같은 패널: Silver wolf fur 0 / 2, Living resin 1 / 2, 그리고 "Done. The product is in your bag."](assets/crafted.jpg)

전과 후의 가방 — 털가죽은 사라지고 나무진은 하나 남으며 망토가 도착합니다:

![가방의 두 줄. 전: 버섯, 동전 ×25, 나무진 ×3, 늑대 털 ×2. 후: 버섯, 동전 ×25, 나무진, Warden mantle.](assets/bag-before-after.jpg)

제작에는 시간이 듭니다: 게임 시계가 레시피의 `Time`만큼 흐릅니다 — 망토는 10분. 망토 자체는 장비(Equipment 참조)라서 바로 착용할 수 있습니다.

## 작업대와 그 레시피

RPG Plus의 작업대:

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

그중 하나의 레시피:

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

- 작업대는 레시피를 id로, 표시 순서대로 나열합니다. 다른 작업대와 레시피를 공유해도 됩니다.
- `Input`은 가방에서 가져가고 `Output`은 가방에 더해집니다. `Time`은 게임 분 단위입니다.
- 이름과 설명은 여러분 mod의 번역 키입니다.

이 레시피들을 먼저 익힐 필요는 없습니다: 작업대는 자기 레시피를 모두 제시합니다.

## 작업대 열기

마을 맵의 작업대 이벤트는 이 시나리오를 실행합니다:

```scenario
ptext "작업대 위에는 주의사항이 놓여 있다: «맛보지 마라. 냄새 맡지도 마라.»" key=rpg_plus/woodland/workbench/1
action craft.open station=rpg_plus/workbench
end
```

창이 열려 있는 동안에는 플레이어가 걸을 수 없습니다. Esc로 닫습니다.

## 설계부터 안전하게

제작은 무엇이든 바꾸기 전에 완전히 검사합니다: 모든 재료와 결과물이 존재하고, 개수는 양수이며, 가방에 충분히 있어야 합니다. 하나라도 어긋나면 아무것도 가져가지 않습니다. 만든 아이템은 평범한 아이템이라 가방과 함께 저장됩니다.

## 문제가 생겼을 때

1. **Craft가 회색으로 남아 있습니다.** 재료가 부족합니다. «보유 / 필요» 숫자가 무엇인지 알려 줍니다.
2. **창에 레시피가 없습니다.** 작업대의 `Recipes`가 어떤 레시피에도 없는 id를 가리킵니다.
3. **창이 열리지 않습니다.** `station=`의 id가 작업대와 일치하지 않습니다. 로그를 확인하세요.
