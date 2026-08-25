---
name: 10report
description: Research 10 new scam/theft/violence reports for Delhi/Gurugram/NCR, format them to match index.html's existing report schema, and append them to index.html for review. Use when the user types "10report" or asks to add a fresh batch of incident reports to the NCR safety map.
---

# 10report

Add 10 new, real, sourced scam/crime reports to the NCR Incident Map (`index.html`) and hand them to the user for review — without committing anything to git.

The map is read-only for site visitors: `index.html`'s `reports` array is the single source of truth, edited only via Claude Code. There is no Firestore, no visitor add/delete UI, and no backend — this skill's whole job is to responsibly edit that one array.

## Report policy: Japanese-language only

**Every new report added by this skill must come from a Japanese-language source and be written in Japanese** (`name` and `notes` fields) — every existing entry in `index.html` is already Japanese-language, and new ones must match that style. This is a hard requirement, not a preference — do not fall back to English-language sources or English-language entries.

## Steps

1. **Read the current `reports` array** in `index.html` (inside the `<script>` block near the bottom). Note every existing `name`, `notes`, `link`, and `video` value — this is the dedupe list. Also note the current highest `id`.

2. **Research 10 new incidents** relevant to Delhi, New Delhi, Gurugram, or the wider NCR (including Noida) — scams, theft, fraud, crime, or violence against residents/tourists. Use WebSearch/WebFetch, searching in Japanese (e.g. `note.com インド デリー 詐欺`, `インド グルガオン 被害`, `インド ノイダ 治安`, `インド デリー 事件`).
   - Draw from Japanese-language sources broadly: note.com posts, other Japanese blogs, Japanese news outlets, embassy/consulate safety advisories in Japanese, etc. — not just note.com.
   - Prefer specific, verifiable sources that describe a concrete incident at a concrete location. Avoid vague trend pieces with no specific place/date.
   - If note.com alone runs dry (its easily-discoverable NCR-crime corpus is finite and gets re-surfaced across runs), broaden the Japanese search terms and source types before ever considering an English source — English sources are not an acceptable substitute per the policy above.

3. **Deduplicate**: skip any candidate that matches an existing entry's location + incident (same `name`/`notes` substance, or same source URL already present as `link`/`video`). If fewer than 10 unique new Japanese-sourced incidents turn up, keep searching rather than padding with duplicates, low-quality items, or non-Japanese sources.

4. **Format each new report** as an object matching the existing schema exactly:
   ```js
   {id: <next sequential id>, name:"<日本語の地名・場所>", type:"scam"|"theft"|"violence", date:"YYYY-MM-DD", notes:"<1〜2文の事実に基づく説明。出典記事のURLを文中に記載>", lat:<number>, lng:<number>, video:"<11-char YouTube ID>" /* if source is a YouTube video */, link:"<source URL>" /* if source is an article/blog, omit when video is set */}
   ```
   - `name` and `notes` must be in Japanese, matching the terse factual style of the existing Japanese entries.
   - `type` must be one of the three existing categories (`scam`, `theft`, `violence`) — pick the closest fit.
   - `lat`/`lng` should be a real, reasonably precise coordinate for the named landmark within the Delhi/Gurugram/NCR bounding box already used by other entries (`lat` ~28.4–28.65, `lng` ~77.0–77.35). Look up the landmark's coordinates rather than guessing wildly.
   - For a YouTube source: extract the 11-character video ID and set `video`, not `link`.
   - For a non-video source: set `link` to the article/post URL, and also mention the source inline in `notes` (e.g. "出典記事: note.com/...").
   - `date` is the date of the report/incident if known, otherwise today's date.
   - Assign `id` values as consecutive integers continuing from the current max id.

5. **Append only** — add the 10 new objects to the end of the `reports` array, after the last existing entry. Do not reorder, edit, or remove any existing entry. Do not touch any other part of the file.

6. **Save index.html permanently** (a real file edit, not a draft) so the map reflects the additions immediately when opened.

7. **Show the user the 10 additions for review** — a concise list (location, type, date, one-line English summary for the user's convenience, source URL) in the chat reply. Don't just say "done."

8. **Do not run any git command** (`add`, `commit`, `push`, etc.) as part of this skill. The user's workflow is: they ask for reports → this skill edits `index.html` → the user reviews and commits/pushes themselves.

## Constraints (hard rules)

- Every new report must be sourced from Japanese-language material and written in Japanese (`name`/`notes`). No English-sourced or English-written entries.
- Never delete or modify an existing report object.
- Never fabricate a report — every entry needs a real, checkable source URL.
- Never commit or push. This skill only edits the working file.
- Never add any visitor-facing add/delete/edit functionality back into `index.html` — the map is permanently read-only for visitors; only this skill (via Claude Code, at the user's request) modifies report data.
