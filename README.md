# Arena Breakout — Item Database

## Purpose

This repository contains the current canonical Arena Breakout item dataset.

Directory structure
```
.
├── helmets.json
├── helmet_shields.json
├── ballistic_vests.json
├── chest_rigs.json
├── headsets.json
├── masks.json
├── food.json
├── injections.json
├── medical.json
├── throwables.json
└── assets/
    ├── helmet/
    │   ├── helmet_001.png
    │   └── ...
    ├── helmet_shield/
    │   ├── helmet_shield_001.png
    │   └── ...
    ├── ballistic_vest/
    │   ├── ballistic_vest_001.png
    │   └── ...
    ├── chest_rig/
    ├── headset/
    ├── mask/
    ├── food/
    ├── medical/
    └── throwable/
```

## General JSON rules

- JSON is the canonical data source.
- Every item must have a unique, stable ID inside its category.
- Do not reuse an existing ID.
- Do not silently deduplicate items with the same display name; different prices or variants may represent different items.
- Preserve the item's exact displayed name as closely as possible.
- Prices must be stored as numeric values, not formatted strings.
- Image paths must point to the corresponding asset included in the repository.
- Keep category-specific fields instead of forcing every category into one artificial schema.
- Do not add fields based on assumptions. If a value is not visible or cannot be established, use the existing convention for that category, ask for the user or leave it absent/null.
- Chest rigs have additional geometry because their closed and opened inventory shapes can differ.

Use:
```
{
  "id": "chest_rig_XXX",
  "type": "chest_rig",
  "name": "...",
  "closed_size": {"width": 1, "height": 3},
  "open_size": {"width": 2, "height": 3},
  "internal_storage": {
    "grid_width": 2,
    "grid_height": 3,
    "layout": [[1,2],[3,4],[3,4]],
    "slot_count": 6,
    "compartments": [
      {"id": 1, "width": 1, "height": 1, "slot_count": 1}
    ]
  }
}
```

The internal layout may be irregular or disconnected. Do not assume that storage is always a simple rectangle.

Do not register a new chest rig until its closed size, open size, and internal layout have been confirmed from the screenshot.

## Image rules

For every newly registered item:

- Use the screenshot supplied for the item.
- Crop the item image from the screenshot.
- Remove unnecessary UI/background when possible while preserving the actual item artwork.
- Save the result as PNG.
- Use the category-specific naming convention:

-- helmet_XXX.png
-- helmet_shield_XXX.png
-- ballistic_vest_XXX.png
-- chest_rig_XXX.png
-- headset_XXX.png
-- mask_XXX.png
-- food_XXX.png
-- injection_XXX.png
-- medical_XXX.png
-- throwable_XXX.png

Make sure the JSON points to the exact file.

## Adding a new item

Recommended workflow:

1) Receive the screenshot.
2) Identify the item name, category, price, dimensions and any visible special properties.
3) Check the existing JSON for duplicates or variants.
4) Assign the next unused ID in that category.
5) Crop and save the item PNG.
6) Add the item to the correct JSON.
7) Verify that the JSON image path exists.
8) Verify that the image filename and item ID match.
9) Do not modify unrelated categories.
10) Prompt for adding more items

## Copy/paste this prompt when continuing the database:

We are continuing the Arena Breakout item database.

IMPORTANT SOURCE RULE:
Use ONLY the data and assets already present in this current project/repository plus the new screenshots I provide in this conversation. Do NOT use data from previous conversations, historical databases, old backups, items.json, old CSVs, or any other external/historical item list unless I explicitly ask for it.

For each new screenshot:
1. Identify the item and its category.
2. Extract the exact visible name and price.
3. Determine the inventory dimensions and category-specific properties only when supported by the screenshot.
4. Check the corresponding JSON for an existing item/variant before creating a new ID.
5. Assign the next unused ID for that category.
6. Crop the item's image from the screenshot and save it as PNG in the correct assets/<category>/ folder.
7. Add the new record to the correct JSON.
8. Make the JSON asset/image path point to the exact PNG that was created.
9. Do not modify unrelated categories.
10. Do not import or reconstruct historical items.
11. After processing, verify that every new JSON record has a corresponding image file.

For chest rigs/non-bindable vests, DO NOT finalize uncertain geometry. First propose:
- closed size
- open size
- internal grid
- internal compartment layout
Then wait for my confirmation before registering the final item.

JSON is the canonical source of truth. Keep IDs stable and never reuse an existing ID.

When you finish, report:
- category updated
- item name
- assigned ID
- price
- dimensions
- image path
- whether the item was new or a duplicate/variant
- total item count in that category

## Quality checklist

Before creating a backup or release:

Every JSON item has a valid ID.
Every referenced image exists.
No image is accidentally assigned to another item.
Duplicate screenshots are not counted as new items.
Same-name items with different prices/variants are preserved when applicable.
Chest-rig geometry has been confirmed before final registration.
