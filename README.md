# Combined Training Deck Planning Hub

A single-page tool for generating both **Requisitioner** and **Administrator** Amazon Business training decks. Merge or generate decks independently from one unified interface.

## Files

| File | Description |
|------|-------------|
| `index.html` | The combined hub — open this in any modern browser |
| `jszip.min.js` | JSZip library (vendored, required for PPTX manipulation) |

## How to Use

### 1. Open the Hub

Double-click `index.html` to open it in your browser. No server required — everything runs locally in the browser.

### 2. Section 1 — Decks to Generate

Check one or both:
- **Requisitioner Deck** — for buyer/end-user training
- **Administrator Deck** — for account admin training

### 3. Section 2 — Source Decks

Upload the corresponding source PPTX file for each deck you selected:
- `Requisitioner Training_December2025.pptx` (for Requisitioner)
- `Administrator Training_December2025.pptx` (for Admin)

These files are **never uploaded or modified**. All processing happens locally in your browser.

### 4. Section 3 — Customer Details

- **Customer Name** *(required)*: Used for filename generation and placeholder replacement
- **Categories in Scope**: Requisitioner deck only — replaces `{{CATEGORY}}`
- **Customer Logo**: Optional — placed in the top-right corner of the title slide

### 5. Section 4 — Ingress Configuration

Select the access method(s):

| Option | Description |
|--------|-------------|
| Direct Access | Users log in directly at business.amazon.com |
| Punchout | Users access through an eProcurement system (Coupa, Ariba, or Custom) |
| Both | Deck includes slides for both scenarios |

**SSO**: Toggle On/Off per ingress type. When On, additional fields appear for SSO links and screenshots (Admin deck only).

**Account Authority / AUC**: Requisitioner deck only — controls which registration scenario slides are included.

### 6. Section 5 — Feature Toggles

Badges indicate which deck each toggle applies to:
- **Shared** — affects both decks
- **Req** — Requisitioner deck only
- **Admin** — Administrator deck only

Features with "No" selected will have their corresponding slides removed from the generated deck.

**Admin-only toggles with sub-fields:**
- **BOI** — upload a BOI settings screenshot (Admin Slide 13)
- **BPO** — sub-toggles for Recurring Budgets, Individual Allocation, Approval Workflow; plus budget amount fields

**Shared toggles with sub-fields:**
- **Guided Buying Preferred** — Preferred ASINs/link (Req) + Preferred Items/messaging (Admin)
- **Guided Buying Restricted/Blocked** — category lists and messaging for Admin Slides 31–32
- **BPO** — budget values (XX Budgets, $$ amounts, date range) used in both decks

### 7. Section 6 — Requisitioner-Specific Fields

Only shown when Requisitioner deck is selected:
- Customer Process Changes (Slide 3) — supplier type, example items, access method note
- Resource Hub Screenshot (Slide 95)

### 8. Section 7 — Administrator-Specific Fields

Only shown when Administrator deck is selected:
- Spend Visibility Dashboard Screenshot (Admin Slide 59)

### 9. Section 8 — Customer Resources

Resource links used in both decks (Req Slide 96, Admin Slide 66).

### 10. Section 9 — Output Options

- **Generate HTML Preview Guide** — Requisitioner deck only — a lightweight HTML summary for trainer reference

### 11. Generate

Click **⚡ Generate Deck(s)**. The hub will:

1. Run the Requisitioner pipeline if selected (downloading the PPTX automatically)
2. Run the Admin pipeline if selected (downloading after a brief delay to avoid browser popup blocking)
3. Show both slide checklists inline below the form

## Download Buttons

After generation, the results section shows:
- **Download PPTX** buttons for each generated deck (re-download without re-generating)
- **Checklist** buttons to download HTML slide checklists
- **Preview Guide** button (if enabled, Req deck only)

## Slide Checklists

Each checklist shows:
- **Included Slides**: Output number, original number, title, substitutions made
- **Removed Slides**: Original number and reason for removal
- Manual task slides (flagged with ⚠️)

## Form Persistence

Form values are automatically saved to `localStorage` between sessions. A "Form restored from your last session" banner appears when values are restored. File uploads are **not** persisted (for security).

## Engine Code

The hub includes the following shared engines (defined once, used by both pipelines):

| Engine | Purpose |
|--------|---------|
| `SlideFilterEngine` | Evaluates `REQ_SLIDE_FILTER_RULES` / `ADMIN_SLIDE_FILTER_RULES` to compute which slides to remove |
| `NotesDrivenFilterEngine` | Reads `[HUB:REMOVE-IF:...]` tags from slide speaker notes for version-agnostic filtering |
| `PptxManipulator` | Removes slides from the ZIP archive and performs token substitution |
| `ReqPlaceholderEngine` | Builds the token → value map for the Requisitioner deck |
| `AdminPlaceholderEngine` | Builds the token → value map for the Administrator deck |
| `ChecklistGenerator` | Generates the HTML slide checklist report |
| `StorageManager` | Persists form state to localStorage |
| `DownloadHelper` | Triggers file downloads |

## Notes-Driven Filtering

Both decks support `[HUB:REMOVE-IF:condition]` tags in PowerPoint slide speaker notes. This allows slide removal rules to travel with the deck itself, making hub code updates unnecessary when slides are reordered.

Example tags:
```
[HUB:REMOVE-IF:ingress!=punchout]
[HUB:REMOVE-IF:feature.approvals=false]
[HUB:REMOVE-IF:sso=false]
```

## Troubleshooting

**"Source Deck PPTX is required"** — Make sure you've selected the correct PPTX file for each checked deck in Section 2.

**Download doesn't start** — Check that your browser isn't blocking multiple file downloads. Click the individual download buttons in the results section to re-download.

**Slides not being removed as expected** — Verify the feature toggles are set correctly. Check the slide checklist to see exactly which slides were removed and why.

**Custom eProcurement system** — Slides 49–52 (Coupa/Ariba specific) will be removed. You'll need to manually add slides for your custom eProcurement system.
