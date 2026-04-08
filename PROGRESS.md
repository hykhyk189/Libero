# PROGRESS — 리베로

> **Living state file. Maintained by Claude, not by you.** Open this whenever you sit down to work. The answer to "what should I do next" lives in **🎯 RIGHT NOW**. When you finish something, just tell Claude and Claude updates this file. When Claude finishes coding something, Claude updates this file in the same response. You should never have to edit this file by hand.
>
> Reference docs: [DESIGN.md](DESIGN.md) (the what + why) · [BUILD.md](BUILD.md) (the how + when) · [TODOS.md](TODOS.md) (deferred work)

**Last updated:** 2026-04-08 (late evening)
**Current phase:** Pre-W1 homework
**Overall:** Day 0 of ~13 weeks — accounts + tooling done, captain interviews + repo bootstrap remain

---

## 🎯 RIGHT NOW

- [ ] **Complete Google Play Console identity verification** — account is created, ID verification still pending. The 14-day closed-testing clock can't fully start until verification clears. Check your email + the Play Console dashboard for whatever Google is asking for. (Waiting on Google, no action you can take besides responding to whatever they ask.)
- [ ] **Schedule the 2 captain interviews** (own club only, see scope note below)
  - Interview A: your own club's captain — date/time scheduled? ___
  - Interview B: your own club's secretary — date/time scheduled? ___
- [ ] **Repo bootstrap** — the next concrete codable thing. BUILD.md Part 1.4 has the exact commands. ~1 hour. First commit ships, `.gitignore` is already in place protecting `secrets/`. Can do this tonight or tomorrow morning.

**Scope note (revised 2026-04-08 evening):** dropped from 3 captain interviews to 2, both from your own club (captain + secretary). This is actually sharper than the original plan because both interviews are about the same multi-manager pair, so you'll see the actual handoff dynamics rather than guessing about strangers. Tradeoff: you lose the "do other clubs have the same pain" signal — captured as a v2 TODO instead.

**Next time you open this file:** if all three items above are checked, scroll to "📅 NEXT" — W1 gates are up.

---

## 📅 THIS WEEK (between tonight and W1 starting)

- [ ] **Captain interview A** — your own club's captain
  - Format: 30-60 min, walk-me-through-last-week, "show me your phone"
  - Ask the killer question: "Show me the last tournament you missed and tell me why"
  - Write up to `notes/captain-interview-captain.md` verbatim
- [ ] **Captain interview B** — your own club's secretary (총무)
  - Same format. Critical for validating the multi-manager handoff dynamic.
  - Listen for: where does the captain hand off to her, where does she sigh, what does she double-check
  - Write up to `notes/captain-interview-secretary.md` verbatim
- [ ] **Captain interview synthesis** — write `notes/captain-interview-synthesis.md`
  - Key question: did anything they said BREAK the design? If yes → re-run /office-hours and update DESIGN.md BEFORE W1
  - Specifically check: does the secretary actually do the KakaoBank reconciliation, or does the captain? That answer gates Module 2's UX assumptions.

**Done with this week when:** all captain interviews written up, synthesis exists, repo has its first commit (in RIGHT NOW), and Play Console verification has cleared.

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

Nothing blocked right now. Update this section if a gate fails, an interview reveals a design break, the captain doesn't respond, or any external dependency stalls.

---

## ✅ DONE

Newest first.

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
Pre-W1 homework  ████████░░  Day 0 (KIPRIS ✓, Play Console ✓ pending verify, tester list ✓, accounts ✓, tooling ✓ — captain interviews + repo bootstrap remain)
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
- [ ] First captain interview done — date: ___
- [ ] First commit pushed — date: ___
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
