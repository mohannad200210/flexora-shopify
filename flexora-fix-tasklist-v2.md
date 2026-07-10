# Flexora — Verified Fix Tasks (grounded in actual repo contents)
Repo: github.com/mohannad200210/flexora-shopify
Working folder: `UltimateThemeRed/`

Give this whole file to Claude Code with instructions to: read each task, make the edit, run `git add -A && git commit -m "<task description>"` after each one, and `git push` at the end (or after each commit — either is fine). No further investigation needed, exact paths/keys are below.

---

## Task 1 — Fix homepage/catalog showing fake placeholder products
**File:** `templates/index.json`
**Key:** `sections.featured_products.settings.collection` — currently `""`
**Fix:** Set it to the handle of the real Flexora product's collection (likely `"all"` if no custom collection exists — confirm the actual collection handle from Admin → Collections, or via `flexora-products.csv` in repo root if it lists a collection).

## Task 2 — Sync header-group announcement bar + nav to Arabic
**File:** `sections/header-group.json`
**Key:** `sections.announcement_bar_EfhBPn.blocks.announcement_67kxVr.settings.text`
Currently: `"Free shipping for all order above £30"`
Replace with something consistent with the homepage's tone, e.g.:
`"🚚 شحن مجاني داخل جميع محافظات الأردن — 💵 الدفع عند الاستلام"`

Also in this same file, check `sections.header.settings.mobile_menu_title` (currently `"Menu"`) — translate to `"القائمة"`.

The actual **nav item labels** ("Home", "Catalog", "Contact") are not in this file — they come from the "main-menu" navigation, which is store data (Admin → Online Store → Navigation), not theme code. Flag this back to the user; it needs to be edited in Admin (rename to الرئيسية / المتجر / تواصل معنا) or via the Shopify Admin API if you have API access configured.

## Task 3 — Remove leftover theme-vendor branding in footer
**File:** `sections/footer-group.json`
**Key:** `sections.footer.settings.branding_text` — currently `"Powered by Ecomplaybook"`
**Fix:** Set to `""` (empty) to remove it, or replace with `"© Flexora"`.

Also check `sections/footer.liquid` line ~1158 — that's just the schema *default* for new footer sections, lower priority, but update it too for consistency (change default to `""`).

## Task 4 — Fix cart drawer upsell blocks referencing a different store's products
**File:** `config/settings_data.json`
**Path:** `current.sections["cart-drawer"].blocks` → two blocks of `"type": "product_upsells"`
(`product_upsells_6DyfY8` and `product_upsells_MfMY9m`)
Both have `product_list` arrays full of handles from an unrelated demo store.

**Fix, pick one depending on catalog size:**
- If Flexora only has 1–2 products total: **disable both blocks** — add `"disabled": true` to each block object (sibling of `"type"`). An upsell drawer showing nothing meaningful, or the same product being purchased, looks broken. Re-enable once there are 3+ products worth cross-selling.
- If there are already 3+ real products: replace `product_list` values with real Flexora product handles (get exact handles from Admin → Products → each product's URL slug, or from `flexora-products.csv`/`product_template_sample.csv` in the repo root if populated).

## Task 5 — Confirm/clean up remaining demo content
Search the whole theme for other leftover demo-store references, since Task 4 revealed the pattern (old store's product handles baked into JSON configs):
```bash
grep -rn "sweat-minimizing-gel\|thepatchbrand\|whole-body-antiperspirant\|laundry-detergent-eco-sheets\|untitled-nov3" .
```
Fix or disable any other blocks that reference these same leftover handles (check `sections/*-group*.json` and `config/settings_data.json` broadly, not just cart-drawer).

## Task 6 — Product images (Admin task, not code)
In Admin → Products → the real product → Media: remove `Screenshot_2026-07-07_*.png` files from the gallery (raw phone screenshots, unprofessional next to the AI-generated/lifestyle photos). Keep the numbered lifestyle images and Gemini-generated ones.

## Task 7 — Inventory (Admin task, not code, but still blocking sales — do this regardless of what else gets done)
In Admin → Products → the real product → each variant (S/M/L/XL/XXL): set a real stock quantity, or turn off "Track quantity" if you don't want inventory limits. Currently all variants show sold out, so nothing can be purchased.

## Task 8 — Publish Arabic as the store's language (Admin task)
Admin → Settings → Languages → add **Arabic**, publish it, and set it as default (or default for the Jordan market if using Shopify Markets). The theme code (`ar.json`, `dir="rtl"`) is already fully ready for this — it's purely an Admin toggle away from taking effect sitewide.

---

## After Claude Code finishes Tasks 1–5 (the code tasks)
1. Push the theme to a **preview/unpublished theme** first (`shopify theme push --unpublished --store=flexora-10124.myshopify.com`), not directly to live, so it can be checked before going live.
2. Send me the preview URL (Admin → Themes → your pushed theme → Preview) and I'll verify the fixes render correctly.
3. Then do Tasks 6–8 in Admin, and publish.
