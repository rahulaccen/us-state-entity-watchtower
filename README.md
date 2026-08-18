# US State Entity Watchtower

A daily, source-supported news briefing for 156 entities listed in the supplied workbook across Arizona, Florida, Massachusetts, New York, North Carolina, Ohio, Pennsylvania, and Texas.

## Data

- `data/entities.json` — official workbook names, states, and categories
- `data/news.json` — latest verified 24-hour briefing

Each news item includes headline, summary, entity name, category, publication date, source, source URL, topic, sentiment, location, and a 0–100 relevance score.

## Method

The briefing searches reliable local, state, national, regulatory, and official company or agency sources. Items must directly mention or materially affect a tracked entity. Duplicate coverage is consolidated, and unsupported claims are excluded.

The site is designed for GitHub Pages and refreshes daily at 8:00 AM US Eastern time.
