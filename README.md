# Bail Reckoner · जमानत गणक

**Undertrial bail eligibility & decision-support system**
Smart India Hackathon — Ministry of Law & Justice, Department of Justice

Computes statutory bail eligibility under **s.479 BNSS 2023** (half-term / one-third rule),
**s.187 BNSS** (default bail), and the special-statute bars — across 72 offences mapped
IPC ↔ BNS, in English and Hindi.

## Run it

No build step, no dependencies, no server required.

- **Locally:** download both HTML files into the same folder and open `index.html`
- **Hosted:** enable GitHub Pages on the `main` branch, root folder

## Files

| File | Purpose |
|---|---|
| `index.html` | Public landing page |
| `bail-reckoner.html` | The working tool (single self-contained file, ~176 KB) |
| `bail-reckoner/` | Un-bundled source: `data.js`, `engine.js`, `i18n.js`, `roster.js`, `ui3.html` |
| `bail-reckoner/tests/` | jsdom regression tests (navigation, Hindi coverage) |
| `bail-reckoner/PITCH.md` | Problem framing, architecture, impact |

## Rebuild the bundle after editing source

```bash
cd bail-reckoner
python3 -c "
h=open('ui3.html').read()
for t,f in [('/*DATA*/','data.js'),('/*ROSTER*/','roster.js'),('/*ENGINE*/','engine.js'),('/*I18N*/','i18n.js')]:
    h=h.replace(t,open(f).read())
open('../bail-reckoner.html','w').write(h)"
```

## Integration

The engine is framework-free ES5 and returns pure JSON — any system (eCourts, ePrisons, a DLSA portal) can call it headlessly.

```js
const finding = BailReckoner.evaluate({
  chargeIds: ["420","406","IT66D"],
  custodyStart: "2025-06-01",
  chargeSheetFiled: true,
  firstTimeOffender: true
});

const worklist = BailReckoner.screenRoster(prisonRegister); // batch UTRC screening
```

## Design note

This is a **rules engine, not a prediction model**. Training on historical bail orders would
launder caste, class and religious bias into an algorithm. Every output is derived from
codified statute and binding precedent and ships with a reasoning trace. The court retains
full discretion.

## Disclaimer

Decision-support only. Does not render legal advice and does not substitute judicial
discretion under ss. 437–439 CrPC / ss. 480–483 BNSS 2023. The offence database is an
indicative reference set and **must be verified section-by-section by a qualified legal
reviewer** before live deployment. Demonstration rosters are synthetic data.
Statistics cited are from NCRB, *Prison Statistics India 2022*.
