# Libero — Claude Context

This is a personal project: a captain tool for Korean amateur 9인제 volleyball clubs.

- Read `BUILD.md` first — it has the week-by-week execution plan with eng-review decisions baked in. **BUILD.md is the source of truth on tech decisions** (Koin, no Room, Supabase Edge Functions, invite codes — all eng-review-era choices that supersede DESIGN.md's earlier text).
- Read `DESIGN.md` next (v3, post-CEO-review) — it has the product scope, the reframed thesis, and the locked decision log. DESIGN.md and BUILD.md should agree; if they don't, BUILD.md is more recent and wins, and the contradiction is a regression to fix.
- Read `PROGRESS.md` to see current state — what's done, what's in progress, what's next.
- Read `TODOS.md` for deferred work.

Voice: this is a CV/portfolio piece with optionality to evolve toward freemium SaaS. Quality matters. Korean cultural details matter.

**Reframed thesis (post-CEO-review 2026-04-08):** Libero is "the operations layer for amateur club captains." Multi-manager is a *capability*, not the *headline*. Module 1 (push notifications) leads on demo material, Module 2 (Excel-over-OCR) is the CV depth play, Module 3 (Band auto-post) is now MANDATORY in MVP (no longer the cut valve — polish + Maestro coverage cut first if needed). Retired: "shared source of truth between two equal managers" framing.

---

## 🚨 PROGRESS.md is YOUR job, not the user's

**You (Claude) maintain `PROGRESS.md`. The user does not edit it. Ever.**

Two triggers update PROGRESS.md, both mandatory:

1. **The user reports finishing something.** Examples: "I did the captain interview", "Play Console signed up", "Gate 1A passes", "KIPRIS came back clean — locking 리베로", "I sent the messages to the captains". When you hear any of these, immediately:
   - Find the relevant checkbox in PROGRESS.md (in 🎯 RIGHT NOW or 📅 THIS WEEK or 📅 NEXT)
   - Move it to ✅ DONE with today's date in `YYYY-MM-DD` format, newest first
   - If a phase boundary was crossed, promote items from NEXT into 🎯 RIGHT NOW
   - Update the **Last updated** field at the top
   - Update the **Current phase** field if it changed
   - Update the overall progress bar if a major milestone shifted

2. **You finish a coding task** (wrote files, ran a build, deployed something, completed an edit batch). Update PROGRESS.md immediately, **in the same response**, before the final summary back to the user. Don't wait to be asked.

The user should NEVER have to edit PROGRESS.md themselves. If you forget to update it after a completed task and notice next turn, fix it immediately without being asked — that's a regression to clean up.

### Style rules for PROGRESS.md edits

- Dates in `YYYY-MM-DD` format only
- DONE entries always go newest first
- Keep 🎯 RIGHT NOW to ≤5 items. If it grows past 5, the extra items belong in 📅 THIS WEEK or 📅 NEXT
- When you write a blocker into 🚧, give it one sentence — "Captain interview waiting on response from 김 captain since 2026-04-09" — not a paragraph
- Don't add new sections to PROGRESS.md without telling the user. The structure is intentional.
- If a checkbox you're moving to DONE has open sub-questions ("Result: ___"), don't move it until the user fills in the answer. Ask them.

### What NOT to put in PROGRESS.md

- Code snippets (those go in BUILD.md or the actual code)
- Strategic decisions (those go in DESIGN.md)
- Deferred work (that goes in TODOS.md)
- Reasoning or rationale (PROGRESS.md is a status file, not a journal)

---

## Project conventions

- Android package: `kr.libero.captain` (KIPRIS confirmed clean 2026-04-08, locked)
- Min Android SDK: 26 (Android 8.0)
- Database: Supabase Postgres (single source of truth)
- **No Room or DataStore in v1** — see TODOS.md, this is v2 work. v1 uses in-memory `StateFlow` only.
- Feature-first package structure: `tournaments/`, `payments/`, `club/`, `members/`, `auth/`, `band/`, `shared/`, `di/`
- DI: Koin, single `AppModule.kt`. **NOT Hilt.**
- Error/state pattern: `Resource<T>` sealed class with Loading/Success/Error. Compose `when` for exhaustive handling.
- All UI strings live in `strings.xml` from day 1, Korean only at MVP.
- `HardcodedText` lint promoted to error level — build fails on any hardcoded Korean string in UI.
- Privacy policy lives in `assets/privacy_policy.html`, displayed via in-app viewer.
- Excel parsing: fastexcel-reader on-device, wrapped in `withContext(Dispatchers.IO)`. Never in an Edge Function.
- FCM dispatch: parallelized via `Promise.allSettled` from day 1.
- Realtime subscriptions: server-side filtered by `club_id`, never whole-table.

## Anti-conventions (don't do these — eng review eliminated each one)

- ❌ Don't add Firestore — using Supabase Postgres
- ❌ Don't add a third auth provider beyond Sign in with Google. Kakao Login is v1.1 work.
- ❌ Don't add Vision OCR for payments — that decision was reversed in DESIGN.md (KakaoBank Excel parser instead)
- ❌ Don't add Room or DataStore in v1 — see TODOS.md
- ❌ Don't increase scraper polling cadence below 3 hours without revisiting Daum stealth tradeoff
- ❌ Don't use Hilt — using Koin
- ❌ Don't use Cloudflare Workers, Cloudflare Pages, or any Cloudflare product — eliminated by eng review (scraper lives in Supabase Edge Functions, no domain needed)
- ❌ Don't introduce App Links — using 6-character invite codes instead
- ❌ Don't buy a domain for any reason — `kr.libero.captain` is reverse-DNS convention, not domain ownership
- ❌ Don't write Excel parsing in an Edge Function — parsing stays on-device for PIPA compliance
- ❌ Don't write Compose Test (in-JVM UI tests) — using Maestro YAML for E2E flows
- ❌ Don't put dynamic content (member names, tournament titles) in `strings.xml`. That's only for static UI strings.
- ❌ Don't designate Module 3 (Band auto-post) as the cut valve. It is PROTECTED in MVP per the 2026-04-08 CEO review. If you have to cut something, cut Maestro coverage and polish first.
- ❌ Don't ship Module 2 without the W4 PIPA captain-attestation checkbox in MembersScreen. The captain has to attest she notified the member that their info is stored — required for PIPA legitimate-interest basis.

## When in doubt

- **Architecture question:** check BUILD.md's "Eng Review Decision Log" first, then DESIGN.md (DESIGN.md is the historical/product context, BUILD.md is the current tech truth)
- **"What week is this work in":** check BUILD.md Part 2
- **"What should I do next":** check PROGRESS.md 🎯 RIGHT NOW
- **"Is this deferred":** check TODOS.md
- **"Why was this decided":** check BUILD.md Eng Review Decision Log (21 items) AND BUILD.md CEO Review Decision Log (added 2026-04-08, see Part 0)
