<div align="center">

<img src="assets/doc-header.svg" alt="AFCM Documentation" width="100%"/>

[README](../README.md) · **Design** · [Terminology](TERMINOLOGY.md) · [References](REFERENCES.md) · [ADF SME Questions](ADF_SME_QUESTIONS.md)

</div>

# AFCM — Design Doc

Status: **Draft v0.1** — pre-implementation. Nothing in this doc is committed to code yet.
Owner: Tasman Dynamics
Depends on: CBA_A3
Soft-integrates with: TFAR or ACRE2 (optional — voice suppression hook only, not a hard dependency)
Consumed by: AFCM-Simulator (scenario authoring/UI tool — see that repo's own DESIGN.md)
Terminology: [docs/TERMINOLOGY.md](TERMINOLOGY.md) — canonical definitions for every clinical,
tactical-medicine, and radio-callings term used below; use it rather than re-defining terms
ad hoc in future doc updates.
SME review: [docs/ADF_SME_QUESTIONS.md](ADF_SME_QUESTIONS.md) — standalone question list for a
real ADF/RAAMC member to review; consolidates every open terminology/pharmacology/equipment
question below into one shareable doc.

---

## 1. Positioning

AFCM is a **replacement** medical layer, not an add-on to ACE3/KAT/ACM. It does not reuse
`ace_medical_engine` hitpoints, ACE wound classes, or KAT's treatment items — a server running
AFCM does not also run ACE3 medical or KAT. The design goal stated in the pitch is explicit:
outcomes come from simulated physiology and pharmacokinetics, not from "have item X, apply to
hitpoint Y."

This has one direct consequence for scope discipline: **every stat AFCM exposes has to be either
derived from a real physiological model or driven by real ADF/RAAMC protocol values** — not
placeholder numbers dressed up as realism. Where real clinical figures aren't available/verified,
that's called out explicitly in §8 rather than silently guessed.

---

## 2. Core Pillars → System Breakdown

### 2.1 The Lethal Triad Engine (`afcm_physiology`)
Models the classic trauma "lethal triad" as three coupled state variables that feed back into each
other rather than three independent bars:

| Variable | Driven by | Feeds back into |
|---|---|---|
| **Core temperature** | environment exposure, wet/wind chill, blood loss (cold fluid/blood loss lowers core temp), warming interventions (blankets, warmed IV fluid) | coagulopathy (cold blood clots poorly), cardiac rhythm risk at extremes |
| **Acid–base state** (acidosis) | tissue hypoperfusion from blood loss/shock, lactate accumulation | coagulopathy (enzymes in the clotting cascade are pH-sensitive — acidotic blood clots poorly), cardiac/neuro depression at extremes |
| **Coagulopathy** | direct function of temperature + acidosis + dilution (crystalloid/plasma-poor resuscitation dilutes clotting factors) | bleed rate multiplier — a coagulopathic patient bleeds faster from the *same* wound than a normothermic, well-perfused one |

The "engine" part: this isn't three meters ticking independently — treating only the visible bleed
(stopping external hemorrhage) without also correcting temperature and acid-base state should
still let a patient spiral into an unshockable arrest, because coagulopathy keeps compounding.
That's the core gameplay lesson the pillar is built to teach.

### 2.2 ADF Protocol Accuracy (`afcm_pharmacology`)
A pharmacokinetics model per drug (onset, peak, duration, dose-response, contraindications) rather
than a flat "apply = fixed effect" item action. Confirmed from the brief:

| Agent | Role in the model |
|---|---|
| **Ketamine** | Analgesia/dissociation — dose-dependent, doesn't suppress respiratory drive the way opioids do |
| **Penthrox** ("green whistle") | Self-administered inhaled analgesic — fast onset, short duration, ceiling-limited by dose supplied per unit |
| **Fentanyl lozenges** | Transmucosal opioid — slower onset than IV, respiratory depression risk at higher effective dose, interacts with the neuro/GCS model (§2.3) |
| **Calcium Gluconate** | Not an analgesic — corrects the **Calcium Triangle**: massive transfusion (large-volume blood product administration) causes citrate-driven hypocalcemia, and hypocalcemia at depth causes cardiac contractility failure independent of volume status. A medic who transfuses aggressively without correcting calcium can push a *volume-resuscitated* patient into unshockable arrest — this is the pillar's signature "you did the obvious thing and it wasn't enough" moment |

**Reference defibrillator: LIFEPAK 15.** The Calcium Triangle's payoff only lands if
"unshockable" is a real mechanic, not just flavour text — the patient goes into cardiac arrest,
gets hooked up to a monitor/defibrillator, and the device tells the medic whether the rhythm is
shockable. **LIFEPAK 15** (Physio-Control/Stryker) is the reference device: it's the monitor/
defibrillator standard across Australian ambulance services, so it's the practical choice for
"Aussie standard" even without a confirmed ADF-specific procurement source. Relevant modelled
behaviour (TERMINOLOGY.md §9, sourced from the manufacturer data sheet):
- **AED mode** (automated rhythm analysis, prompted shock/no-shock) vs. **Manual mode**
  (operator reads the rhythm and decides) — AFCM should model Manual mode, consistent with a
  trained-medic simulation rather than a bystander-AED one.
- **Shockable vs. unshockable rhythm** as the actual gate on whether defibrillation can do
  anything — a Calcium-Triangle-driven arrest should present as unshockable, so shocking it
  repeatedly doesn't work and only correcting calcium does.
- **Escalating biphasic energy (up to 360J)**, **noninvasive pacing**, and **12-lead ECG/ST
  monitoring** are real LIFEPAK 15 capabilities worth keeping available as interactions even if
  the Calcium Triangle scenario is the headline use case — a full arrest/cardiac-monitoring loop
  is more reusable than a single scripted moment.
- This is new physiology-engine scope (a cardiac rhythm state on `PatientState`, §3, and a
  monitor/defibrillator interaction in `afcm_ui`) — not yet reflected in the repo layout (§7) or
  phased roadmap (§9) below; flagged here so it doesn't get lost before those sections are revised.

Each drug needs an explicit interaction/contraindication table (e.g., stacking two respiratory
depressants) before implementation — not just an isolated effect curve per drug.

### 2.3 Dynamic Realism
Three sub-features under one pillar, split into two subsystems:

- **Physics-based airway occlusion** (`afcm_airway`) — patency is a function of position (supine
  vs. recovery position vs. seated), obstruction sources (blood, vomit, tongue in an unconscious
  casualty), and intervention (recovery position, airway adjuncts). This is a physical/positional
  simulation, not a checkbox item.
- **GCS-based neurological system** (`afcm_neuro`) — tracks eye/verbal/motor response (see
  TERMINOLOGY.md §5 for the standard GCS component definitions) as an actual derived state (from
  blood loss, hypoxia from airway compromise, head injury, drug effects — e.g. ketamine
  dissociation or fentanyl sedation lowering verbal/eye score), and **that GCS state suppresses
  voice projection through TFAR/ACRE2** when integrated (TERMINOLOGY.md §7) — a patient with a
  depressed verbal score should be mechanically unable to communicate clearly over radio/proximity
  voice, not just visually tagged as "unconscious."
- **Stress-linked performance matrix** (`afcm_stress`) — the *medic's* stress state (under fire,
  time pressure, mass-casualty load) degrades their own performance: e.g. procedure time, hand
  steadiness/dosing precision, or task failure chance. This is explicitly about the treater, not
  the patient — a second state machine, not an extension of the patient's physiology model.

---

## 3. Patient State Model

```
PatientState = {
    core_temp_c:      Number,   // °C
    ph:                Number,   // arterial pH proxy
    lactate:            Number,   // acidosis contributor, rises under hypoperfusion
    coagulation_factor: Number,   // 0..1, derived from temp+ph+dilution, not independently set
    blood_volume_pct:   Number,   // % of baseline
    calcium_level:       Number,   // relative to normal range; driven down by transfusion volume
    gcs: { eye: 1..4, verbal: 1..5, motor: 1..6 },
    airway_patency:      Number,   // 0..1
    active_drugs: [ { agent, dose, administeredAt, route } ],
    bleeds: [ { site, rate, external: Bool } ],  // rate is itself a function of coag_factor, not fixed at wound time
    cardiac_rhythm: Enum,       // e.g. perfusing / VF / pulseless-VT / PEA / asystole — derived, not directly set
    shockable: Bool             // derived from cardiac_rhythm; gates whether LIFEPAK-style defibrillation can act
}

MedicState = {
    stress_load: Number,          // rises under fire / MASCAL / time pressure
    performance_modifier: Number  // derived from stress_load; applied to procedure timing/precision
}
```

Coagulation factor, bleed rate, and GCS are explicitly **derived**, not stored-and-edited directly
— that's what keeps the triad "an engine" instead of three separate sliders a medic (or a
mission/preset author) can just set.

---

## 4. Simulation Loop & Multiplayer Authority

Consistent with the rest of the TasDyn stack's principle (`about.md`: deterministic, zero
desync, validation on an authority machine):

- **Server/host-authoritative tick**: `PatientState` is simulated server-side (or by the logical
  host in non-dedicated play) on a fixed low-rate tick (e.g. 1–2 Hz — physiology doesn't need
  frame-rate resolution). Clients receive state deltas, not raw per-frame values.
- **Interventions are requests**: a medic applying a drug or an intervention sends a request; the
  server validates (dose sanity, route, contraindication check) and applies it to the authoritative
  state, then the result propagates back down. This mirrors the request→authority→domain-call
  pattern already used elsewhere in TasDyn's design work.
- **Local prediction for feel**: purely cosmetic/UI feedback (e.g. an injection animation) can play
  optimistically client-side without waiting on the round-trip; anything that affects actual
  PatientState waits on server confirmation.

---

## 5. TFAR/ACRE2 Integration

Soft dependency only — AFCM must run without either installed (GCS/consciousness still functions;
only the radio-suppression hook is skipped). Needs a compatibility-layer addon
(`afcm_ui`/a small dedicated compat PBO — TBD in §8) that:
- Detects which comms mod (if any) is loaded.
- On GCS verbal-score drop below a threshold, calls into that mod's exposed API to suppress or
  degrade outgoing voice transmission.
- Needs the actual TFAR/ACRE2 public API surface confirmed before implementation — this doc
  assumes both expose *some* programmatic hook for suppressing a unit's transmission, which needs
  verification against their current public function lists, not assumed from mod age/reputation.

---

## 6. Relationship to AFCM-Simulator

AFCM-Simulator is the scenario-authoring/training tool (limb/injury selection UI, presets,
randomization difficulty, patient spawner, stretcher placement, map tool) described in its own
repo. With AFCM now existing as a standalone engine:

- AFCM-Simulator's "apply an injury to a patient" action should call into **AFCM's own state-
  mutation API** (an entry point that sets initial `bleeds`/trauma state on a `PatientState`) as
  its primary target, rather than translating into ACE3 hitpoint damage.
- ACE3/KAT/ACM compatibility (mentioned in AFCM-Simulator's README) becomes an **optional
  secondary bridge** for servers not running AFCM at all — lower priority than the AFCM-native
  path, and should not shape AFCM's own data model (§3) to accommodate ACE3/KAT concepts like
  hitpoints or wound classes that AFCM doesn't use internally.
- This means AFCM needs to publish a stable, documented API (function list + event names) for
  external callers (AFCM-Simulator, mission makers) — not just internal state mutation.

---

## 7. Repo / Module Layout

```
AFCM/
  addons/
    afcm_main/            # CfgPatches root, CBA requirement, shared macros/constants
    afcm_physiology/       # Lethal Triad engine: temp, acid-base, coagulopathy, bleed-rate derivation,
                            # + cardiac rhythm / shockable-rhythm derivation (§2.2)
    afcm_pharmacology/     # drug catalog, pharmacokinetics, interactions (depends on afcm_physiology)
    afcm_airway/            # positional airway occlusion model (depends on afcm_physiology)
    afcm_neuro/              # GCS derivation, consciousness, comms-suppression hook point
    afcm_stress/             # medic stress/performance matrix (depends on afcm_neuro for shared state patterns)
    afcm_ui/                  # AFCM's own treatment interaction UI (native dialogs / ACE-interaction-menu-style),
                              # + LIFEPAK-15-modelled monitor/defibrillator interaction (§2.2)
  docs/
    DESIGN.md                # this file
```

---

## 8. Open Questions (need your call / SME input before implementation)

1. **Clinical reference values** — actual dosing, onset/duration curves, and threshold values for
   Ketamine/Penthrox/Fentanyl/Calcium Gluconate need a real ADF/RAAMC-aligned reference before
   they're hardcoded; this doc intentionally does not invent numbers. See
   [docs/REFERENCES.md](REFERENCES.md) for the source set to work from — no public ADF-P-Health/
   RAAMC doctrine document was found, so the strongest available sources are Therapeutic
   Guidelines/Australian Prescriber (Australian-context prescribing) and ATMA/JMVH (Australian
   tactical-medicine framing); real ADF-affiliated SME input would beat all of them.
2. **TFAR/ACRE2 API** — needs the current public function list from each mod confirmed before
   `afcm_neuro`'s comms hook is designed in detail; assumed-feasible here, not verified.
3. **Where does the comms-compat layer live?** — a dedicated `afcm_comms_compat` addon (loads only
   if TFAR/ACRE2 detected) vs. folding it into `afcm_neuro` with a runtime check. Leaning toward a
   separate optional addon so `afcm_neuro` has zero knowledge of either mod's internals.
4. **Tick rate and network cost** — 1–2 Hz is a starting assumption for §4; needs validation once
   there's a real player-count/MASCAL-scale target (ties into AFCM-Simulator's own open question
   about max simultaneous patients).
5. **UI approach** — same native-dialog-first decision AFCM-Simulator made applies here by default,
   but worth confirming AFCM's own treatment UI (`afcm_ui`) is in scope for this repo rather than
   assumed to be supplied by AFCM-Simulator.
6. **Terminology accuracy vs. marketing framing** — **updated, stronger than first drafted.**
   A follow-up pass checking official/near-official ADF sources (defence.gov.au, army.gov.au, The
   Cove, JMVH) confirmed real Australian Army sourcing for: CASEVAC/MEDEVAC (platform-protection
   distinction), **Role 2 Basic / Role 2 Enhanced / Role 2 Forward** (with real capability figures
   — see TERMINOLOGY.md §3), and **Combat First Aider (CFA)** — a real 12-day-course qualification
   explicitly built on the TCCC handbook, which also upgrades TCCC/MARCH/PAWS from "assumed
   applicable" to "confirmed ADF-adopted framework." Still unconfirmed from a public source: the
   exact T1–T4 triage prefix (Australian Army sourcing uses "priority-one casualty" phrasing
   instead — TERMINOLOGY.md §2), the 9-Liner and MIST report formats, and Role 1/Role 3 by name
   (only Role 2's sub-variants turned up detail). Named-but-inaccessible doctrine publications
   (ADFP 53, ADDP 1.3, ADDP 3.20 — REFERENCES.md) may resolve the remaining gaps if anyone can
   access them through Defence channels; direct fetches of army.gov.au/cove pages were unreliable
   during this research pass (timeouts/connection resets) and are worth manually re-checking.
   Net: the README's "ADF and RAAMC trauma doctrine" framing now has real backing for its core
   claims (echelons of care, point-of-injury role, TCCC basis) — the remaining unconfirmed items
   are specific formats/labels, not the doctrine framing itself.
7. **Cardiac rhythm / defibrillation scope** — §2.2 adds `cardiac_rhythm`/`shockable` to
   `PatientState` and a LIFEPAK-15-modelled monitor/defibrillator interaction, but this is new
   scope not yet sized against the phased roadmap (§9) — needs a call on whether it's a v2
   (Calcium Triangle needs it to have teeth) or v3+ item, and whether AED-mode-style guided
   defibrillation is worth building at all if AFCM is targeting trained-medic play (Manual mode
   only, per TERMINOLOGY.md §9).

---

## 9. Phased Roadmap

- **v0.1 (this doc)** — design only.
- **v1** — `afcm_physiology` core loop (temp/acidosis/coagulopathy feedback, derived bleed rate),
  server-authoritative sync, no pharmacology yet.
- **v2** — `afcm_pharmacology` (Ketamine, Penthrox, Fentanyl, Calcium Gluconate + Calcium Triangle
  interaction with transfusion) + `cardiac_rhythm`/`shockable` state and LIFEPAK-15-modelled
  manual-mode defibrillation, so the Calcium Triangle's "unshockable arrest" payoff is a real
  mechanic rather than narrative text (open question §8.7 — sizing/scope still needs a call).
- **v3** — `afcm_airway` + `afcm_neuro` (GCS derivation, positional airway model).
- **v4** — `afcm_stress` (medic performance matrix) + TFAR/ACRE2 comms-suppression integration.
- **Parallel** — AFCM-Simulator's injury-application path migrates from placeholder/ACE-bridge to
  AFCM's native API as each subsystem lands.

---

<div align="center">

**Tasman Dynamics** — Engineering high-fidelity systems for the future of multi-domain simulation.
[Discord](https://discord.gg/RmCkSuSHRa) · [AFCM-Simulator](https://github.com/A3-TasmanDynamics/AFCM-Simulator)

</div>
