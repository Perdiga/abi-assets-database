# ABI Assets Database

A versioned, image-backed item database for **Arena Breakout**. Each category is stored in a standalone JSON file, with its item artwork under `assets/`. The included `index.html` is a lightweight, searchable catalog for browsing the data.

## What's in this repository

- JSON is the canonical source of item metadata.
- PNG files in `assets/<category>/` are the canonical item artwork.
- `index.html` loads the catalog directly in the browser; it has no build step or dependencies.
- Item names and prices reflect the source material used to register each entry (currently Portuguese-language data).

The repository currently includes backpacks, ballistic rigs and vests, chest rigs, food, headsets, helmet shields, helmets, keys, masks, medical items, throwables, and miscellaneous items.

## Browse the catalog locally

`index.html` fetches the JSON files, so open it through a local HTTP server rather than double-clicking the file.

With Python installed:

```powershell
python -m http.server 8000
```

Then open <http://localhost:8000>. The page supports text search, filtering by type, and sorting by ID, name, or price.

## Repository layout

```text
.
├── index.html                 # Static catalog viewer
├── backpacks.json             # One JSON array per item category
├── ballistic_rigs.json
├── ballistic_vests.json
├── chest_rigs.json
├── food.json
├── headsets.json
├── helmet_shields.json
├── helmets.json
├── keys.json
├── masks.json
├── medical.json
├── throwables.json
├── varieties.json
└── assets/
    └── <category>/<category>_NNN.png
```

When adding a category, add its JSON filename to `JSON_FILES` in `index.html`; otherwise it will not appear in the catalog.
At present, `backpacks.json` is stored in the repository but is not included in that viewer list.

## Data model

Every JSON file contains an array of item records. Fields shared by the current categories are:

```json
{
  "id": "ballistic_vest_001",
  "type": "ballistic_vest",
  "name": "Peitoral à Prova de Balas Clássico do Engenheiro",
  "price": 7650,
  "asset_path": "assets/ballistic_vest/ballistic_vest_001.png",
  "width_slots": 3,
  "height_slots": 4,
  "width_slots_open": null,
  "height_slots_open": null,
  "slots": null,
  "headset_compatible": null,
  "mask_compatible": null,
  "shield_compatible": null
}
```

`width_slots` and `height_slots` describe the item's closed inventory footprint. Open dimensions and `slots` are used only where applicable, such as containers or rigs. Compatibility fields are nullable when the information is unavailable or does not apply. Keep category-specific information in that category's records; do not invent values to force a universal schema.

## Adding an item

1. Use only the screenshots supplied for the current work and the data already in this repository, unless a different source is explicitly approved.
2. Identify the item category, exact displayed name, price, visible dimensions, and supported properties.
3. Search the target JSON for an existing item or a same-name variant. Preserve legitimate variants; do not silently deduplicate them.
4. Assign the next unused, zero-padded category ID (for example, `helmet_041`). IDs are stable and must never be reused.
5. Crop the item artwork from the supplied screenshot, remove unnecessary UI/background where possible, and save it as PNG at `assets/<category>/<category>_NNN.png`.
6. Add the record to the appropriate JSON file and make `asset_path` match the PNG exactly.
7. Confirm the edited JSON parses and the referenced image exists. Avoid unrelated data changes.

For chest rigs and non-bindable vests, confirm closed size, open size, and the internal storage layout before recording uncertain geometry. Layouts may be irregular or disconnected; do not replace them with an assumed rectangle.

## Data conventions

- Use valid, consistently formatted JSON; prices are numbers, never formatted strings.
- Preserve the display name as closely as the source allows.
- Keep IDs unique within their category and aligned with their asset filename.
- Include only fields supported by the source material. Follow the target category's existing `null`/omission convention for unknown values.
- Put assets in the matching category directory and use PNG format.
- Do not import or reconstruct data from old databases, backups, CSVs, or prior conversations unless explicitly requested.

## Validation

After an edit, parse the updated JSON and check the new item's `asset_path`. This PowerShell command parses every category file:

```powershell
Get-ChildItem -File *.json | ForEach-Object {
  Get-Content $_.FullName -Raw | ConvertFrom-Json | Out-Null
  "OK: $($_.Name)"
}
```

Before a release, also verify that every record has an ID, every referenced image exists, IDs have not been reused, and duplicate screenshots have not been registered as new items.

## Continuing data-entry work

When processing new screenshots, report the category, item name, assigned ID, price, dimensions, image path, whether the result was new or a duplicate/variant, and the category's updated item count.
