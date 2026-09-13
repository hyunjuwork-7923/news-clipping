---
name: seoul-goods-clipping
description: Produces the team's actual daily media-clipping format — three sections covering (1) 언론보도 (시·SDF 관련, general Seoul City/Seoul Design Foundation press, not just goods), (2) 시장 동향 (news coverage of the Mayor's own public activities/statements), and (3) 굿즈관련 (general Korean goods/merchandise trend news, not restricted to Seoul's own brand) — for yesterday's date only, all domestic outlets, rendered as a refined text-centric HTML page. Use this whenever the user asks for a "서울 클리핑", "시정 클리핑", "SDF 클리핑", "굿즈 뉴스클리핑", "시장님 동향", "오늘 클리핑 만들어줘" in a Seoul city-hall/SDF context, or wants the daily press/mayor/goods roundup the team normally publishes — even if they only mention one of the three sections. Do NOT trigger for one-off single-article requests, for general national news unrelated to Seoul city administration or the goods/merch beat, or for SNS/social-media monitoring (that was tried and dropped — public search can't reliably verify social post content; 시장 동향 here means news coverage of the Mayor, not his social accounts).
---

# Seoul Daily Clipping (Press · Mayor · Goods, 3 Sections)

This mirrors a real internal product: a daily clipping with three distinct sections, not one
undifferentiated list. Section scope is not uniform — Section 1 is genuinely broad (any Seoul
City/SDF-relevant press, not just goods), Section 2 narrows to one person (the Mayor) but
widens to any topic he touches, and Section 3 is broad in a different way again (any notable
Korean goods/merch story, not just Seoul's own brand). Don't try to force one filtering rule
across all three — each has its own bar, described below.

(An earlier version of this skill had a section for official social-media posts (SNS채널). It
was dropped: this environment can't reliably verify what a social account actually posted on
a given day — logged-out platform pages don't expose the feed, and public web search only
catches a post if some other article happens to describe it. Section 2 below is a different,
more tractable idea aimed at the same underlying need ("what's the Mayor been up to") — it
tracks *news coverage* of him, which is normal search territory, not his accounts directly.
Don't fold Section 2 back into raw SNS monitoring without real account access.)

## Common setup

Take today's date from the environment/system context in KST and subtract one day — that's
the target calendar date for all three sections. State it once at the top of the output.

No outlet whitelist anywhere in this skill — any legitimate Korean news outlet counts, not a
curated Tier-1 list. What still doesn't count as a citable source: government PR channels
presented as news, blogs, forums, social posts.

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

## Section 2 — 시장 동향

News coverage of the Mayor's own public activities, statements, or schedule for the target
date — an event he attended, a policy he personally announced, a remark he made, an interview,
a site visit. Search "오세훈" and "오세훈 시장" (or "오세훈 서울시장") with the date filter — if
a different mayor is in office when this runs, use their name instead; don't assume the name
is fixed.

**Hard exclusion: no litigation, criminal proceedings, prosecution, or partisan political-
scandal coverage, even if it's the top "오세훈" search result that day.** This clipping goes out
automatically every morning via a messaging bot and gets forwarded to people outside the
immediate team — a brand/comms team publishing daily coverage of the Mayor's own criminal
trial (testimony, a verdict date, a defense argument) is a real reputational and organizational
risk, not just an editorial judgment call, and there's no one reviewing it before it goes out.
If a search for the Mayor's name turns up mostly this kind of story on a given day, that's a
sign to include fewer items (even zero) rather than pad the section with it. This is not the
same caution as the "skip photo-caption filler" rule below — that's about substance, this is
about subject matter being categorically out of scope for an unattended, externally-shared
daily.

Beyond that hard exclusion, use judgment on ordinary political friction (e.g. a policy dispute
with the city council) — it's not automatically excluded the way litigation is, but weigh
whether it's the kind of thing this document's actual readers (a brand/design-org team, not a
political war room) need in their morning clipping, especially knowing it may be forwarded
externally.

This overlaps with Section 1 in subject matter (both are Seoul city-hall news) but the lens is
different: Section 1 asks "is this relevant to the city/SDF broadly," Section 2 asks
"did the Mayor himself do or say this." A story can qualify for both — that's fine, but put it
in whichever section is the better fit for its actual focus rather than duplicating it in both
(a policy announcement he personally delivered goes in Section 2; a department's routine
program update that merely quotes him in passing belongs in Section 1, or neither).

Skip pure photo-caption filler (a wire-service one-line photo credit with no real content)
unless the event itself is genuinely newsworthy — a caption noting he attended a minor
ceremony isn't worth a slot on its own.

Capture headline + 원문링크 + an optional one-line detail, same shape as Section 1 but without
a 관련부서 tag (this section is about the person, not department routing). Cap at a reasonable
count (5 is a sensible ceiling), same "don't pad" discipline — a quiet or entirely
litigation-dominated day should produce zero items here, not a stretch.

## Section 3 — 굿즈관련

General Korean goods/merchandise trend news for the target date — **deliberately not
restricted to Seoul's own brand or any keyword list.** A notable new collaboration, a product
launch, a "완판/품절" consumer trend story — anywhere in Korea, any brand — counts, as long as
it's a real, dated news item and not incidental background noise (e.g. a passing mention of
"굿즈" inside an unrelated K-pop concert review doesn't count; a dedicated story about a
specific goods launch or trend does).

This section used to be gated behind an "industry structural trend" test (market size,
licensing deals) in an earlier version of this skill — that was too narrow. The actual bar,
based on what the team really publishes here, is much simpler: is this a genuine, dated news
item about a goods/merch launch or trend that someone tracking this beat would want to see?
Search "굿즈" directly with the date filter (yes, it's noisy — skim past K-pop/gaming/generic
convention coverage for the retail-collab/product-launch items) plus more targeted phrases
like "굿즈 콜라보", "굿즈 완판", "굿즈 출시" to catch the sharper hits faster.

**Also specifically search Seoul's own brand terms — 서울마이소울, 해치, 서울굿즈,
DDP디자인스토어 — even though this section isn't restricted to them.** An earlier version of
this skill searched exactly these terms as its whole scope; when the skill widened to general
goods news, a genuinely on-brand story (e.g. a profile of the city official who built the
서울마이소울/해치/서울굿즈 brand strategy) got missed because the generic "굿즈" search alone
buried it under national retail/K-pop/gaming noise. These four terms are cheap, low-noise
searches precisely because they're specific — always run them alongside the generic ones, and
when they turn up a real hit, it's usually the most relevant item in this section, not just
one candidate among many equals.

Cap at a reasonable count (5 is a sensible ceiling), same padding discipline as elsewhere.

## Verification (all sections)

Open each candidate (don't trust the search snippet alone) and confirm the byline/dateline
matches the target date exactly — a live search result's "N시간 전" language is relative to
whenever you're searching, not a fixed date, so check the article's own timestamp.

## Build the text-centric HTML page

Not a card grid — a numbered, editorial text list matching the real format: three headed
sections ("01 언론보도 (市·SDF 관련)", "02 시장 동향", "03 굿즈관련"), sub-numbered items
within Section 1 (1.1, 1.2...), and numbered lists for Sections 2 and 3 (2.1... / 3.1...).
Use `assets/template.html` as the starting structure — it's styled after a "Bain Red"
consulting-deck look the user referenced (red #CC0000 / navy #00253E, bold sans headlines,
thin rule dividers, uppercase tracked eyebrows, a big red stat number per section showing its
item count). Don't revert to serif headlines or a softer palette without the user asking.
Keep the visual polish even though the structure is closer to a working memo than a magazine
page; it should read as considered, not decorative.

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
- Section 2 items are genuinely about the Mayor's own activity/statement, not just any
  city-hall story that happens to mention him, and no single story sits in both Section 1
  and Section 2
- **No Section 2 item is about litigation, a criminal case, prosecution, or a political
  scandal** — that's a hard exclusion, not a judgment call, given this goes out unattended
  and gets forwarded externally
- Section 3 items are genuine dated goods/merch news, unrestricted by brand, but still real
  news (not incidental mentions)
- Every cited item's date matches the target date exactly
- No section was padded to look fuller than what was actually found
- Layout is a numbered text list, not a card grid, and stays legible even when a section is
  short
- No SNS/social-media section — Section 2 is news coverage of the Mayor, not his own accounts
