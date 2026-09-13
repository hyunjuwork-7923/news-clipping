---
name: seoul-goods-clipping
description: Produces the team's actual daily media-clipping format — three sections covering (1) 언론보도 (시·SDF 관련, general Seoul City/Seoul Design Foundation press, not just goods), (2) SNS채널 (notable official social-media posts), and (3) 굿즈관련 (general Korean goods/merchandise trend news, not restricted to Seoul's own brand) — for yesterday's date only, all domestic outlets, rendered as a refined text-centric HTML page. Use this whenever the user asks for a "서울 클리핑", "시정 클리핑", "SDF 클리핑", "굿즈 뉴스클리핑", "오늘 클리핑 만들어줘" in a Seoul city-hall/SDF context, or wants the daily press/SNS/goods roundup the team normally publishes — even if they only mention one of the three sections. Do NOT trigger for one-off single-article requests or for general national news unrelated to Seoul city administration or the goods/merch beat.
---

# Seoul Daily Clipping (Press · SNS · Goods, 3 Sections)

This mirrors a real internal product: a daily clipping with three distinct sections, not one
undifferentiated list. Section scope is not uniform — Section 1 is genuinely broad (any Seoul
City/SDF-relevant press, not just goods), Section 3 is genuinely broad in a different way (any
notable Korean goods/merch story, not just Seoul's own brand), and Section 2 is a different
kind of source entirely (social posts, not news articles). Don't try to force one filtering
rule across all three — each section has its own bar, described below.

## Common setup

Take today's date from the environment/system context in KST and subtract one day — that's
the target calendar date for all three sections. State it once at the top of the output.

No outlet whitelist anywhere in this skill — any legitimate Korean news outlet counts, not a
curated Tier-1 list. What still doesn't count as a citable source: government PR channels
presented as news, blogs, forums, social posts (for Sections 1 and 3 — Section 2 is precisely
about social posts, so that exclusion doesn't apply there).

**Naver News search's date-range filter is blocked by this environment's browser tool policy
— don't rediscover that. Use Daum News search instead**, which supports the same exact-date
filtering and is confirmed reachable:

```
https://search.daum.net/search?w=news&q=서울시&period=u&sd=20260912000000&ed=20260912235959
```

`q` is the search phrase, `sd`/`de` are `YYYYMMDDHHmmss` start/end — set both to the target
date (`000000`/`235959`) for exactly one calendar day. If Daum is ever unreachable, fall back
to `WebSearch`, but treat it as a fallback — it's noticeably less precise for same-day Korean
news.

## Section 1 — 언론보도 (市·SDF 관련)

This section is general Seoul City / Seoul Design Foundation (SDF) press coverage — policy
announcements, city initiatives, SDF venue programming (e.g. DDP hosting an exhibition),
budget/plan news, anything a city-hall press-monitoring desk would flag. **It is not limited
to goods, DDP, or character-brand keywords** — a story like a citywide investment plan has
nothing to do with goods and still belongs here.

Search "서울시" and "서울디자인재단" (and "SDF" as a standalone acronym) with the date filter.
For each qualifying item, capture:

- **headline**, linked to the original article (원문링크)
- **관련부서** (the relevant city department), when it can be reasonably inferred from the
  story's topic — e.g. a labor/welfare policy story maps to a labor-policy department, a DDP
  exhibition maps to SDF/culture-facility management. If you genuinely can't tell which
  department, leave this field out rather than guessing — a wrong department tag is worse
  than no tag, since someone will actually try to route it there.
- **주요내용** (optional), a one-line detail beyond the headline when the headline alone
  doesn't convey a key fact worth surfacing (a date range, a budget figure, a venue name).

Number sub-items 1.1, 1.2, etc. Cap at a reasonable count (5 is a sensible ceiling) — same
"don't pad a quiet day" discipline as the rest of this skill: if there are only 2 genuine
items, list 2.

## Section 2 — SNS채널 (주요 게시건)

Notable posts from Seoul City's official social channels (the Mayor's own accounts, the
city's official channels) for the target date — event-announcement content, campaign videos,
programming pushes.

**Be honest about the access limitation here.** This session can't log into or scrape a
social platform's own feed directly the way it can search news — public web search sometimes
surfaces a post's existence (a news article mentioning "시장님이 영상을 올렸다", or the post
itself if indexed), but this is much less reliable than Section 1's news search. If you can't
verify specific posts happened on the exact target date, say so plainly in this section
rather than presenting unverified guesses as confirmed activity — an honest "이 항목은 공개
검색으로 확인이 어려웠습니다" beats a fabricated post description.

Format as a short bullet list — no links required the way Section 1 has them, since these are
brief descriptions of what was posted, not citations of an article.

## Section 3 — 굿즈관련

General Korean goods/merchandise trend news for the target date — **deliberately not
restricted to Seoul's own brand or the earlier keyword list.** A notable new collaboration, a
product launch, a "완판/품절" consumer trend story — anywhere in Korea, any brand — counts,
as long as it's a real, dated news item and not incidental background noise (e.g. a passing
mention of "굿즈" inside an unrelated K-pop concert review doesn't count; a dedicated story
about a specific goods launch or trend does).

This section used to be gated behind an "industry structural trend" test (market size,
licensing deals) in an earlier version of this skill — that was too narrow. The actual bar,
based on what the team really publishes here, is much simpler: is this a genuine, dated news
item about a goods/merch launch or trend that someone tracking this beat would want to see?
Search "굿즈" directly with the date filter (yes, it's noisy — skim past K-pop/gaming/generic
convention coverage for the retail-collab/product-launch items) plus more targeted phrases
like "굿즈 콜라보", "굿즈 완판", "굿즈 출시" to catch the sharper hits faster.

Cap at a reasonable count (5 is a sensible ceiling), same padding discipline as elsewhere.

## Verification (all sections)

Open each candidate (don't trust the search snippet alone) and confirm the byline/dateline
matches the target date exactly — a live search result's "N시간 전" language is relative to
whenever you're searching, not a fixed date, so check the article's own timestamp.

## Build the text-centric HTML page

Not a card grid — a numbered, editorial text list matching the real format: three headed
sections ("1. 언론보도 (市·SDF 관련)", "2. SNS채널", "3. 굿즈관련"), sub-numbered items within
Section 1 (1.1, 1.2...), a plain bulleted list for Section 2, and a numbered or bulleted list
for Section 3. Use `assets/template.html` as the starting structure — narrow centered column,
serif headline face, hairline rules, restrained color. Keep the visual polish even though the
structure is closer to a working memo than a magazine page; it should read as considered, not
decorative.

If a section has zero qualifying items, keep the section header and say so plainly inside it
("어제자 관련 보도 없음") rather than silently dropping the whole section — the section
structure itself is part of what makes this the team's actual format, so it should always be
visible even on a quiet day.

If this will be shown to the user in this session, publish it through whatever
artifact-publishing convention the environment provides rather than leaving it as a bare
file. Save the working file with a dated name (`seoul-goods-clipping-{TARGET_DATE}.html`) so
repeat runs don't collide.

## Sanity check before delivering

- All three section headers are present, even on a section with zero items
- Section 1 items are genuinely about Seoul City/SDF broadly (not filtered down to only
  goods-related ones) and only carry a 관련부서 tag where it's a confident inference
- Section 2 is honest about what could and couldn't be verified — no fabricated post content
- Section 3 items are genuine dated goods/merch news, unrestricted by brand, but still real
  news (not incidental mentions)
- Every cited item's date matches the target date exactly
- No section was padded to look fuller than what was actually found
- Layout is a numbered text list, not a card grid, and stays legible even when a section is
  short
