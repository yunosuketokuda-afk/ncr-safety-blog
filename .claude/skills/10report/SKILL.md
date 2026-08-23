---
name: 10report
description: Research 10 new scam/theft/violence reports for Delhi/Gurugram/NCR, format them to match index.html's existing report schema, and append them to index.html for review. Use when the user types "10report" or asks to add a fresh batch of incident reports to the NCR safety map.
---

# 10report

Add 10 new, real, sourced scam/crime reports to the NCR Incident Map (`index.html`) and hand them to the user for review — without committing anything to git.

## Steps

1. **Read the current `reports` array** in `index.html` (inside the `<script>` block near the bottom). Note every existing `name`, `notes`, `link`, and `video` value — this is the dedupe list. Also note the current highest `id`.

2. **Research 10 new incidents** relevant to Delhi, New Delhi, Gurugram, or the wider NCR — scams, theft, or violence against residents/tourists. Use WebSearch/WebFetch. Prefer specific, verifiable sources: news articles, police advisories, established YouTube channels, or blog posts (note.com posts have been used before) that describe a concrete incident at a concrete location. Avoid vague trend pieces with no specific place/date.

3. **Deduplicate**: skip any candidate that matches an existing entry's location + incident (same `name`/`notes` substance, or same source URL already present as `link`/`video`). If fewer than 10 unique new incidents turn up, keep searching rather than padding with duplicates or low-quality items.

4. **Format each new report** as an object matching the existing schema exactly:
   ```js
   {id: <next sequential id>, name:"<Landmark, Area>", type:"scam"|"theft"|"violence", date:"YYYY-MM-DD", notes:"<1-2 sentence factual description, matching the existing terse style>", lat:<number>, lng:<number>, video:"<11-char YouTube ID>" /* if source is a YouTube video */, link:"<source URL>" /* if source is an article/blog, omit when video is set */}
   ```
   - `type` must be one of the three existing categories (`scam`, `theft`, `violence`) — pick the closest fit.
   - `lat`/`lng` should be a real, reasonably precise coordinate for the named landmark within the Delhi/Gurugram/NCR bounding box already used by other entries (`lat` ~28.4–28.65, `lng` ~77.0–77.35). Look up the landmark's coordinates rather than guessing wildly.
   - For a YouTube source: extract the 11-character video ID and set `video`, not `link` (mirrors ids 21–22 in the current file).
   - For a non-video source: set `link` to the article/post URL (mirrors ids 23–24), and it's fine to also mention the source inline in `notes` (e.g. "Source: example.com/...").
   - `date` is the date of the report/incident if known, otherwise today's date.
   - Assign `id` values as consecutive integers continuing from the current max id.

5. **Append only** — add the 10 new objects to the end of the `reports` array, after the last existing entry. Do not reorder, edit, or remove any existing entry. Do not touch any other part of the file.

6. **Save index.html permanently** (a real file edit, not a draft) so the map reflects the additions immediately when opened.

7. **Show the user the 10 additions for review** — a concise list (location, type, date, one-line summary, source URL) in the chat reply. Don't just say "done."

8. **Do not run any git command** (`add`, `commit`, `push`, etc.) as part of this skill. Wait for the user to explicitly approve before any commit/sync happens.

## Constraints (hard rules)

- Never delete or modify an existing report object.
- Never fabricate a report — every entry needs a real, checkable source URL.
- Never commit or push. This skill only edits the working file.
