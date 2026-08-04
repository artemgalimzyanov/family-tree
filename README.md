# Family Tree Viewer (Reborn)

A zero-dependency, single-file family tree app. Open `index.html` in any modern
browser (double-click, no server needed), drop your JSON file on the page, and
explore an interactive, zoomable, crossing-free tree.

## Quick start

1. Open `index.html`.
2. Drag & drop your data file (or click **Upload JSON**).
3. Pan by dragging, zoom with the mouse wheel, use **Fit** / **Center** in the toolbar.

`family-data-format.json` in this folder is a working example (real data, format v2).

## Data format (v2)

```json
{
  "family": {
    "name": "Bob",
    "anchor": "bob",
    "members": [
      {
        "id": "123",
        "firstName": "John",
        "lastName": "Smith",
        "birthDate": "2000-01-01",
        "deathDate": null,
        "gender": "male",
        "parents": ["Jacob", "Helene"],
        "partners": ["Alice"],
        "photo": null,
        "notes": ""
      }
    ]
  }
}
```

| Field | Notes |
|---|---|
| `id` | Unique string. Referenced by other members' `parents` / `partners`. |
| `birthDate` | `YYYY-MM-DD`, `YYYY`, or approximate `1910?`. `""` if unknown. |
| `deathDate` | Same formats. `null` = living, `""` = deceased, date unknown. |
| `gender` | `male` / `female` / `other` — sets the card's colour accent. |
| `parents` | 0–2 parent ids. Couples are **inferred automatically** from shared children. |
| `partners` | Optional. Only needed for childless couples. |
| `anchor` | Optional (`family.anchor`). The person the layout is centred on: their father's side goes left, mother's side right. Falls back to `artem`, then to the member with the deepest ancestry. |

v1 files (without `anchor`/`partners`) load unchanged; empty or unknown ids in
`parents` are dropped with a notice banner.

## Layout

The layout follows `../ideas/rules.md`:

- strict generation rows (grandparents → parents → children…);
- couples joined by a horizontal line with a **centre dot**; one **trunk** drops
  from the dot and fans out to each child with symmetric curves — geometry that
  cannot produce crossings;
- unit order comes from a recursive genogram construction around the anchor
  (father's ancestry left of each couple, mother's right, collateral branches on
  the outer side), then coordinates are compacted with barycentric passes + PAVA
  isotonic regression, with tighter gaps between siblings than between branches.

## Features

- **Collapse / expand** — the −/+ chip on any card with children hides that whole
  branch (the direct line to the anchor always stays visible). Toolbar has
  **Expand all / Collapse all**.
- **Detail panel** — click a card: full dates, notes, and clickable chips for
  parents / partner / children that navigate the tree.
- **Editing** — **＋ Add member**, or **Edit** / **Delete** from the panel.
  IDs are generated automatically (transliterated first name, then last name /
  birth year on collision). Edits live in memory until you press
  **Download JSON** (an orange dot marks unsaved changes).
- **Pan / zoom** — drag, wheel (zooms to cursor), − / ＋ / Fit / Center buttons.

## Files

```
family-tree-reborn/
├── index.html               # the whole app (HTML + CSS + JS, no dependencies)
├── family-data-format.json  # example / reference data (v2)
└── README.md
```
