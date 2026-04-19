# TODOS — 리베로

Deferred work captured during eng review or build. Each item has context for future-you.

## v1.1 fast-follow (after MVP ships)

### Attendance tracking as M3 extension (RSVP scope only)
**What:** Extend Module 3 (Naver Band auto-post) to also READ the auto-posted practice poll's comment thread. Parse the comments (via Band Open API + Haiku comment parser if needed) to identify which members said yes/no. Surface as an "attendance-rate" view: "지난 4주 김철수님 출석률 75%".

**Scope clarifier — this is RSVP tracking, NOT true attendance.** "Attendance" colloquially means "did you physically show up at practice." This feature captures "who said yes to the poll." Many members say yes and don't show up. True attendance requires a captain-marks-present UI at the gym — that stays v2+ (see below).

**Why v1.1 (not v1):** captain asked for it in the 2026-04-17 interview ("출석률 체크할 수 있으면 좋겠다 ... (밴드) 댓글 확인해서 출석률 반영?"). Natural extension of M3's existing Band API integration — reuses the auth token, the cron infrastructure, and the Haiku eval harness. Not a new module.

**Why NOT v1:** M3 in v1 has tight scope (post-only). Adding comment-read + attendance-rate-view doubles M3's UI surface and adds a new data flow. Safer to ship the post-only v1, then extend.

**Trigger:** v1 shipped AND M3 post-only verified working in closed test OR ≥3 closed-test captains ask for attendance visibility explicitly.

**Depends on:** Module 3 live, Band Open API comment-read scope (may require additional API scope beyond the post scope — verify in v1.1 scoping).

**Context:** Interview Re-anchor Decision 39 (2026-04-17). Captain's suggestion is clever and low-cost-at-the-margin; just not scope for v1.

---

### NotificationListenerService exploration (risky, Play-Store-gated)
**What:** Intercept KakaoBank's "X원이 입금되었습니다" push notifications directly on the Android device via `NotificationListenerService`. Feed the parsed amount/sender into the same reference-matching algorithm that M2 uses for Excel imports. Real-time reconciliation with zero user action.

**Why deferred (hard):** Google Play restricts `NotificationListenerService` to apps with a "core use case" (screen reader, SMS backup, car dash display). General personal finance apps have had approvals revoked by Play Store review historically. Also: the permission reads ALL notifications — privacy surface is large.

**Design already future-compatible:** the `ReferenceMatcher` in M2 operates on `ParsedTransaction` regardless of source. A notification-listener input would produce the same shape of object. Swap is mechanical — no algorithm changes needed.

**Trigger:** (a) Play Store explicitly clarifies the "core use case" criteria in Libero's favor, OR (b) a real user pattern emerges where Excel-upload friction is the #1 complaint in closed test AND we have a way to justify to Play Store review.

**Context:** Interview Re-anchor Decision 38 (2026-04-17). Captain independently proposed the mechanism — smart product thinking, wrong platform risk profile for v1.

**Depends on:** v1 shipped, Play Store landscape shift OR real user demand signal.

---

## v2 (post-MVP, after real users complain)

### True attendance tracking (captain marks present at the gym)
**What:** A captain-facing UI at practice to mark which members physically showed up. Produces a real attendance-rate metric distinct from the RSVP-rate metric from v1.1's Band-comment-read feature.

**Why deferred to v2:** New UX surface (captain taps present/absent per member during or after practice), new data flow (`attendance_records` table with timestamp + location optional), real attendance UX debate (mark-as-I-see-them vs batch-confirm-at-end vs QR-scan-at-door). Non-trivial.

**Why NOT v1.1:** v1.1 extends M3 via Band comment reading — cheap, reuses existing scaffolding. True attendance is a new module with new schema. Don't lump these together.

**Trigger:** ≥5 closed-test or production captains ask for physical-show-up tracking (not just RSVP) OR a club disputes a member's attendance claim.

**Depends on:** v1 shipped, RSVP feature v1.1 landed, real user demand for the finer-grained metric.

**Context:** Interview Re-anchor Decision 39 (2026-04-17).

---

### Role-based UI / permission differentiation (captain vs 총무)
**What:** Add UI branching and permission checks based on whether a user is the "captain" or "secretary" of a club. Examples: only the captain can edit tournament preferences; only the secretary can upload KakaoBank Excel; the captain has a "Mark as Paid" override the secretary doesn't; secretary sees a "Finance" tab the captain doesn't, etc.

**Why deferred to v2:** Confirmed as a v1 design conviction on 2026-04-08 — every user inside a club sees identical screens, has identical permissions, and can do every action. The captain/secretary label is just metadata on the user record, not a permission gate. Reasoning: simpler v1, no permission-check branches in code, the killer-feature thesis ("shared source of truth between two equal managers") is actually undermined by role differentiation, and role-based design is a classic premature-design trap.

**Pros (when v2 ships this):** more aligned with traditional Korean club hierarchies (회장/주장/총무 separation), enables clubs that explicitly want 권한 분리, better audit story for clubs with money disputes.

**Cons (which is why it's deferred):** ~5x the design surface (every screen needs role-aware variants), permission RLS becomes more complex, onboarding has to ask "are you the captain or 총무" upfront, "I can't do this" friction for the "wrong" user, splits the codebase's mental model.

**Trigger to actually build this:** ≥10 active captains explicitly complain they want 권한 분리. Until then, equal UI is correct. NOT before.

**Context:** Captured during captain-interview prep on 2026-04-08 when the user corrected Claude's assumption that Module 2 might need role-differentiated UI based on who actually does KakaoBank reconciliation. The user's framing: "All the UI should be equal among whatever user they are, regardless of if they're captain or secretary. Modifying the feature into separate UI and roles is a next step."

**Cheap optionality kept in v1:** the schema's `paid_by_user_id` field (and similar "who did this" audit fields) CAN exist in v1 — they don't surface in UI but they record who did what. If v2 needs to add audit-trail visibility, the data is already there. ~0 cost in v1, big saving in v2.

**Depends on:** v1 shipped + ≥10 active captains + explicit complaint about权한 분리 (NOT speculative — wait for the actual signal).

---

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

**Context:** Decided in /plan-eng-review revision (2026-04-08 evening) to ship v1 with own-club-only validation as a deliberate scope cut. CEO review (2026-04-08 evening) reaffirmed this decision but sharpened the trigger.

**SHARPENED TRIGGER (CEO review 2026-04-08):** during W12-13 closed testing, monitor non-own-club testers for ANY of these signals: (a) tester opens the app < 2 times after install, (b) tester uninstalls within 7 days, (c) tester gives feedback "this doesn't match how my club works." If ANY of those signals fires from ≥1 non-own-club tester, **halt v1.1 feature work and do these interviews immediately.** Do not wait until "patterns emerge."

**When to do this:** triggered by closed-test signal above, OR by month 3 unconditionally (whichever comes first).

**Depends on:** v1 in closed testing.

---

### Portfolio polish budget (CEO review deferral, 2026-04-08)

**What:** README case-study rewrite, demo video, polished screenshots, "what I learned" decision-reversal section. Total ~3-4 days CC time.

**Why:** Outside voice flagged that the 13-week build investment is converted into a CV-piece outcome by the polish, NOT by the build itself. CEO review recommended adding this in W11-W13; user chose to defer to post-W13.

**CEO recommendation (NOT a hard contract):** aim to land all 4 deliverables within 1 week post-W13. The recommendation exists so the polish doesn't slip indefinitely. If it slips into week 2, that's the user's call. Deliverables broken out:
1. README rewrite as a case study (the killer-feature pivot, the Hilt→Koin reversal, the Cloudflare→Supabase reversal, the Vision-OCR→KakaoBank-Excel pivot, the multi-manager reframe). ~1 day.
2. Polished screenshots (5+ in Korean, generated via Android Studio Device Frame Generator). ~half day.
3. 60-second demo video (emulator screen record + Korean captions). ~1 day.
4. Decision Reversals section in README + linked deeper writeups. ~half-day.

**Trigger:** day after W13 production rollout, OR earlier if user starts interviewing.

**Depends on:** v1 shipped to Production.

---

### Confidence badge on tournaments (CEO review deferral, 2026-04-08)

**What:** Show the Haiku `parser_confidence` value as a small visual badge on each tournament in the list view (e.g., "AI 92%" or a colored dot). The data is already in the schema. ~30 min CC time.

**Why:** Pairs with the W6 cafe-post-link trust escape hatch. Builds calibrated trust in the LLM extraction over time. CV story bullet: "designed the UX around LLM uncertainty."

**Trigger:** after MVP launch, build in v1.1 if captains complain about parser misses OR if you have a free hour during W12-13.

**Depends on:** v1 shipped.

---

### Realtime backfill on reconnect (Section 2 deferral, 2026-04-08)

**What:** When the Supabase Realtime SDK reconnects after a WebSocket disconnect, refetch relevant `tournaments` and `member_payments` rows where `created_at > last_seen` to backfill missed events.

**Why:** Section 2 error map flagged this as a gap. The Supabase SDK handles reconnect but doesn't backfill missed events by default. For two managers in one club, the practical impact is "if your phone loses signal for an hour, you may miss a tournament push or a payment update from your co-manager." Acceptable for v1 scale; bug-prone at scale.

**Trigger:** v2 work, or earlier if real users report stale data after network drops.

**Depends on:** v1 shipped + real user complaints.

---

### Edge Function Sentry integration (CEO review deferral, 2026-04-08)

**What:** Add Sentry to the Supabase Edge Functions (`scrape_daum_cafe`, `dispatch_tournament_match`, `post_band_poll`) for backend error tracking.

**Why:** CEO Decision 27 moved Sentry to W3 for the Android app. Edge Function Sentry was kept at W11 polish week because it's a separate integration (Sentry has a Deno SDK but it's less mature than the Android one). If you skip W11 polish under pressure, this is the lowest-priority of the polish items to lose.

**Trigger:** W11 polish, or v1.1 if W11 was tight.

**Depends on:** Edge Functions deployed in W5-W6.

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
