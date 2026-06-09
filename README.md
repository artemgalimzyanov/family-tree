# Family Tree Viewer

A zero-dependency, single-file browser app for visualizing family trees. Load a JSON data file and explore an interactive, zoomable tree with clickable member cards.

---

## Quick Start

Open `index.html` in any modern browser — no server required. Click **Upload JSON**, select your data file, and the tree renders immediately.

---

## Building Your JSON File

Create a `.json` file with this top-level structure:

```json
{
  "family": {
    "name": "Your Family Name",
    "members": [ ... ]
  }
}
```

### Member Object

Each person in the `members` array is a flat object:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | yes | Unique identifier (e.g. `"john-doe"`). Referenced by other members in their `parents` array. |
| `firstName` | string | yes | First name |
| `lastName` | string | yes | Last / family name |
| `birthDate` | string | no | ISO date `YYYY-MM-DD`, partial year `YYYY`, or approximate `1910?`. Use `""` if unknown. |
| `deathDate` | string \| null | no | Same format as `birthDate`. Use `null` (not `""`) for living people; `""` if the date is unknown but the person has died. |
| `gender` | string | yes | `"male"`, `"female"`, or `"other"` |
| `parents` | string[] | yes | Array of parent `id` values. Use `[]` for root ancestors with no known parents. |
| `photo` | string \| null | no | Relative path or URL to a portrait image (currently reserved for future use). |
| `notes` | string | no | Free-text notes — occupations, locations, historical context, etc. |

### Minimal Example

```json
{
  "family": {
    "name": "Smith",
    "members": [
      {
        "id": "john",
        "firstName": "John",
        "lastName": "Smith",
        "birthDate": "1950-03-15",
        "deathDate": null,
        "gender": "male",
        "parents": [],
        "photo": null,
        "notes": ""
      },
      {
        "id": "mary",
        "firstName": "Mary",
        "lastName": "Smith",
        "birthDate": "1952-07-22",
        "deathDate": null,
        "gender": "female",
        "parents": [],
        "photo": null,
        "notes": ""
      },
      {
        "id": "alice",
        "firstName": "Alice",
        "lastName": "Smith",
        "birthDate": "1978-11-05",
        "deathDate": null,
        "gender": "female",
        "parents": ["john", "mary"],
        "photo": null,
        "notes": ""
      }
    ]
  }
}
```

### How Relationships Work

- **Parents → children**: list parent IDs in a child's `parents` array — the app derives the full tree from these references.
- **Couples**: inferred automatically — two people become partners when they share at least one child.
- **Single parents**: list only one ID in `parents`.
- **Root ancestors**: set `parents` to `[]`.

---

## Features

### Canvas Navigation
- **Pan**: drag on any empty area of the canvas
- **Zoom**: scroll wheel (zooms toward cursor), or use the toolbar buttons
- **Reset View**: fits the entire tree into the viewport

### Tree Layout
- Generations are arranged in horizontal rows, oldest ancestors at the top
- Couples are placed side-by-side with a connecting line
- Siblings are grouped under their parents, sorted by birth year
- The layout algorithm automatically resolves crossing edges across complex multi-branch trees

### Collapse / Expand
- Each card with children shows a **−/+** button in the top-right corner
- Click it to hide or show that person's entire subtree
- **Expand All / Collapse All** buttons apply globally

### Member Detail Panel
Click any card to open a side panel showing:
- Full name, gender badge
- Birth and death dates
- Parents, partner(s), and children as clickable navigation buttons
- Notes

Clicking a relative button in the panel navigates to and highlights that person's card.

### Gender Colour Coding
- Blue border / dot — male
- Pink border / dot — female
- Cards highlighted in blue when selected

---

## Data Tips

- IDs must be unique strings across the entire file; letters, digits, and hyphens work well.
- Dates are displayed as a range (e.g. `1950–2020`) or `b. 1950` for living people.
- Approximate dates like `"1910?"` are accepted and displayed as-is.
- A person with an unknown death date but confirmed deceased: set `deathDate` to `""`.
- A living person: set `deathDate` to `null` — the death section is hidden in the panel.
- `parents` IDs that don't match any member in the file are silently ignored.

---

## File Structure

```
family-tree/
├── index.html              # Complete self-contained app (HTML + CSS + JS)
├── family-data-format.json # Reference / example data file

```

The entire viewer lives in `index.html` — no build step, no npm, no server needed.
