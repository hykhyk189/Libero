# PROGRESS — 리베로

> **Living state file. Maintained by Claude, not by you.** Open this whenever you sit down to work. The answer to "what should I do next" lives in **🎯 RIGHT NOW**. When you finish something, just tell Claude and Claude updates this file. When Claude finishes coding something, Claude updates this file in the same response. You should never have to edit this file by hand.
>
> Reference docs: [DESIGN.md](DESIGN.md) (the what + why) · [BUILD.md](BUILD.md) (the how + when) · [TODOS.md](TODOS.md) (deferred work)

**Last updated:** 2026-04-18 (Band app resubmitted; 주민번호 first-7 confirmed by captain; waiting on Band + Play Console)
**Current phase:** Pre-W1 homework (captain interviews DONE 2026-04-17; 주민번호 first-7 CONFIRMED 2026-04-18; Play Console pending; Naver Band app resubmitted 2026-04-18 with GitHub Pages URLs + Korean naming + Android service type)
**Overall:** Day 9 of ~13 weeks (calendar) — environment fully set up and both captain interviews complete. Key findings: multi-manager premise confirmed; 주민번호 in club Excel is first-7-only (PIPA general info, not 고유식별정보); Supabase architecture preserved (Drive pivot considered and rejected); members schema grows by ~5 fields. Remaining before W1: Play Console verification (waiting on Google), Naver Band write-scope verification, 3 plan decisions from interview re-anchor.

---

## 🎯 RIGHT NOW

- [ ] **Complete Google Play Console identity verification** — account is created, ID verification still pending. The 14-day closed-testing clock can't fully start until verification clears. Check your email + the Play Console dashboard for whatever Google is asking for. (Waiting on Google, no action you can take besides responding to whatever they ask.)
- [ ] **Naver Band app review — resubmitted 2026-04-18, awaiting result (~2026-04-25)** — second submission went in with `Libero` / `리베로` naming, Android service type selected, and real GitHub Pages URLs (`https://hykhyk189.github.io/Libero/` service URL, `https://hykhyk189.github.io/Libero/oauth/callback/` redirect URI — both verified HTTP 200). Repo `hykhyk189/Libero` is now PUBLIC, Pages enabled on main root. If approved: check whether `WRITE_POST` scope on the approved app is self-serve, approval-gated, or 사업자등록-gated (BUILD.md Part 0.7 three-bucket question, just one gate deeper than originally planned). If rejected again: escalate via developers.band.us contact form — do NOT blind-retry a third time.
**Scope note (post-interview re-anchor 2026-04-17, decisions baked):** 8 new BUILD.md decisions (34-41) baked into BUILD.md + DESIGN.md v4 + TODOS.md. M2 pivoted to reference-matched deposit classifier. Members schema gains 6 nullable columns (birth_date, gender, address, phone, jersey_size, jersey_number). Attendance = v1.1 M3 extension. NotificationListenerService + true-attendance parked to TODOs. Multi-manager premise re-confirmed. M1 framing validated.

**Next time you open this file:** Band review is in flight (resubmitted 2026-04-18, expected result ~2026-04-25). Check the developers.band.us dashboard ~4/25. Play Console verification is passive (waiting on Google). Pre-W1 homework is now idle-waiting — both open items are external dependencies, no user action between now and Band's response. Then W1 gates are up.

---

## 📅 THIS WEEK (between tonight and W1 starting)

- (Captain interviews and synthesis complete — see DONE.)

**Done with this week when:** the 3 open plan decisions are resolved (RIGHT NOW), Naver Band write-scope research is complete, and Play Console verification has cleared.

---

## 📅 NEXT (Week 1 — the gate week)

W1 only starts after this week's homework is done. **Don't start W1 if any captain interview surfaced something that breaks DESIGN.md.** Re-run /office-hours first.

The 5 W1 gates (each is a separate checkbox — see BUILD.md W1 for full details):
- [ ] **Gate 1A — Daum scraper feasibility** (capture 20 real posts for the W6 Haiku eval corpus while you're testing)
- [ ] **Gate 1B — fastexcel-reader smoke test** on a real KakaoBank xlsx from your secretary
- [ ] **Gate 1C — FCM delivery on Samsung with battery optimization**
- [ ] **Gate 1D — Sign in with Google + Credential Manager on Korean OEM**
- [ ] **Gate 1E — Supabase Edge Function latency from Korea**

Any gate fails AND fallback fails → STOP, rethink, do not proceed to W2.

---

## 🚧 BLOCKED / WAITING ON

- **Naver Band app review in flight — resubmitted 2026-04-18, expected result ~2026-04-25.** First submission (2026-04-08) rejected 2026-04-18 with no disclosed reason. Second submission went in with Korean naming + live GitHub Pages URLs + Android service type. Unblocks when Band approves (Client ID appears in dashboard) OR when scope gate is resolved. Escalation path if second rejection: developers.band.us contact form — NOT a third blind retry.

---

## ✅ DONE

Newest first.

- **2026-04-18** — **주민번호 first-7-digits CONFIRMED by captain.** The baked assumption in BUILD.md Decision 35 stands. No Drive-backed architecture pivot needed. Supabase plan holds. PIPA classification stays as "general personal information" (not 고유식별정보) — captain-attestation pattern in W2 schema + W4 UI checkbox remains sufficient. `members` schema gains the 6 nullable columns as planned (birth_date, gender, address, phone, jersey_size, jersey_number). Pre-W1 homework is now down to two external-dependency waits: Band review (~2026-04-25) and Play Console verification.
- **2026-04-18** — **Naver Band app resubmitted (2nd attempt).** First submission (2026-04-08, name="Libero", placeholder URLs) rejected 2026-04-18 — Band does not disclose reasons. Root causes diagnosed: English-only naming, `.placeholder`/`.local` Service/Redirect URIs, and the original Redirect URI pointing at a github.com repo path (which 404s, not a real served page). Fix-stack for resubmission: (a) added `리베로` Korean name, (b) set service type to Android (was `미선택` before), (c) spun up GitHub Pages landing (`https://hykhyk189.github.io/Libero/`) with a Korean description + an `/oauth/callback/` placeholder page, (d) flipped `hykhyk189/Libero` repo from PRIVATE → PUBLIC, (e) verified both URLs return HTTP 200 before submit. Commit `52b55d8` shipped the two HTML files. Regression flagged for BUILD.md Part 0.7: the portal's Step 2 app creation itself is reviewed (~1 week), not just the `WRITE_POST` scope — the three-bucket question applies to the scope gate AFTER app approval, not at app-creation time.

- **2026-04-17** — **Interview re-anchor decisions baked into BUILD.md + DESIGN.md + TODOS.md.** 8 new BUILD.md decisions (34-41) added as a new "Interview Re-anchor Decision Log (2026-04-17)" section. W2 schema section updated with `payment_references` migration + 6 new members columns. W8 updated with reference-table UI + extended member form. W9 updated with reference-matched classification pass (PRIMARY) + fuzzy name-match (SECONDARY). DESIGN.md bumped to v4 — Decision Reversals table grew from 15 rows to 21, members schema annotated with new columns, M2 section header got a v4 redirect pointing at the reference-matched framing, `payment_references` table added to data model. TODOS.md gained new v1.1 section (attendance-as-M3-extension RSVP-only, NotificationListenerService exploration) and v2 section (true attendance with captain-marks-present UI). 0 new unresolved decisions. One 30-second captain confirmation remains in RIGHT NOW (주민번호 first-7 vs full-13) as a cheap verification of the plan's baked assumption.
- **2026-04-17** — **Both captain interviews complete + synthesis captured + `/plan-ceo-review` re-anchor session.** Interviewed own-club captain and secretary. User provided consolidated synthesis (not per-speaker verbatim) → captured to `notes/captain-interview-combined.md`. Synthesis + premise analysis in `notes/captain-interview-synthesis.md`. Key findings: (1) **multi-manager premise CONFIRMED** (both described overlapping workflows); (2) **주민번호 in current Excel = first-7-digits only** (생년월일+성별, PIPA general personal information, not 고유식별정보) → captain-attestation pattern sufficient, 30-sec captain-confirmation task added to RIGHT NOW; (3) **Drive-backed architecture pivot considered and rejected** — user proposed storing member data in captain's Google Drive; once 주민번호 recharacterized, the PIPA urgency evaporated and original Supabase plan preserved; (4) **M1 demo framing confirmed** — captain has Daum cafe push on, so M1's killer story is filter+structure+one-tap-Band-draft, NOT raw speed (CEO review had already adjusted DESIGN.md line 61 — interview validated); (5) **members schema will add** birth_date + gender + address + phone + jersey_size + jersey_number, covered by W4 attestation; (6) **new product findings surfaced** → 3 open decisions moved to RIGHT NOW: M2 monthly-vs-event reframe, AI deposit classification placement, attendance tracking placement; (7) **NotificationListenerService idea parked** to TODOs (Play Store friction on general finance apps). 0 new BUILD.md decisions baked yet — pending the 3 RIGHT-NOW decisions.
- **2026-04-08 (post-late-evening)** — **`/plan-ceo-review` complete.** Mode: SELECTIVE EXPANSION. 6 expansions proposed → 3 accepted + 2 deferred + 1 skipped. 4 cross-model tensions surfaced via outside voice (Claude subagent, codex unavailable) and all resolved by user. **12 new BUILD.md decisions baked in (Decisions 22-33) on top of the existing 21 eng-review decisions.** Major changes: (1) **reframed killer-feature thesis** from "shared SoT between two equal managers" → "operations layer for amateur club captains" — multi-manager is a capability not the headline, M1 leads on demo, M2 is the depth play, M3 is the breadth play; (2) **Module 3 upgraded from W9 cut-valve to PROTECTED in MVP** — new cut valve = polish + Maestro coverage; (3) **Naver Band write-scope verification moved to W1 P0** alongside KIPRIS + captain interviews; (4) **PIPA captain-attestation pattern** added (W2 schema column + W4 UI checkbox + privacy policy section) to address third-party member PII legal exposure surfaced by outside voice; (5) **Sentry moved to W3** (was W11 polish); (6) **GitHub Actions CI added in W3**; (7) **cafe post link button added to W6** (15 min trust escape hatch); (8) **W12-13 acquisition prep block added** (cafe post wording, 홍보 rules, in-app KakaoTalk Sharesheet referral, in-person 60-sec demo script); (9) **Haiku schema decision register baked in** (multi-date posts, 수도권 전체, 오픈/친선, parser_confidence rubric); (10) **member deletion = anonymize pattern** (PIPA-compliant + audit-trail intact). Files changed: BUILD.md (12 new decisions + multiple section updates), DESIGN.md (v3 banner + CEO review section coming next), CLAUDE.md (voice + anti-conventions updated), TODOS.md (5 new entries with sharpened triggers), `notes/captain-interview-script.md` (NEW with 3 new questions). 0 unresolved decisions. ENG + CEO CLEARED.
- **2026-04-08 (late evening)** — **Design conviction clarified: Libero v1 has zero role-based UI branching.** All users inside a club see identical screens with identical permissions, regardless of whether they're labeled captain or 총무. The captain/secretary distinction is metadata only, not a permission gate. Role-based UI/permission differentiation is explicitly v2 work, deferred until ≥10 active captains complain about 권한 분리. Saved as feedback memory + added to TODOS.md as a v2 entry. The killer-feature framing simplifies to "shared source of truth between two equal managers" — drop the "captain's tool" / "secretary's tool" framings entirely.
- **2026-04-08 (late evening)** — Confirmed Android emulator is already API 34 (not API 24 as initially feared). No swap needed. Ready for Android testing in W3.
- **2026-04-08 (late evening)** — **🎉 REPO BOOTSTRAP COMPLETE.** First commit `d75574a init project for Libero` shipped. GitHub remote `https://github.com/hykhyk189/Libero` created as **PRIVATE** and pushed. 79 files tracked: Android Studio template (`kr.libero.captain`, minSdk 26, targetSdk 36, Compose theme scaffolding under `ui/theme/`), Supabase init with 3 edge function stubs, scratchpad scaffolding for W1 gates 1A and 1B, all 5 living docs (DESIGN/BUILD/PROGRESS/TODOS/CLAUDE). **Audited git tracked files for secret leaks: zero hits across `secrets/`, `*.env`, `*-firebase-adminsdk-*.json`, `google-services.json`, `*.keystore`, `*.jks`, captain interview patterns. .gitignore worked.** Day 0 → W1 bridge complete; only captain interviews + Play Console verification gate the start of W1. (Curiosity flagged: `scratchpad/01_scraper_fetch/CLAUDE.md` was auto-generated by `bun init` and got committed; check next session and decide whether to keep or delete.)
- **2026-04-08 (late evening)** — `.gitignore` audit against BUILD.md Part 1.5 turned up 4 missing critical patterns: `*.keystore`, `*.jks`, `notes/captain-interview-*.md` (PIPA), `scratchpad/02_excel_parse/samples/` (PIPA). Plus `.dev.vars` and top-level `.gradle/`. All added with prominent `🚨 SECRETS` and `🚨 PERSONAL DATA (PIPA)` section banners. BUILD.md Part 1.5 rewritten to point at the actual file as the source of truth instead of duplicating a stale snippet.
- **2026-04-08 (late evening)** — BUILD.md Part 0.6 patched: added `brew install deno` to local tooling list (Deno binary is required by the Deno VSCode extension and `supabase functions serve`, was missing from the install list and only surfaced when the user hit the Deno LSP error during repo bootstrap). `.gitignore` updated to track `.vscode/settings.json` (Supabase-generated Deno LSP scope is shared project state, not personal IDE prefs).
- **2026-04-08 (late evening)** — `.gitignore` created at project root with three layers of secret protection (`secrets/`, `google-services.json`, `*-firebase-adminsdk-*.json`). Repo bootstrap can now safely `git add .` without leaking credentials.
- **2026-04-08 (late evening)** — Fixed typo in `secrets/.env.supabase`: `ANTROPIC_API_KEY` → `ANTHROPIC_API_KEY` (would have caused silent "no API key" failures in W6 Haiku eval).
- **2026-04-08 (late evening)** — **Account setup complete (BUILD.md 0.3 - 0.6)**: Firebase project `libero-492707` live with Android app `kr.libero.captain` registered, Cloud Messaging auto-enabled, Authentication → Google enabled, Firestore deliberately disabled. `google-services.json` and Firebase Admin SDK private key saved to `secrets/`. Supabase project live in Tokyo region with URL + anon key + service_role key saved. Anthropic API key saved with $5 prepaid credit. Local tooling installed and verified: Temurin 17 (default JDK confirmed), Android Studio, Bun, Supabase CLI, Maestro CLI, gh CLI. Day 0 account/tooling phase done.
- **2026-04-08 (evening)** — Tester recruitment list locked in (12+ Android-using humans identified). Promote to Closed Testers in W11 once Play Console verification clears.
- **2026-04-08 (evening)** — Captain interview scope reduced from 3 to 2 — own club captain + own club secretary only. Other-club interviews deferred to v2 (see TODOS.md). BUILD.md updated to match.
- **2026-04-08 (evening)** — Google Play Console account created. $25 paid. Identity verification still pending (carry-over to RIGHT NOW).
- **2026-04-08 (evening)** — KIPRIS trademark check **PASSED**. Locked in: **"Libero (리베로)"**. Package name `kr.libero.captain` confirmed. No rebrand needed.
- **2026-04-08** — `PROGRESS.md` ownership transferred to Claude. `CLAUDE.md` created with the maintenance rule baked in. Memory entry saved so future sessions in any project carry the same convention. BUILD.md Part 1.6 updated to point at the real CLAUDE.md/TODOS.md/PROGRESS.md instead of embedded templates.
- **2026-04-08** — `PROGRESS.md` created at project root as the living state file.
- **2026-04-08** — `/plan-eng-review` complete. 13 issues + 13 outside voice findings. 21 decisions made. BUILD.md rewritten with all decisions baked in. TODOS.md created with v2 work + month-3 strategic decision deferral.
- **2026-04-08** — `/office-hours` follow-up: BUILD.md drafted as the execution companion to DESIGN.md
- **2026-04-08** — DESIGN.md APPROVED v2 (Android-first pivot, post-spec-review, post-rename to "리베로", post-revenue-model)
- **2026-04-08** — DESIGN.md spec review fixes applied (Play Console 14-day rule, .xls fallback, App Links domain mandatory note, etc.)
- **2026-04-08** — DESIGN.md draft (initial iOS-first version, then same-day Android-first pivot)
- **earlier** — `context-from-prior-session.md` captured (4 modules, captain-only scope, killer feature thesis)

---

## 📊 OVERALL PROGRESS

```
Pre-W1 homework  █████████░  Day 0 (KIPRIS ✓, Play Console ✓ pending verify, tester list ✓, accounts ✓, tooling ✓, repo bootstrap ✓ + GitHub push — only captain interviews remain)
W1 Gates         ░░░░░░░░░░  not started
W2 Backend       ░░░░░░░░░░  not started
W3 Android       ░░░░░░░░░░  not started
W4-7 Module 1    ░░░░░░░░░░  not started
W8-9 Module 2    ░░░░░░░░░░  not started
W10 Module 3     ░░░░░░░░░░  not started  ← cut decision at end of W9 if Naver Band scope not approved
W11 Submit       ░░░░░░░░░░  not started
W12-13 Review    ░░░░░░░░░░  not started
LIVE             ░░░░░░░░░░  not started
```

**Major milestones with dates as you hit them:**
- [x] First captain interview done — **2026-04-17** (both own-club captain and secretary, synthesis in `notes/captain-interview-synthesis.md`)
- [x] First commit pushed — **2026-04-08** (commit `d75574a`, pushed to private GitHub remote `hykhyk189/Libero`)
- [ ] W1 all 5 gates passed — date: ___
- [ ] W3 sign-in works on real Galaxy — date: ___
- [ ] W7 push notification → instant detail screen working on real device — date: ___
- [ ] W9 real KakaoBank export auto-matches ≥80% — date: ___
- [ ] W10 first Band auto-post lands (or formally cut) — date: ___
- [ ] W11 closed test enrolled, 14-day clock running — date: ___
- [ ] W13 Production rollout — date: ___
- [ ] First user other than yourself installs from Play Store — date: ___
- [ ] Month 3 — strategic CV vs SaaS decision — date: ___

---

## 📝 NOTES TO SELF (free-form, append as you go)

_Use this section to capture quick observations, surprises, or "I should remember this later" moments. These are not TODO items — they're scratchpad observations. If something here turns into real work, move it to RIGHT NOW or NEXT and delete from here._

- (empty)

---

## ⚙️ How this file is maintained

Claude maintains this file. You don't.

- **When you finish something:** tell Claude in plain language ("KIPRIS came back clean", "I did the captain interview with 김 captain", "Play Console signed up", "Gate 1A passes"). Claude moves the checkbox to ✅ DONE with today's date and updates anything else that's affected.
- **When Claude finishes coding something:** Claude updates this file in the same response, before reporting back. You don't have to ask.
- **If something blocks you:** tell Claude. Claude writes it in 🚧 with the reason.
- **If you change DESIGN.md or BUILD.md directly:** tell Claude what changed, so this file stays in sync.
- **If this file looks stale:** flag it. That's a Claude regression — Claude will fix it.
