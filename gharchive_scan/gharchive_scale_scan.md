# GH Archive Scale Scan (Spark)

## Purpose
Supplementary Signal 3 evidence at archive scale: are repos named after Signal 1's
shortlist candidates being pushed to? Complements the Code Search evidence
(signal3_evidence_design.md). This is NOT proof of installation; PushEvents carry no
file lists (see gharchive_scope.md).

## Method
- Window: 2026-08-24 to 2026-08-30, 168 hourly files, ~1.6 GB compressed.
- Spark local mode (4 cores, 8 GB heap set at notebook start), reading only 4 fields
  (type, created_at, actor.login, repo.name).
- Kept PushEvents, matched the lowercase repo short name against the 142 distinct
  candidate short names, aggregated pushes, active days and distinct pushers per repo.
- Scanned 12,061,354 events, 11,555,061 pushes (95.8%), in ~135 s.

## Results
71 matching repos; the top 30 by pushes were reviewed, the rest have <= 1 push.
Dominated by `elasticsearch` (elastic/elasticsearch plus likely forks), `9router`
(many accounts, 1-3 pushes each, one pusher each), and common words (`anima`,
`telemetry`). No match could be classified as malicious from this data.

## Limitations
- Matches by repo name only, so recall is low by design.
- Activity is not installation; forks and clones inflate name matches.
- One week, not a quarter.
- Sep 1-4 files contained almost no PushEvents (cause not investigated), so the
  choice of window matters. Aug 24-30 was verified at 94-97% pushes.

## Correction to Week 1 volume estimate
Measured Aug 24-30: ~232 MB/day average (160-344 MB), roughly 20-30 GB per 90 days.
This is the same order of magnitude as the original ~29 GB estimate, but it rests
on one week, so treat it as a range.