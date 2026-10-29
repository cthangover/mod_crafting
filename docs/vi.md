# Crafting — bàn chế tạo biến nguyên liệu thành vật phẩm

Crafting thêm vào những trạm nơi người chơi chế tạo đồ: một bàn chế tạo, bàn của nhà giả kim, một bộ kim chỉ. Một trạm đưa ra danh sách công thức cố định; mỗi công thức lấy vài vật phẩm từ túi đồ và trao lại những vật phẩm khác. Tất cả đều là dữ liệu — trạm, công thức và các vật phẩm thường — còn việc mở trạm chỉ cần một dòng kịch bản. Nó chạy trong mọi trò chơi: từ một lựa chọn đối thoại trong tiểu thuyết hình ảnh, một cú bấm vào đồ vật, hay một sự kiện trên bản đồ ô vuông.

## Trong trò chơi

RPG Plus có một bàn chế tạo bên cạnh giếng làng. Người chơi đã gom hai tấm lông sói bạc và ba miếng nhựa sống, rồi chọn Chiếc choàng Người canh rừng:

![Cửa sổ bàn chế tạo bên giếng đè lên bản đồ làng: bên trái là ba công thức — Woodland salve, Warden mantle (đang chọn), Ward lantern; bên phải là hình và mô tả của choàng, INGREDIENTS · owned / needed: Silver wolf fur 2 / 2, Living resin 3 / 2; PRODUCTS: Warden mantle × 1; nút Craft; dưới cùng là "Ingredients are consumed only when you craft. Esc closes."](assets/workbench.jpg)

- **Công thức** ở bên trái; bấm vào một công thức để xem.
- **Nguyên liệu** cho biết bạn có bao nhiêu và cần bao nhiêu.
- **Craft** chỉ khả dụng khi đã có đủ mọi thứ.

Sau khi bấm **Craft**:

![Cùng một bảng sau khi chế tạo: Silver wolf fur 0 / 2, Living resin 1 / 2, và "Done. The product is in your bag."](assets/crafted.jpg)

Túi đồ trước và sau — lông sói biến mất, còn lại một nhựa sống, choàng đến tay:

![Hai hàng ô của túi đồ. Trước: nấm, xu ×25, nhựa sống ×3, lông sói ×2. Sau: nấm, xu ×25, nhựa sống, Warden mantle.](assets/bag-before-after.jpg)

Chế tạo tốn thời gian: đồng hồ trò chơi chạy thêm số phút bằng `Time` của công thức — với chiếc choàng là 10. Chiếc choàng vốn là trang bị (xem Equipment), làm xong mặc ngay được.

## Một trạm và các công thức của nó

Bàn chế tạo của RPG Plus:

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

và một trong các công thức của nó:

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

- Trạm liệt kê công thức theo id, theo thứ tự hiển thị; nó có thể dùng chung công thức với trạm khác.
- `Input` được lấy từ túi đồ, `Output` được thêm vào túi; `Time` tính bằng phút trong game.
- Tên và mô tả là các khóa dịch của mod của bạn.

Người chơi không cần học trước các công thức này: trạm đưa ra toàn bộ công thức của chính nó.

## Mở một trạm

Sự kiện của bàn chế tạo trên bản đồ làng chạy kịch bản sau:

```scenario
ptext "Trên bàn chế tạo có một tờ hướng dẫn: «Không nếm. Không ngửi.»" key=rpg_plus/woodland/workbench/1
action craft.open station=rpg_plus/workbench
end
```

Khi cửa sổ mở, người chơi không thể đi lại; Esc đóng nó lại.

## An toàn ngay từ thiết kế

Một lần chế tạo được kiểm tra trọn vẹn trước khi bất cứ thứ gì thay đổi: mọi nguyên liệu và sản phẩm phải tồn tại, số lượng phải là số dương, và túi đồ phải đủ. Có gì sai thì không lấy gì cả. Vật phẩm chế tạo ra là vật phẩm thường, nên chúng được lưu cùng túi đồ.

## Khi có gì đó không ổn

1. **Craft vẫn màu xám.** Thiếu một nguyên liệu; các con số "có / cần" cho biết là món nào.
2. **Một công thức không có trong cửa sổ.** Trạm nêu trong `Recipes` một id mà không công thức nào có.
3. **Cửa sổ không mở.** id trong `station=` không khớp trạm nào; hãy xem log.
