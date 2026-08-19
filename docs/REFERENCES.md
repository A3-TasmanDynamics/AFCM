# Clinical & Medical References

Source material for grounding AFCM's physiology, pharmacology, and trauma-doctrine claims in real
medicine rather than invented numbers. This directly feeds DESIGN.md §8, item 1 ("Clinical
reference values") — nothing here is a substitute for a qualified SME/RAAMC-affiliated reviewer
sign-off before values go into config, but it's the source set to work from and cite against.

None of these are a license to copy dosing tables verbatim into a game mod without judgement —
AFCM's numbers exist to be *plausible and internally consistent for gameplay*, not to double as
real-world clinical guidance. Treat every value pulled from here as "starting point, needs a pass"
per DESIGN.md §8.

---

## General clinical reference (as supplied)

| Source | Use for |
|---|---|
| [Medscape](https://www.medscape.com/) | Drug monographs, dosing, clinical reference articles — broad general-medicine reference |
| [PubMed](https://pubmed.ncbi.nlm.nih.gov/) | Primary literature — search here when a specific mechanism (e.g. citrate-induced hypocalcemia in massive transfusion) needs a cited source rather than a summary site |
| [Khan Academy — Health & Medicine](https://www.khanacademy.org/science/health-and-medicine) | Conceptual/physiology grounding (acid-base balance, coagulation cascade basics) — good for internal team ramp-up, not for dosing |
| [MSD Manuals — Professional](https://www.msdmanuals.com/professional) | Strong general clinical reference, good disease/mechanism write-ups (hypothermia, coagulopathy, shock physiology) |
| [Dietitians Australia](https://dietitiansaustralia.org.au/) | Peripheral to trauma care — more relevant if AFCM ever models nutrition/metabolic state long-term |
| [TeachMeAnatomy](https://teachmeanatomy.info/) | Anatomy reference for limb/site modelling (§4 of AFCM DESIGN.md), airway anatomy for `afcm_airway` |
| [TGA — Medicine safety & consumer info](https://www.tga.gov.au/resources/consumer-information-and-resources/medicines-safety-and-general-information-consumers/find-information-about-medicine) | Australian regulatory source for approved medicines, indications, and consumer-facing safety info — useful for confirming a drug (e.g. Penthrox) is actually TGA-approved for the modelled use |
| [healthdirect Australia](https://www.healthdirect.gov.au/) | Australian Government consumer health information service — plain-language cross-check for general medical claims |

---

## Australian military / combat medicine specific

| Source | Use for |
|---|---|
| [Royal Australian Army Medical Corps — Australian Army](https://www.army.gov.au/about-us/army-corps/royal-australian-army-medical-corps) | RAAMC role/structure overview — starting point, not a clinical-doctrine document itself |
| [Australian Tactical Medical Association (ATMA) — Tactical Combat Casualty Care](https://atma.net.au/page-1075474) | Australian-context TCCC — closest public source for how US TCCC doctrine (care-under-fire / tactical-field-care / tactical-evacuation phases) is applied in an Australian tactical-medicine context |
| [Journal of Military and Veterans' Health (JMVH)](https://jmvh.org/) | Peer-reviewed Australian military medicine journal — search here for ADF-specific trauma, CBRN, and field-care articles (e.g. its Military Health Support and CBRN Defence pieces) rather than generic TCCC sources |
| [Tactical Combat Casualty Care — overview](https://en.wikipedia.org/wiki/Tactical_Combat_Casualty_Care) | Background/overview only — use as an index into primary TCCC guideline documents, not as a citable source itself |

> No public, freely-accessible ADF-P-Health doctrine publication or RAAMC clinical practice manual
> turned up in search — these are almost certainly internal/restricted-distribution documents.
> If anyone on the team has serving/ex-ADF medic access to the real doctrine, that's a materially
> better source than anything public listed here for the "ADF Protocol Accuracy" pillar.

---

## Australian clinical practice guidelines & prescribing references

These are the strongest sources for anything that needs to look like real Australian clinical
practice rather than generic/US-sourced medicine — most relevant to `afcm_pharmacology` and the
Lethal Triad thresholds in `afcm_physiology`.

| Source | Use for |
|---|---|
| [Therapeutic Guidelines (eTG)](https://www.tg.org.au/) | The core Australian evidence-based prescribing/management reference — analgesia, trauma, and drug-specific guidance. Subscription-gated for full content; [Australian Prescriber](https://australianprescriber.tg.org.au/) (same publisher) is the free companion journal |
| [Australian Prescriber](https://australianprescriber.tg.org.au/) | Free, independent Australian drugs-and-therapeutics journal — good citable source for pharmacology specifics (e.g. opioid dosing, ketamine use) |
| [Australian Resuscitation Council / ANZCOR Guidelines](https://resus.org.au/) | Evidence-based resuscitation and first-aid guidelines for Australia/NZ — relevant guideline numbers found: Guideline 8 (CPR), Guideline 9.1.1 (bleeding management), plus head injury, burns, and spinal injury guidelines |
| [Queensland Health — Primary Clinical Care Manual](https://www.health.qld.gov.au/) | Rural/remote-context Australian clinical manual — useful for austere/limited-resource care patterns, which is closer to a battlefield-care context than a hospital ED reference |
| [NSW ACI — Rural Adult & Paediatric Emergency Clinical Guidelines](https://aci.health.nsw.gov.au/networks/eci/clinical/tools/rural) | NSW rural emergency clinical guidelines — same austere-care relevance as above |
| Royal Flying Doctor Service — Clinical Manual (Parts 1–4: Clinical Guidelines, Drug Infusion Guidelines, Procedures, Standard Drug List) | Not confirmed publicly hosted at a stable URL — RFDS clinical manuals are the single closest real-world analogue to "small team, limited resources, evacuation-distance-constrained care" that AFCM is trying to simulate. Worth chasing down directly (RFDS clinical governance contact) rather than relying on a search-indexed copy |

---

## Medical education (grounding/ramp-up, not primary sourcing)

| Source | Use for |
|---|---|
| [Life in the Fast Lane (LITFL)](https://litfl.com/) | Australian/NZ-run (Perth-based) emergency medicine & critical care education site — FOAMed (free open-access medical education), strong on ECG, toxicology, critical care physiology. Good for team ramp-up on mechanisms (e.g. what coagulopathy actually looks like clinically) before translating into game systems |

---

## How to use this against AFCM DESIGN.md

- **Lethal Triad thresholds** (hypothermia/acidosis/coagulopathy interaction) → MSD Manuals +
  PubMed for mechanism, LITFL for a more digestible clinical-education pass.
- **Pharmacology** (Ketamine, Penthrox, Fentanyl lozenges, Calcium Gluconate / Calcium Triangle) →
  Therapeutic Guidelines / Australian Prescriber first (Australian-context dosing and indications),
  TGA to confirm approved status/use, Medscape/PubMed to fill gaps.
- **ADF-specific doctrine framing** (how TCCC phases map onto Australian tactical medicine
  language) → ATMA and JMVH — closest public approximations; flag anywhere the design doc currently
  assumes something ADF-specific that only a real internal source could confirm.
- **Anatomy/site model** (§4 limb/site mapping) → TeachMeAnatomy.

Anything pulled from a source above and used to set a concrete value in config should get a short
citation comment at the point of use (source + guideline/section, not just a link dump) — makes it
possible to revisit if the guideline updates.
