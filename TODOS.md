# TODOS — 리베로

Deferred work captured during eng review or build. Each item has context for future-you.

## v2 (post-MVP, after real users complain)

### Persistent cache layer (DataStore + tiny Room)
**What:** Add DataStore for key-value config (club_id, sync timestamps, user prefs) + a 2-table Room database (`tournaments_cache`, `payment_cycle_cache`) for surviving Android process death.

**Why:** v1 ships with in-memory `StateFlow` only and shows spinners after process death. The killer feature's hot path (push → detail) is mitigated via FCM data payload in W6, but other screens (cold open from app drawer) still show spinners after Android kills the app's process.

**Pros:** instant content on app reopen, demo polish in airplane mode, "implemented two-tier cache" CV story addition.

**Cons:** cache invalidation between Supabase and Room is genuinely hard, can introduce subtle sync bugs, ~3-4 days of work + ongoing maintenance burden, two sources of truth.

**Context:** Decided in /plan-eng-review (2026-04-08) to defer to v2. The decision was: ship v1 with in-memory only because cache invalidation is famously one of the hardest problems in software, and a 2-user-per-club app where both managers are mostly online doesn't justify the complexity. The killer feature's most critical moment (push → detail) is mitigated via the FCM data-payload trick (full tournament data ships in the push payload itself, no fetch needed). Add this TODO ONLY after v1 ships AND real users complain about cold-start spinners on screens other than the push-tap path. NOT before.

**Depends on:** v1 shipped + real user complaint about spinners.

---

## Strategic decisions deferred to month 3

### Pure CV piece vs real freemium SaaS commitment
**What:** decide whether to start 사업자등록 (Korean business registration, 2-3 weeks bureaucracy) and apply for Toss Payments / KakaoPay merchant accounts, OR delete the subscription scaffold and commit to a pure CV piece.

**Why:** Strategic position C from /plan-eng-review (2026-04-08): defer the call until month 3 with real usage data. The MVP build is identical in all three positions — the difference is only whether you've started bureaucracy + whether the subscription scaffold tables stay or get deleted.

**Decision criteria at month 3:**
- **If month-3 usage shows ≥50 active captains and growing** → start 사업자등록 immediately, build Pro checkout flow over weeks 16-22, launch Pro at month 6 as planned in DESIGN.md
- **If month-3 usage shows ≤10 active captains** → delete `subscription_tiers` and `club_subscriptions` from the schema, update DESIGN.md success criteria to remove revenue mentions, commit to pure CV piece
- **In between (10-50 captains)** → another month of data, decide at month 4

**Context:** subscription scaffold tables stay in W2 schema as cheap optionality (~30 min of work). Feature-flag helper in the Android app always returns `true` at MVP. Cost of optionality: ~3-4 hours of W2 work + tables sitting unused for ~3 months. Cost of NOT having optionality: 2-3 days of refactoring at month 6 if usage justifies the pivot. The bureaucracy lift (사업자등록, Toss merchant application) is itself 2-3 weeks, so it must start at the END of month 3 if you decide to pivot, not later.

**Depends on:** v1 shipped + 3 months of real usage data.

---

## Validation work deferred to v2

### Interview captains from OTHER clubs
**What:** Conduct 2-3 user interviews with captains/secretaries from clubs OTHER than your own. Different region, different skill bracket, different size if possible.

**Why:** v1's captain interviews are own-club only (own captain + own secretary, decided 2026-04-08). This validates the multi-manager handoff dynamic in your specific club but does NOT validate "do other clubs have the same pain in the same shape." The original eng review recommended 3 interviews across multiple clubs for this reason.

**Pros:** confirms the killer feature thesis generalizes beyond your club. Surfaces region-specific or skill-bracket-specific differences in workflow. Better data for the freemium pricing decision at month 3.

**Cons:** time-intensive (need to find willing strangers, build rapport, schedule). Owns no immediate code changes — pure research.

**Context:** Decided in /plan-eng-review revision (2026-04-08 evening) to ship v1 with own-club-only validation as a deliberate scope cut. The risk: if your club's workflow is unusually well-organized (or unusually chaotic), you may build modules that don't generalize. Mitigation: closely track the v1 retention curve from non-own-club captains in W12-W13 closed testing — if they churn fast, do these interviews immediately.

**When to do this:** after v1 ships AND closed test reveals patterns from non-own-club testers (positive or negative). Specifically: target between W12 and month 3.

**Depends on:** v1 in closed testing.

---

## Mobile UX polish (defer until v1 ships)

### QR code invite sharing
**What:** Add a QR code rendering of the 6-character invite code on the club detail screen. Captain shows secretary the QR code at practice, secretary scans it with her phone camera.

**Why:** Even smoother than KakaoTalk text sharing for in-person invites. Uses a tiny QR library (~50KB). Better demo video shot.

**Pros:** more polished UX for in-person onboarding, marginal CV story polish.

**Cons:** none significant — pure addition, no architectural impact.

**Context:** v1 ships with text sharing only via KakaoTalk Sharesheet. QR is a polish add-on that doesn't change the underlying invite mechanism. Library: zxing-android-embedded or similar.

---

### Samsung battery optimization guide screen
**What:** First-launch instructional screen that walks the captain through adding 리베로 to "잠자지 않는 앱" (Never sleeping apps) on Samsung One UI.

**Why:** Samsung's aggressive background app killing is the #1 reason Korean push apps fail. Without guiding the user to opt 리베로 out of battery optimization, they may never receive tournament pushes.

**Context:** This becomes MANDATORY (not deferred) if W1 Gate 1C fails. If Gate 1C passes (push delivers reliably with default battery settings), this becomes a polish add-on for the cautious path. Schedule for W3 or W4 if Gate 1C fails.

---

### Kakao Login as alternate auth provider
**What:** Add Kakao Login as a secondary auth provider alongside Sign in with Google, for Korean users who trust Kakao more than Google.

**Why:** Korean trust signal — Kakao Login is the dominant Korean auth pattern (KakaoTalk integration). Some Korean users distrust Google Sign-In on principle.

**Pros:** broadens reach to the most Korean-native subset of users.

**Cons:** Kakao Developer registration + app review + custom OAuth callback Edge Function = ~2-3 days of work. Supabase doesn't natively support Kakao OAuth as a built-in provider, so you'd integrate it as a "custom OAuth provider."

**Context:** v1.1 fast-follow per DESIGN.md. Already documented there.

---

## Open Questions / Uncertainty

### Daum HTML structure stability
**What:** Daum cafe HTML can be rewritten without notice. The 20-post Haiku eval corpus catches PROMPT regressions but won't catch a mid-MVP layout change at the cafe itself.

**Mitigation in plan:** Daum shadowban anomaly alarm fires if zero new posts >36h. This catches both account bans AND HTML structure breaks.

**Action:** monitor `scraper_runs` table after launch. If anomaly alarm fires, manually inspect cafe HTML for layout changes and update the cheerio selector.

---

### Supabase single-vendor concentration risk
**What:** Database, auth, realtime, edge functions, secrets, and cron all live on Supabase. A Supabase incident takes down the entire backend simultaneously.

**Acceptable for MVP** (small user base, free tier, low expectation). **Becomes a real concern at month 6+** if you have paying users.

**Action:** if month-3 usage data justifies the freemium pivot, also plan a "what does the user see when Supabase is down" UX (banner, retry button). Currently the app would just show error states everywhere.

---

### 3-hour polling cadence reconsideration
**What:** DESIGN.md and BUILD.md lock the scraper at 3-hour cadence for Daum stealth. Outside voice argued this means ~30% of popular tournaments fill before captains see them, undermining the killer feature value prop.

**When to reconsider:** after first month of real production data on `time_from_post_to_notification` metric. If captains complain about "I got the push but the tournament was already full," bump cadence to 30 minutes (Daum stealth risk increases marginally, but the cost is negligible: 8x more polls per day = ~$0 at this scale).

**Action:** track the metric from W7 onward. Decide based on data, not theory.
