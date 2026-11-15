# UAT Test Plan and Defect Tracker: Promo Code Feature

User Acceptance Testing (UAT) of a promo-code flow, done the way a Business Analyst would before a release: requirements → test cases → execution → defects → Jira follow-up → release recommendation.

> Independent practice project. Not affiliated with MakeMyTrip or Grab. Tested by hand on a public page, stopped before payment. Findings are observations from my own test run and were not reported to the company.

## Why this project
Grab-style products ship features like "apply a promo code at checkout". I wanted to show the full BA cycle: define what the business wants, test whether it was delivered, and report clearly. Grab only runs in Southeast Asia, so I tested the same kind of feature on a flight-booking Review page available in India.

## Result at a glance

| | |
|---|---|
| Requirements | 9 |
| Test cases run | 7 (4 Pass, 1 Fail, 2 Blocked) |
| Requirements with a test | 6 of 9 (R6, R7, R8 not tested) |
| Defects | 2 (1 Medium, 1 Low) |
| Recommendation | **Not ready** until the Medium defect is confirmed and resolved |

## Key findings
- **D02 (Medium):** with WELCOMEMMT applied (₹2,949 off, total ₹26,543), typing an unavailable code replaced it with TKMFOCUS (₹2,650 off, total ₹26,842), which is ₹299 more. This may be by design, so it is marked for product-owner confirmation.
- **D01 (Low):** a rejected code shows "isn't available" with no reason.
- Discount maths was correct: 29,492 − 2,949 = 26,543.

## What's in this repo
```
├── README.md
├── Promo_Voucher_UAT_Santhosh.xlsx   Requirements, test cases, defect log, summary, traceability
├── docs/
│   └── UAT_Project_Document.md       Full write-up
└── test-evidence/                    Cropped screenshots (test run + Jira)
```

## Method
1. Wrote 9 testable requirements (R1 to R9) with acceptance checks.
2. Designed test cases and mapped each one to a requirement (traceability matrix).
3. Ran the tests on 15 Nov 2026 and recorded actual results with screenshots.
4. Logged defects with steps, expected vs actual, severity and linked tests.
5. Tracked each defect as a Bug ticket in Jira (project "UAT for a Promo Voucher Feature": AFRIQ-3 for D01, AFRIQ-4 for D02).
6. Wrote a summary with a release recommendation.

## Honest limitations
- Two tests are **Blocked** (missing evidence), and three requirements were not tested.
- "Expired code" could not be verified because the site does not say why a code failed.
- One session, one browser, live site that changes over time.

## Tools
Excel / Google Sheets · Jira · Google Chrome

## Author
Santhosh A · [LinkedIn](https://www.linkedin.com/in/santhosh7562152/)
