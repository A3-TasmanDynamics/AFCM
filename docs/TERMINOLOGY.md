# Terminology & Callings

Canonical glossary for AFCM and AFCM-Simulator — clinical terms, tactical-medicine doctrine terms,
radio callings/voice-procedure terms, and the mods' own internal jargon. Purpose: keep every doc,
UI label, and variable name using the same word for the same thing, and keep real-world terms from
getting misused or blurred with gameplay-only naming.

Every entry is tagged with how confident the sourcing is:
- **[Confirmed]** — found in an ADF-specific source ([REFERENCES.md](REFERENCES.md)) or is
  unambiguous plain clinical terminology.
- **[NATO/US-common]** — extremely widely used across NATO militaries and prehospital trauma care,
  almost certainly applicable in an ADF context, but not confirmed against an ADF-specific
  published source. Safe to use for AFCM's fiction/UI; don't present as verified ADF doctrine
  without a real source.
- **[Gameplay-only]** — a term AFCM/AFCM-Simulator invented; not a real clinical/military term,
  flagged so it never gets mistaken for one in later docs.

---

## 1. Casualty Evacuation

| Term | Definition | Status |
|---|---|---|
| **CASEVAC** (Casualty Evacuation) | Movement of a casualty using a non-dedicated, multimission-capable ("designated") platform not specifically protected under the Geneva Protocols | **[Confirmed]** — JMVH, *Casualty Evacuation in the Australian Defence Force* |
| **MEDEVAC** (Medical Evacuation) | Movement of a casualty using a dedicated, purpose-built/-operated medical platform (road, sea, or air), protected under the Geneva Protocols | **[Confirmed]** — same source. The CASEVAC/MEDEVAC line is drawn by *platform protection status*, not by urgency or distance — a common misconception is that MEDEVAC just means "faster/more urgent," which isn't the actual distinction |
| **Forward / Tactical / Strategic CASEVAC** | Operational-level distinctions in evacuation stage (point-of-injury forward movement → tactical-level movement → strategic/long-range movement) | **[Confirmed]** — same source |
| **Casualty regulation** | The process of ensuring the *right* casualties, at the *right* time, in the *right* order, by the *right* means, reach the *right* destination medical facility | **[Confirmed]** — same source, direct quote |
| **9-Liner (MEDEVAC request)** | Standardised 9-line radio request format for requesting evacuation: (1) pickup location, (2) freq/callsign, (3) patient count/precedence, (4) special equipment, (5) casualties by type, (6) pickup-site security, (7) site marking method, (8) casualty nationality/status, (9) NBC/terrain-in-the-area notes (variant field) | **[NATO/US-common]** |
| **MIST report** | Casualty handover format: **M**echanism of injury, **I**njuries sustained, **S**igns/symptoms (pulse, BP, resp rate), **T**reatment rendered | **[NATO/US-common]** |

## 2. Triage Categories

| Term | Definition | Status |
|---|---|---|
| **T1 / Red** | Immediate — requires immediate life-saving intervention | **[NATO/US-common]** |
| **T2 / Yellow** | Urgent — requires significant intervention within roughly 2–4 hours | **[NATO/US-common]** |
| **T3 / Green** | Delayed — needs treatment, but it can safely wait | **[NATO/US-common]** |
| **T4 / Blue** | Expectant — unlikely to survive given available resources; treated if/when capacity allows, not abandoned | **[NATO/US-common]** |
| **Dead / Black** | Deceased | **[NATO/US-common]** |

> The military convention prefixes categories with **T** (T1–T4); the civilian/ambulance
> convention uses **P** (P1–P3) instead. AFCM-Simulator's gameplay-facing "injury difficulty"
> levels (Easy/Medium/Hard/Extreme/F*CKED!, see §6) are a **separate, gameplay-only** scale and
> must not be presented as, or silently mapped onto, T1–T4 — they measure scenario-authoring
> difficulty, not clinical urgency. If a future feature needs real triage categorisation (e.g. a
> MASCAL sorting exercise), it should use T1–T4 explicitly rather than repurposing the difficulty
> labels.

## 3. Echelons of Care

| Term | Definition | Status |
|---|---|---|
| **Role 1** | Point-of-injury first aid through Battalion Aid Station-level care — broadly analogous to prehospital EMS care | **[NATO/US-common]** |
| **Role 2** | First echelon providing damage-control surgery, blood product transfusion, limited imaging/lab, combat/operational stress control | **[NATO/US-common]** |
| **Role 3** | Full medical treatment facility — resuscitation, initial wound surgery, damage-control surgery, postoperative care | **[NATO/US-common]** |

## 4. TCCC Phases & Assessment

| Term | Definition | Status |
|---|---|---|
| **Care Under Fire (CUF)** | First TCCC phase — care rendered while still under effective hostile fire; priority is fire superiority and moving the casualty to cover | **[NATO/US-common]** |
| **Tactical Field Care (TFC)** | Second phase — casualty and provider no longer under direct effective fire; more deliberate assessment/treatment | **[NATO/US-common]** |
| **Tactical Evacuation Care (TACEVAC)** | Third phase — care rendered while preparing for and during evacuation | **[NATO/US-common]** |
| **MARCH(E)** | Assessment/treatment priority order: **M**assive haemorrhage, **A**irway, **R**espiration, **C**irculation, **H**ypothermia/**H**ead injury, (**E**vacuation) | **[NATO/US-common]** |
| **PAWS** | Secondary-assessment extension to MARCH in Tactical Field Care: **P**ain management, **A**ntibiotics, **W**ounds, **S**plinting | **[NATO/US-common]** |

## 5. Clinical / Physiology Terms

Directly underpins AFCM's Lethal Triad Engine (`afcm_physiology`) — see that repo's DESIGN.md §2.1.

| Term | Definition |
|---|---|
| **Lethal Triad** | The self-reinforcing combination of hypothermia, acidosis, and coagulopathy — each worsens the others, driving a casualty toward unrecoverable shock even after external bleeding is controlled |
| **Hypothermia** | Below-normal core body temperature; in trauma, worsened by blood loss and environmental exposure, and itself impairs clotting enzyme function |
| **Acidosis** | Abnormally low blood pH, in trauma typically driven by lactate build-up from tissue hypoperfusion (inadequate blood flow/oxygen delivery) |
| **Coagulopathy** | Impaired blood clotting — in trauma, a *derived* state driven by temperature, pH, and dilution from resuscitation fluids, not an independently-set value |
| **Massive transfusion** | Large-volume administration of blood products to replace major blood loss |
| **Calcium Triangle** | The dynamic where large-volume transfusion (citrate-preserved blood products) drives down the patient's ionised calcium, and hypocalcemia itself causes cardiac contractility failure — a volume-resuscitated patient can still arrest without calcium correction |
| **Citrate toxicity** | The specific mechanism behind the Calcium Triangle — citrate (a blood-product preservative/anticoagulant) chelates (binds) circulating calcium |
| **GCS (Glasgow Coma Scale)** | Standard consciousness scoring: Eye response (1–4) + Verbal response (1–5) + Motor response (1–6); AFCM derives GCS from blood loss, hypoxia, head injury, and drug effects, and uses the verbal component to drive comms suppression (`afcm_neuro`) |
| **Hypovolemic shock** | Shock caused by inadequate circulating blood volume, typically from haemorrhage |
| **Tourniquet (TQ)** | Constricting device applied proximal to a wound to occlude arterial bleeding when direct pressure/packing is insufficient |
| **Airway occlusion / patency** | Obstruction of, or the openness of, the airway — AFCM models this positionally (`afcm_airway`) rather than as a toggle |

## 6. Pharmacology Terms

| Term | Definition |
|---|---|
| **Ketamine** | Dissociative anaesthetic/analgesic; unlike opioids, doesn't significantly suppress respiratory drive at analgesic doses |
| **Penthrox** ("green whistle") | Brand name for inhaled methoxyflurane, a self-administered analgesic device; fast onset, short duration, dose-limited per unit |
| **Fentanyl lozenge** ("lollipop") | Transmucosal (absorbed through oral mucosa) opioid — slower onset than IV administration, carries respiratory depression risk at higher effective dose |
| **Calcium Gluconate** | IV calcium salt used to correct hypocalcemia — the direct treatment for the Calcium Triangle (§5) |
| **Onset / Peak / Duration** | Pharmacokinetic curve shape: time to first effect, time to maximum effect, and how long the effect lasts — the basis of AFCM's per-drug model (`afcm_pharmacology`) rather than a flat "apply = fixed effect" |
| **Route (of administration)** | How a drug enters the body (IV, IM, transmucosal, inhaled, etc.) — affects onset speed and is tracked per dose in AFCM's `active_drugs` state |

## 7. Radio Callings / Voice Procedure

Relevant wherever AFCM's GCS-driven comms suppression (`afcm_neuro`) interacts with TFAR/ACRE2,
and for any future in-mission MEDEVAC-request feature.

| Proword/Term | Meaning | Status |
|---|---|---|
| **Callsign** | A unit/individual's radio identifier, used instead of a name | **[NATO/US-common]** |
| **"This is"** | Precedes the speaker's own callsign when initiating/answering a call | **[NATO/US-common]** |
| **"Send, over"** | Invites the other station to transmit their traffic | **[NATO/US-common]** |
| **"Roger"** | Message received and understood (not necessarily "yes"/agreement) | **[NATO/US-common]** |
| **"Wilco"** | "Will comply" — received, understood, *and* will be acted on | **[NATO/US-common]** |
| **"Say again"** | Request to repeat the last transmission | **[NATO/US-common]** |
| **"Out"** | This exchange is complete, no reply expected | **[NATO/US-common]** |
| **SITREP** | Situation report | **[NATO/US-common]** |
| **9-Liner / MIST call** | The specific structured calls used to request evacuation or hand over a casualty (§1) — the actual content-critical "callings" in a medical context, as opposed to general voice-procedure prowords above | **[NATO/US-common]** |

> None of this dictates TFAR/ACRE2 implementation details (that's AFCM DESIGN.md §5, still open) —
> it's the vocabulary any future in-mission radio-call UI/prompt text should use consistently.

## 8. AFCM Internal System Terminology

Keeps code/docs/UI consistent — see AFCM DESIGN.md for full definitions, this is the quick index.

| Term | Meaning |
|---|---|
| **Lethal Triad Engine** | The `afcm_physiology` subsystem modelling §5's temp/acidosis/coagulopathy feedback loop |
| **PatientState** | The per-casualty simulated data object (temp, pH, lactate, coagulation factor, blood volume, calcium, GCS, airway patency, active drugs, bleeds) |
| **MedicState** | The per-treater data object (stress load, performance modifier) — deliberately separate from `PatientState` |
| **Native path** | Interactions that go through AFCM's own `PatientState` API directly |
| **Compat mode** | The optional `afcm_sim_compat` bridge (AFCM-Simulator) that translates into ACE3/KAT calls for servers not running AFCM |

## 9. AFCM-Simulator Terminology

| Term | Meaning | Status |
|---|---|---|
| **MASCAL** | Mass casualty (incident) — multiple simultaneous patients exceeding routine care capacity | **[NATO/US-common]** |
| **Injury preset** | A named, reusable, pre-authored set of injuries applicable to a spawned patient | **[Gameplay-only]** |
| **Injury level** (Easy/Medium/Hard/Extreme/F*CKED!) | AFCM-Simulator's randomization-difficulty scale — **not** a triage category, see the §2 warning | **[Gameplay-only]** |
| **Random Patient** | One-action spawn of a unit with randomized identity/loadout and randomized injury set | **[Gameplay-only]** |
