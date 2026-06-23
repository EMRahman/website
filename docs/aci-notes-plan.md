# Plan: ACI Notes Section — index.html

## Context
Personal notes on Air Crash Investigations episodes, observing that every lesson maps to complex software production systems. Goal: add a new styled section to the existing observatory portfolio site that:
1. Summarises findings under prioritised themes (most → least relevant to software)
2. Lists every incident with a link to the real flight on Wikipedia where identifiable
3. Matches the site's dark terminal / aviation-control aesthetic

The site is a **single-file** React portfolio (`index.html`) using a custom `x-dc` component system. All content is inline HTML. No build step.

---

## Notes on episode numbering
The "SE10" episodes appear to correspond to Season 11 in the standard Mayday/Wikipedia numbering (common when streaming services package specials differently). The identified flights have been verified against the actual incidents regardless of episode numbering.

---

## What changes

### 1. Add nav link in topbar (line ~58)
Add `./aci-logs` anchor alongside existing `./about`, `./projects`, `./contact` links.

### 2. New section in `<main>` (insert before `<!-- CONTACT -->`, around line 225)
Panel title: `INCIDENT LOGS · ACI` / right label: `$ cat aci-lessons.md`
Anchor id: `aci-logs`

Section content has two parts:

#### Part A — Prioritised Themes
Eight themes ranked by software relevance. Each is a row with a coloured severity badge (green/amber/red), theme name, concise summary, and cross-references to the incidents below.

| Priority | Theme | Badge colour |
|---|---|---|
| 1 | Alert Fatigue & Monitoring Erosion | #ef6b6b (red) |
| 2 | Automation Authority & Trust | #ef6b6b |
| 3 | Failsafe Design & Lab-vs-Production Testing | #f0b73f (amber) |
| 4 | Sensor & Data Integrity | #f0b73f |
| 5 | Cost Pressure / Technical Debt | #f0b73f |
| 6 | Checklist / Procedural Discipline Under Pressure | #46e2a0 (green) |
| 7 | Communication Protocols & Authority Clarity | #46e2a0 |
| 8 | Hierarchy, Whistleblowers & Systemic Accountability | #46e2a0 |

Summaries per theme (to be adjusted by author):

- **Alert Fatigue** — Controllers/pilots disabled or ignored safety systems because noise exceeded signal. SE10 E8 (13 collision alerts/day), SE10 E12 (runway sensors deactivated), S19 E8 (GPWS circuit breaker pulled), Qantas 32 (serial A380 alerts). _In software: alert fatigue is one of the fastest paths from "monitored" to "dark"._
- **Automation Authority** — Who wins when the machine and the human disagree? Überlingen (TCAS vs ATC tone of voice), Qantas 72 (fly-by-wire overrode pilots on bad sensor data), TransAsia 235 (pilot acted before diagnosing, then shut off the wrong engine). _In software: automation must degrade gracefully and operators must understand what it is doing and why._
- **Failsafe Design / Testing Gaps** — Lab conditions never matched flight conditions. SE14 E6 (propeller feathering tested flat, not in airflow), SE10 E11 (engine water-ingestion only tested at high speed), United 811 / DC-10 cargo door (known failure mode covered up). _In software: integration tests in a staging environment that never resembles prod are effectively decoration._
- **Sensor & Data Integrity** — A single faulty reading piped into critical automation causes a cascade. AeroPeru 603 (tape over static ports, wrong data fed to autopilot), Birgenair 301 (pitot tube nest after 3-week park, same pathology), Qantas 72 (mislabelled ADIRU data triggered violent pitch), Air Canada 143 (unit conversion error; no automated fuel check). _In software: validate every sensor boundary; reconcile redundant readings; never silently accept a default._
- **Cost Pressure / Technical Debt** — Maintenance shortcuts accumulate risk invisibly. Alaska 261 (jackscrew interval extended 400%; whistleblower overruled), Air Transat 236 (wrong engine part fitted to cut costs), Swissair 111 (flammable wiring kept in fleet due to retrofit cost), Air Midwest 5481 (subcontracted maintenance with no independent check), BA 5390 (window designed to be bolted from outside — suboptimal design raised maintenance complexity and risk of error). _In software: deferred reliability work is a loan; it compounds._
- **Checklist Discipline** — Time pressure causes procedural steps to be skipped; the step that gets skipped is the one that kills you. American 1420 / Delta 1141 (unarmed spoilers/flaps), TransAsia 222 (rushed checklist, flew below minimums), TransAsia 235 (no diagnosis before action). _In software: deployment runbooks and incident checklists must be enforced, not advisory._
- **Communication Protocols** — Informal language fails in emergencies. Avianca 052 (said "priority", not "MAYDAY"; ATC did not act). Überlingen (TCAS issued resolution advisories in a neutral voice; ATC shouted in Russian; pilots obeyed ATC). _In software: on-call escalation paths must have unambiguous severity levels that override social friction._
- **Hierarchy & Accountability** — Senior overrides junior's correct call; designers blame operators; scapegoating prevents systemic fixes. First Air 6560, Alitalia 404, DC-10 cargo door (cover-up), Überlingen ATC (charged with manslaughter, later murdered). _In software: blameless post-mortems and protected escalation channels matter precisely because the pattern without them is well-documented._

---

#### Part B — Full Incident Reference Table
All episodes listed, sorted by software-priority tier.

| Tier | Ep ref | Flight | Year | Key lesson | Link |
|---|---|---|---|---|---|
| 1 | SE10 E8 | ATC collision alert incident | — | 13 false TCAS alerts/day → alert habit broken → blind | (episode unidentified) |
| 1 | SE10 E12 "The Invisible Plane" | Linate Airport disaster — SAS Flight 686 | 2001 | Runway incursion sensors deactivated due to false-alarm saturation (triggered by hares) | https://en.wikipedia.org/wiki/Linate_Airport_disaster |
| 1 | S19 E8 "No Warning" | Trigana Air Flight 267 | 2015 | Bad terrain DB → GPWS false alerts → crew pulled circuit breaker and never reset it; no GPWS on fatal approach | https://en.wikipedia.org/wiki/Trigana_Air_Flight_267 |
| 1 | Qantas 32 | Qantas Flight 32 | 2010 | Engine burst triggered cascading A380 alerts; hierarchical alerting needed to surface root cause | https://en.wikipedia.org/wiki/Qantas_Flight_32 |
| 2 | S2 E4 "Deadly Crossroads" | Überlingen mid-air collision | 2002 | TCAS issued neutral RA; ATC gave authoritative verbal override; Russian crew obeyed ATC — no global authority protocol | https://en.wikipedia.org/wiki/%C3%9Cberlingen_mid-air_collision |
| 2 | "Free Fall" | Qantas Flight 72 | 2008 | Mislabelled ADIRU input data caused fly-by-wire to pitch down violently; software bug | https://en.wikipedia.org/wiki/Qantas_Flight_72 |
| 2 | "Caught on Tape" | TransAsia Airways Flight 235 | 2015 | Sensor error shut off good engine; pilot acted before diagnosing; didn't trust automation's checklist | https://en.wikipedia.org/wiki/TransAsia_Airways_Flight_235 |
| 2 | "Flying Blind" | AeroPeru Flight 603 | 1996 | Tape over static ports during cleaning; wrong data fed to autopilot; pilots not trained to distrust instruments | https://en.wikipedia.org/wiki/Aeroperu_Flight_603 |
| 3 | SE14 E6 | Propeller feathering incident | — | Feathering tested in lab (static, flat surface) but not in airflow; blade went 87° to wind — plane uncontrollable | (episode unidentified; flight TBD) |
| 3 | SE10 E11 "Nowhere to Land" | TACA Airlines Flight 110 | 1988 | Engines certified for water ingestion at high speed only; flamed out at low speed; crew landed on grass levee | https://en.wikipedia.org/wiki/TACA_Airlines_Flight_110 |
| 3 | "Flight 811" | United Airlines Flight 811 | 1989 | Cargo door failsafe not designed for airframe pressure; risk known and suppressed; initial blame on operators | https://en.wikipedia.org/wiki/United_Airlines_Flight_811 |
| 3 | DC-10 cargo door | Turkish Airlines Flight 981 | 1974 | Design flaw confirmed in testing, covered up; same pattern: blame operator first | https://en.wikipedia.org/wiki/Turkish_Airlines_Flight_981 |
| 4 | "Pitot Tube block" | Birgenair Flight 301 | 1996 | Wasp nest in pitot after 3-week park; autopilot slaved to faulty instrument; co-pilots didn't override captain | https://en.wikipedia.org/wiki/Birgenair_Flight_301 |
| 4 | "Gimli Glider" | Air Canada Flight 143 | 1983 | Manual lbs-to-kg conversion error; no automated fuel-sufficiency validation; NASA made the same error | https://en.wikipedia.org/wiki/Gimli_Glider |
| 4 | SE20 E8 "Caught in a Jam" | Ansett New Zealand Flight 703 | 1995 | Continuation bias; committed to landing with gear not confirmed down; should have gone around | https://en.wikipedia.org/wiki/Ansett_New_Zealand_Flight_703 |
| 5 | S1 E5 "Cutting Corners" | Alaska Airlines Flight 261 | 2000 | Jackscrew inspection interval extended 400%; specific component flagged for replacement but overruled; no failsafe | https://en.wikipedia.org/wiki/Alaska_Airlines_Flight_261 |
| 5 | Air Transat 236 | Air Transat Flight 236 | 2001 | Incompatible engine part fitted to cut costs; fuel leak masked by frequent Airbus false alerts | https://en.wikipedia.org/wiki/Air_Transat_Flight_236 |
| 5 | "Flight 111, 1998" | Swissair Flight 111 | 1998 | Flammable entertainment wiring; global electrical constraints not propagated to new design; retrofit costs kept risk in-fleet | https://en.wikipedia.org/wiki/Swissair_Flight_111 |
| 5 | Charlotte commuter | Air Midwest Flight 5481 | 2003 | Subcontracted maintenance with no independent check; weight calculated on population averages not actuals | https://en.wikipedia.org/wiki/Air_Midwest_Flight_5481 |
| 5 | "British Airways Flight" (windshield) | British Airways Flight 5390 | 1990 | Window designed to be bolted from outside — suboptimal design raised maintenance complexity and risk of error | https://en.wikipedia.org/wiki/British_Airways_Flight_5390 |
| 6 | American 1420 | American Airlines Flight 1420 | 1999 | Spoilers not armed; checklist step missed in hectic landing; getthererytis | https://en.wikipedia.org/wiki/American_Airlines_Flight_1420 |
| 6 | "Deadly Distraction" | Delta Air Lines Flight 1141 | 1988 | Rushed takeoff checklist; flaps not set; takeoff warning system itself failed | https://en.wikipedia.org/wiki/Delta_Air_Lines_Flight_1141 |
| 6 | "Blow Away" | TransAsia Airways Flight 222 | 2014 | Rushed checklist; flew below minimum altitude; company expanding too fast, SOPs systemically ignored | https://en.wikipedia.org/wiki/TransAsia_Airways_Flight_222 |
| 7 | S2 E6 "Missing over New York" | Avianca Flight 052 | 1990 | "Priority" is not "MAYDAY"; informal phrasing failed to trigger ATC emergency response | https://en.wikipedia.org/wiki/Avianca_Flight_052 |
| 7 | "Southern Storm" | Southern Airways Flight 242 | 1977 | Weather report not pursued; ATC unaware of nearby airport; pilots didn't understand engine physics in heavy rain | https://en.wikipedia.org/wiki/Southern_Airways_Flight_242 |
| 7 | "Microburst" | Delta Air Lines Flight 191 | 1985 | Microburst phenomenon not yet understood by either ATC or crew | https://en.wikipedia.org/wiki/Delta_Air_Lines_Flight_191 |
| 8 | S2 E4 (ATC scapegoat) | Überlingen — Peter Nielsen | 2002 | ATC charged with manslaughter despite systemic staffing/maintenance failures; later murdered | https://en.wikipedia.org/wiki/%C3%9Cberlingen_mid-air_collision |
| 8 | "First Air Flight 6560" | First Air Flight 6560 | 2011 | Pilot refused co-pilot's correct ILS malfunction warning; power struggle | https://en.wikipedia.org/wiki/First_Air_Flight_6560 |
| 8 | "Deadly Inclination" | Alitalia Flight 404 | 1990 | Junior FO's go-around call was correct; overruled; failed equipment showed OK signal | https://en.wikipedia.org/wiki/Alitalia_Flight_404 |
| 8 | "Data distraction" | Eastern Air Lines Flight 401 | 1972 | Entire crew focused on landing-gear indicator; no one flying | https://en.wikipedia.org/wiki/Eastern_Air_Lines_Flight_401 |
| misc | BA ice crystals | British Airways Flight 38 | 2008 | Temporal failure (ice crystals) vanished before investigation; only simulatable; steady-state testing is insufficient | https://en.wikipedia.org/wiki/British_Airways_Flight_38 |
| misc | SE10 E3 "Breakup Over Texas" | Continental Express Flight 2574 | 1991 | Screws left off left horizontal stabilizer after shift change; inspector doing wrong role; no handover checklist | https://en.wikipedia.org/wiki/Continental_Express_Flight_2574 |
| misc | SE20 E9 "Seconds from Touchdown" | Propair Flight 420 | 1998 | Brake pad overheating caused wing structural failure; no brake-pad monitoring; safety bulletin released in newsletter not manual | https://en.wikipedia.org/wiki/Propair_Flight_420 |
| misc | S2 E4 "Deadly Crossroads" (phone line) | Überlingen | 2002 | Phone line down for maintenance with no alert; comms infrastructure is as safety-critical as flight systems | https://en.wikipedia.org/wiki/%C3%9Cberlingen_mid-air_collision |
| misc | Swiss hill crash | Crossair Flight 3597 | 2001 | ILS off for noise abatement; pilot overconfident; first officer did not challenge; no go-around | https://en.wikipedia.org/wiki/Crossair_Flight_3597 |
| misc | "Ditch the Plane" S7 E8 | Tuninter Flight 1153 | 2005 | Wrong fuel gauge (ATR-42 gauge in ATR-72) showed more fuel than present; incompatible part accepted silently | https://en.wikipedia.org/wiki/Tuninter_Flight_1153 |

---

## Files to modify (when implementing)

| File | Change |
|---|---|
| `index.html` | (1) Add `./aci-logs` anchor in topbar nav (~line 58); (2) Insert new `<section id="aci-logs">` before the `<!-- CONTACT -->` comment (~line 225) |

No JS changes needed — the section is purely static HTML, no reactive state.

---

## Styling conventions to match
- Section wrapper: `border:1px solid rgba(120,160,145,0.14);background:rgba(16,22,20,0.4);border-radius:5px;`
- Header bar: `padding:11px 16px;border-bottom:1px solid rgba(120,160,145,0.12);font-family:'JetBrains Mono',monospace;font-size:10.5px;letter-spacing:0.15em;color:#7fb8ad;`
- Body padding: `class="sec-pad" style="padding:42px 44px;"`
- Priority badges use existing colour variables: `#ef6b6b` (critical), `#f0b73f` (warning), `#46e2a0` (ok)
- Links: `color:#5bc8d6;`

---

## Verification (post-implementation)
1. Open `index.html` directly in a browser
2. Confirm the topbar nav link scrolls to the new section
3. Confirm all Wikipedia links open correctly
4. Confirm section renders correctly on mobile (≤760px breakpoint)
