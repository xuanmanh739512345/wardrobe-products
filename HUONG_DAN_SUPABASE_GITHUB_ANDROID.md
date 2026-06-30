# Supabase + GitHub images cho app tủ đồ

Nguồn dữ liệu: `tong_hop.xlsx`.

## 1. Tạo bảng Supabase

Trong Supabase Dashboard -> SQL Editor, chạy file được tạo ra:

```sql
supabase_export/supabase_products.sql
```

Bảng chính:

```text
public.products
- product_id text primary key
- name text
- category text
- image_url text
- shopee_url text
- tiktok_url text
- amazon_url text
- active boolean
- sort_order integer
```

Policy đọc public chỉ cho sản phẩm `active = true` đã có trong SQL.

## 2. Đưa ảnh lên GitHub

Tạo repo GitHub public, ví dụ `wardrobe-products`, rồi tạo thư mục `images/`.

Chạy script để copy/đổi tên ảnh vào repo GitHub local:

```bash
cd '/run/media/manh/PJ/DU AN/ANDOID THAY QUAN AO Vr2/AFFFLIATE'
python3 scripts/export_products_for_supabase.py \
  --image-base-url 'https://raw.githubusercontent.com/USER/wardrobe-products/main/images' \
  --copy-images-to '/duong/dan/toi/repo/wardrobe-products/images'
```

Sau đó vào repo GitHub local:

```bash
git add images
git commit -m 'Add product images'
git push
```

## 3. Tạo file SQL/CSV/JSON không copy ảnh

Nếu ảnh đã có trên GitHub/CDN, chỉ cần tạo dữ liệu:

```bash
cd '/run/media/manh/PJ/DU AN/ANDOID THAY QUAN AO Vr2/AFFFLIATE'
python3 scripts/export_products_for_supabase.py \
  --image-base-url 'https://raw.githubusercontent.com/USER/wardrobe-products/main/images'
```

Kết quả nằm trong:

```text
supabase_export/products.csv
supabase_export/products.json
supabase_export/supabase_products.sql
supabase_export/summary.txt
```

## 4. Android đọc Supabase

Query app nên dùng:

```sql
select product_id, name, category, image_url, shopee_url, tiktok_url, amazon_url, sort_order
from public.products
where active = true
order by sort_order asc;
```

App cache offline bằng Room hoặc DataStore JSON. Khi bấm mua hàng, mở link ưu tiên theo thứ tự: `shopee_url`, `tiktok_url`, `amazon_url`.
