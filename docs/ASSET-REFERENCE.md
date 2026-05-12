# Asset Reference — OPTCG API & Card Images

## Primary Source: OPTCG API

**Website:** https://optcgapi.com
**Base URL:** `https://optcgapi.com/api/`
**Auth:** None required (free, open API)
**Format:** JSON responses
**Coverage:** Sets OP-01 through OP-14, Starter Decks ST-01 through ST-28, Promo cards

---

## Key Endpoints

### Get a specific card
```
GET https://optcgapi.com/api/sets/card/{card_id}/
```
Returns a JSON array (may include parallel/alt-art variants). Use the first entry for the standard version.

### Get all cards in a set
```
GET https://optcgapi.com/api/sets/{set_id}/
```
The `set_id` uses the format from the card data's `set_id` field: `OP-01`, `OP-02`, ..., `OP-14`.
Returns a JSON array of all cards in that set. Each card entry follows the same schema as the single-card endpoint. A set typically contains 50–130+ cards. The response includes all variants (standard + parallel + alt art) as separate entries.

### Get all sets
```
GET https://optcgapi.com/api/allSets/
```
Returns a list of all available sets. Use this to populate a set selector dropdown.

### Available Set IDs
| Set ID | Set Name |
|--------|----------|
| OP-01 | Romance Dawn |
| OP-02 | Paramount War |
| OP-03 | Pillars of Strength |
| OP-04 | Kingdoms of Intrigue |
| OP-05 | Awakening of the New Era |
| OP-06 | Wings of the Captain |
| OP-07 | 500 Years in the Future |
| OP-08 | Two Legends |
| OP-09 | Emperors in the New World |
| OP-10 | Royal Blood |
| OP-11 | A Fist of Divine Speed |
| OP-12 | Legacy of the Master |
| OP-13 | Carrying On His Will |
| OP-14 | The Azure Sea's Seven |

### Starter deck cards
```
GET https://optcgapi.com/api/decks/card/{card_id}/
```

---

## Card ID Format

Card IDs follow the pattern: `{SET}{NUMBER}`

**Examples:**
| Card ID | Card Name | Set |
|---------|-----------|-----|
| OP01-001 | Roronoa Zoro (Leader) | Romance Dawn |
| OP01-120 | Monkey.D.Luffy | Romance Dawn |
| OP02-001 | Cavendish (Leader) | Paramount War |
| OP05-119 | Monkey.D.Luffy (SR) | Awakening of the New Era |
| ST01-001 | Monkey.D.Luffy (Leader) | Straw Hat Crew Starter |

---

## Image URLs

Card images follow a predictable URL pattern:
```
https://optcgapi.com/media/static/Card_Images/{card_id}.jpg
```

**Examples:**
- `https://optcgapi.com/media/static/Card_Images/OP01-001.jpg`
- `https://optcgapi.com/media/static/Card_Images/OP01-001_p1.jpg` (Parallel/alt art variant)

These image URLs are directly accessible in browsers via `<img>` tags (no CORS issues for images).

---

## Example API Response

```json
[
  {
    "inventory_price": 2.25,
    "market_price": 2.68,
    "card_name": "Roronoa Zoro (001)",
    "set_name": "Romance Dawn",
    "card_text": "[DON!! x1] [Your Turn] All of your Characters gain +1000 power.",
    "set_id": "OP-01",
    "rarity": "L",
    "card_set_id": "OP01-001",
    "card_color": "Red",
    "card_type": "Leader",
    "life": "5",
    "card_cost": null,
    "card_power": "5000",
    "sub_types": "Straw Hat Crew Supernovas",
    "counter_amount": null,
    "attribute": "Slash",
    "date_scraped": "2026-03-09",
    "card_image_id": "OP01-001",
    "card_image": "https://optcgapi.com/media/static/Card_Images/OP01-001.jpg"
  },
  {
    "card_name": "Roronoa Zoro (001) (Parallel)",
    "rarity": "L",
    "card_set_id": "OP01-001",
    "card_image_id": "OP01-001_p1",
    "card_image": "https://optcgapi.com/media/static/Card_Images/OP01-001_p1.jpg"
  }
]
```

### Key Fields for Our Prototypes
| Field | Usage |
|-------|-------|
| `card_name` | Display below the card |
| `card_set_id` | Display below the card, also used as input for loading cards |
| `set_name` | Display context |
| `rarity` | Display + determines if holo effect applies (Experiment 2) |
| `card_color` | Potential theming (glow color, etc.) |
| `card_type` | Leader / Character / Event / Stage |
| `card_power` | Display info |
| `card_image` | The card image URL |

---

## CORS Handling

The API JSON endpoints may block direct browser `fetch()` calls due to CORS policy. Strategy:

1. **Try the fetch** — it may work depending on their current CORS config
2. **If blocked:** Load card images directly via `<img src="...">` (images are cross-origin accessible)
3. **Construct image URL manually:** `https://optcgapi.com/media/static/Card_Images/{card_id}.jpg`
4. **Display fallback:** Show the card ID as the label instead of the full card name

---

## Official Reference Site

**https://en.onepiece-cardgame.com**
- Card database: https://en.onepiece-cardgame.com/cardlist/
- Product pages with booster pack art: https://en.onepiece-cardgame.com/products/
- Use this for visual reference on card design, booster pack designs, and official branding
- Card images on the official site use lazy loading and are harder to hotlink — prefer the API images

---

## Good Test Cards

A handful of cards useful for testing across different types and rarities:

| Card ID | Name | Type | Rarity | Notes |
|---------|------|------|--------|-------|
| OP01-001 | Roronoa Zoro | Leader | L | Default test card |
| OP01-120 | Monkey.D.Luffy | Character | SR | Iconic art, good holo candidate |
| OP02-001 | Cavendish | Leader | L | Different color (Green) |
| OP05-119 | Monkey.D.Luffy | Character | SR | Popular alt art |
| ST01-001 | Monkey.D.Luffy | Leader | L | Starter deck leader |
