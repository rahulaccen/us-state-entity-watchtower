# Daily US State Entity Watchtower workflow

## Purpose
At 8:00 AM America/New_York each day, produce a concise, evidence-based briefing covering news published during the immediately preceding 24 hours for every row in `entities.json`. Publish the same result to `rahulaccen/us-state-entity-watchtower` on branch `main` as both `data/news.json` (latest) and `data/archive/YYYY-MM-DD.json` (dated archive, using the America/New_York date of `window.end`).

## Inputs and canonical identity
- Canonical entity file: `/tasklet/agent/home/state-entity-news/entities.json`.
- Preserve every workbook row and its exact `name`, `state`, and `category`. Never silently normalize or rename an entity in output.
- Search repeated names only once, but map a retained item to the exact canonical name and the category most applicable to the story. The directory remains the complete record of all listed rows.

## Research process
1. Calculate an exact rolling 24-hour window ending at run time in America/New_York. Record ISO timestamps in `window.start` and `window.end`.
2. Search every unique entity name. Use quoted exact-name searches plus its state, common acronym when unambiguous, and category or function when necessary to disambiguate.
3. Check source classes in this order:
   - official agency, government, regulator, legislature, procurement, court, and company newsroom sources;
   - reliable local or state reporting;
   - reputable national reporting and trade publications;
   - primary filings and official public notices.
4. A story qualifies only if its publication timestamp falls in the window and it either directly names the entity or documents an event with a clear, material effect on it. Broad sector commentary, search snippets without article support, job posts, generic marketing, undated pages, and speculative mentions do not qualify.
5. Open and verify each retained source page. Do not rely only on search-result snippets. If the date, claim, entity match, or material impact cannot be verified, exclude it.

## Deduplication
- Consolidate syndicated copies, press-release rewrites, URL variants, and multiple reports of the same underlying event.
- Prefer the primary source when complete; otherwise prefer the strongest independent reporting.
- One underlying event should normally appear once. Mention additional materially useful sources in the summary only when needed, but keep one canonical `sourceUrl`.

## Required JSON
Write valid UTF-8 JSON with this structure:
```json
{
  "schemaVersion": 1,
  "generatedAt": "ISO-8601 timestamp",
  "window": {"timezone":"America/New_York","hours":24,"start":"ISO-8601","end":"ISO-8601"},
  "items": [
    {
      "headline": "source-supported headline",
      "summary": "1–2 concise factual sentences",
      "entityName": "exact canonical workbook name",
      "category": "exact canonical workbook category",
      "state": "exact canonical workbook state",
      "publicationDate": "ISO-8601 timestamp when available, otherwise YYYY-MM-DD",
      "source": "publisher name",
      "sourceUrl": "canonical HTTPS URL",
      "topic": "short controlled topic label",
      "sentiment": "Positive|Neutral|Negative|Mixed",
      "location": "city/state or state/national scope",
      "relevanceScore": 0
    }
  ],
  "quality": {"duplicatesRemoved":0,"unsupportedItemsExcluded":0,"notes":"brief method or coverage caveat"}
}
```

## Editorial rules
- Headline and summary must not add claims beyond the cited page.
- Use neutral, plain language. Sentiment describes likely impact on the named entity, not the emotional tone of the article.
- Score relevance from 0–100: 90–100 entity is central and impact is direct; 75–89 substantial direct mention or clear material effect; 60–74 verified but secondary material relevance. Exclude anything below 60.
- Use concise topic labels such as Regulation, Contract Award, Procurement, Leadership, Budget, Litigation, Technology, Cybersecurity, Operations, Partnership, Financial Results, Policy, or Public Service.
- Sort retained items by relevance descending, then publication date descending.
- An empty `items` array is valid. Never pad a quiet day with weak or old stories.

## Validation and publication
1. Parse the finished JSON before publishing. Confirm every item has all required fields, a unique canonical URL/event, an allowed sentiment, a relevance score from 60–100, an in-window date, and an exact entity/category/state combination from the canonical file.
2. Save the same JSON locally to `/tasklet/agent/home/state-entity-news/news.json` so the latest successful result persists.
3. Push the identical validated JSON to both `data/news.json` and `data/archive/YYYY-MM-DD.json` in `rahulaccen/us-state-entity-watchtower`, branch `main`, with commit message `Daily briefing: YYYY-MM-DD`. Use the America/New_York calendar date of `window.end` for the dated filename. A rerun on the same date may replace that date's archive file.
4. If research or publication fails persistently, do not overwrite the last successful briefing with partial or invalid data. Report the blocker to Rahul once with the action needed.
