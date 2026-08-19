# Questions for ADF/RAAMC SME Review

This is a standalone list — meant to be handed directly to a serving or ex-serving ADF member
(ideally RAAMC or Combat First Aider-qualified) for review, without needing to read the rest of
the project's docs first.

## What AFCM is

AFCM is an Arma 3 (military simulation game) mod that replaces the usual "have the right item,
click it on the wound" medical gameplay with something closer to real trauma physiology —
hypothermia/acidosis/coagulopathy feeding into each other, real drug names with realistic
timing, a consciousness model that affects whether a casualty can talk on the radio, and so on.
It's built around Australian Defence Force framing specifically. Everything below is a place
where we used public sources (websites, journal articles, manufacturer data sheets — full list in
[REFERENCES.md](REFERENCES.md)) to get close, but a real person with real training is the only way
to know if we've actually got it right or just found something that sounds plausible.

You don't need to answer everything — even a quick "that's roughly right" / "no, actually it's X"
on whichever items you know off the top of your head is genuinely useful. Nothing here needs a
classified or restricted source to answer; if a question can only be answered from something
you're not able to share, just flag that rather than guessing.

---

## 1. Terminology — is this actually how it's said?

| # | What we've got | Where it came from | Question |
|---|---|---|---|
| 1.1 | **CASEVAC** vs **MEDEVAC** are distinguished by whether the platform is a dedicated, Geneva-Protocol-protected medical asset (MEDEVAC) or a multi-role platform pressed into casualty movement (CASEVAC) — not by urgency or speed | A JMVH article on ADF casualty evacuation | Is this the actual working distinction, or is it more blurred/context-dependent in practice? |
| 1.2 | Casualty priority is described as **"priority-one casualty"** rather than a strict "T1/Red" label | Same, describing Role 2 Basic capacity | Is "priority one/two/three" the term actually used day-to-day, or is a colour/T-number system also/instead in active use? |
| 1.3 | **Role 1 / Role 2 (Basic, Enhanced, Forward) / Role 3** as the echelon-of-care structure | Australian Army (The Cove) | Is this the current live terminology, or has anything changed/been renamed since these sources were written? |
| 1.4 | **Combat First Aider (CFA)** — a 12-day course for non-medical-corps soldiers, built on the TCCC handbook, including needle decompression and IV cannulation | The Cove, JMVH | Is that scope/duration/skillset still accurate? Is there a different or updated name for this qualification now? |
| 1.5 | The **9-Liner MEDEVAC request** and **MIST report** (Mechanism/Injuries/Signs/Treatment) formats | Generic NATO/US TCCC sourcing — not ADF-specific | Does the ADF use these exact formats, a modified version, or different terminology entirely for a casualty evacuation request and a handover report? |
| 1.6 | **MARCH** (Massive haemorrhage/Airway/Respiration/Circulation/Hypothermia-Head) and **PAWS** (Pain/Antibiotics/Wounds/Splinting) as the TCCC assessment mnemonics | Generic TCCC sourcing, with ADF Combat First Aider training confirmed to be "built on the TCCC handbook" | Does ADF training use these mnemonics as-is, or a locally adapted version? |

## 2. Equipment

| # | What we've got | Where it came from | Question |
|---|---|---|---|
| 2.1 | **LIFEPAK 15** as the reference monitor/defibrillator | Manufacturer (Stryker) data sheet + it being the standard across Australian *civilian ambulance* services | Is this (or a similar/newer LIFEPAK model) actually what ADF medical elements carry/use, or is it a different manufacturer/model in a field context? |
| 2.2 | Modelling **Manual mode** (operator reads the rhythm) rather than **AED mode** (device tells you shock/no-shock) | Assumption based on targeting trained-medic gameplay | Does that match how a Combat First Aider or RAAMC medic would actually operate the device in the field, or would AED-guided mode be more realistic for a CFA-level user specifically? |

## 3. Pharmacology & scope of practice

| # | What we've got | Where it came from | Question |
|---|---|---|---|
| 3.1 | **Ketamine, Penthrox ("green whistle"), Fentanyl lozenges, Calcium Gluconate** as the modelled drug set | Project brief | Are these actually the drugs carried/administered at the point-of-injury/Combat-First-Aider level, or is some of this restricted to RAAMC-medic or Role 2+ level care? |
| 3.2 | No specific dosing, onset, or duration values set yet — deliberately not invented | — | If you can point us to a public/shareable source for realistic (not necessarily exact-to-the-decimal) dosing and timing for these four agents, or roughly characterise them (e.g. "Penthrox wears off in under half an hour," "Ketamine at analgesic dose won't drop their breathing"), that's exactly the level of detail we need |
| 3.3 | **Calcium Triangle**: modelled as large-volume blood transfusion causing citrate-driven hypocalcemia, which can cause cardiac arrest even after bleeding/volume is fixed, unless Calcium Gluconate is given | General trauma medicine, not ADF-specific | Does this match how it's taught/emphasised in ADF trauma training, or is it treated as a more minor/rare consideration in practice? |
| 3.4 | Whether a **Combat First Aider** would be the one giving these specific drugs, vs. it being medic/Role-2-only | Not confirmed | Who's actually authorised to administer each of the four drugs above — CFA, RAAMC medic, or higher? This affects whether AFCM should gate certain treatments by in-game "qualification" |

## 4. Does the overall emphasis feel right?

Not a factual question so much as a gut-check — from someone who's actually done this training:

- Does making **hypothermia/acidosis/coagulopathy** ("Lethal Triad") the central mechanic reflect what's actually drilled into people, or would a different emphasis (e.g. massive haemorrhage control, or airway) be more true to how it's actually taught?
- Does a casualty's ability to communicate over radio degrading as their consciousness (GCS) drops feel like a reasonable thing to simulate, or does it risk trivialising something that's actually handled very differently in practice?
- Is there anything in the pitch (Lethal Triad Engine / ADF Protocol Accuracy / Dynamic Realism — full text in the project README) that reads as obviously wrong or overstated to someone who's actually served?

## 5. Documents

We found these referenced by name but couldn't get accessible text:
- **ADFP 53** — "Health Support" (Australian Defence Force Publication)
- **ADDP 1.3** — "Mental Health and Psychology Support to Operations"
- **ADDP 3.20** — "The Military Contribution to Humanitarian Operations"

If any of these (or a current equivalent) are something you can access, point us to, or describe
the relevant sections of, that would replace a lot of the guesswork above with an actual source.

---

*Full source list for everything above: [REFERENCES.md](REFERENCES.md). Full glossary with
citations: [TERMINOLOGY.md](TERMINOLOGY.md). Design doc with the "why" behind each system:
[DESIGN.md](DESIGN.md).*
