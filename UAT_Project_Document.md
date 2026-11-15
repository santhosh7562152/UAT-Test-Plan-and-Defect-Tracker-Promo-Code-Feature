# UAT Test Plan and Defect Tracker: Promo Code Feature

**Author:** Santhosh A  
**Test date:** 15 November 2026 (about 11:08 PM to 11:18 PM IST)  
**Project type:** Independent learning / portfolio project (User Acceptance Testing)  
**Tools:** Excel / Google Sheets, Jira (free plan), Google Chrome

> **Disclaimer.** This is a practice exercise on a public, user-facing flow. I am not affiliated with MakeMyTrip or Grab. Nothing here was reported to MakeMyTrip. All findings are *observations from my own test run*, not claims about how the company's systems are built. I stopped at the booking review page and never went to payment.

---

## 1. Problem and objective

Companies such as Grab release features like "apply a promo code at checkout". Before release, a Business Analyst checks that the feature behaves the way the business asked. This is User Acceptance Testing (UAT).

**Objective:** write requirements for a promo-code feature, design test cases mapped to those requirements, run them on a live flow, log defects with severity and evidence, track follow-up in Jira, and give a release recommendation.

**Why MakeMyTrip, not Grab?** Grab only operates in Southeast Asia and needs a local account, phone number and payment method. I am in India, so I used a flow I can legally and safely use from here. The skills (requirements, test design, defect tracking, reporting) are the same.

## 2. Scope

| In scope | Out of scope |
|---|---|
| Typing a coupon code on the flight booking Review page | Payment, card details, booking confirmation |
| Discount calculation | Search, seat selection, other add-ons |
| Error messages for rejected codes | Performance, security, load testing |
| One-coupon-per-booking behaviour | Mobile app (only browser tested) |

**Test setup:** 1 adult, domestic flight. Base fare ₹18,216 + taxes and surcharges ₹11,276 = **₹29,492** before any discount. Desktop Chrome, logged-in state not recorded.

## 3. Business goal and users

- **Feature:** Apply promo code at checkout
- **Business goal:** customers get discounts, and the business avoids misuse of codes
- **Users:** Customer, Marketing team

## 4. Requirements

The requirements were written by me for learning. They are not taken from any real MakeMyTrip or Grab document.

| ID | Requirement | Priority |
|---|---|---|
| R1 | A valid code must reduce the order total | High |
| R2 | An invalid code must show a clear error message | High |
| R3 | An expired code must not work | High |
| R4 | Only one code can be used per order | Medium |
| R5 | The discount must be calculated correctly | High |
| R6 | Removing the code must restore the original price | Medium |
| R7 | An empty code box must show a message, not crash | Low |
| R8 | The code must work the same on desktop and mobile view | Low |
| R9 | A code with eligibility rules (new user, minimum amount, bank) must apply only when the rules are met, and the message must say which rule is not met | Medium |

## 5. Test cases and results

Seven tests were run. Test IDs keep their original numbering; test cases I did not run (TC04, TC09 to TC15) were removed instead of being filled with invented results.

| Test | Req | Coupon | Scenario | Result | What I observed |
|---|---|---|---|---|---|
| TC01 | R1 | TKMSALE | Apply eligible sale code | **Pass** | Success message; discount ₹1,200; total ₹29,492 → ₹28,292 |
| TC02 | R9 | WELCOMEMMT | New-user eligibility | **Blocked** | Code was accepted (-₹2,949), but I did not read the offer terms or check if my account counts as "new", so the rule cannot be judged |
| TC03 | R2 | MMTSTANC | Apply a wrong code | **Blocked** | Message said the coupon "isn't available" with no reason. I did not take a *before* screenshot, so "total unchanged" cannot be confirmed |
| TC05 | R3 | NEWUSERSPECIAL | Expired / invalid code response | **Fail** | Message gave no reason; and my applied WELCOMEMMT coupon was replaced by a smaller one (see D02) |
| TC06 | R4 | TKMSALE | Apply first coupon | **Pass** | One discount line only; it replaced an earlier coupon |
| TC07 | R4 | WELCOMEMMT | Apply a second coupon | **Pass** | Applied over TKMSALE; only one discount stayed, no double discount |
| TC08 | R5 | WELCOMEMMT | Verify discount calculation | **Pass** | 29,492 − 2,949 = 26,543, matches the screen (2,949 is 10% of 29,492, rounded) |

**Not tested (known gap):** R6 (remove code), R7 (empty box), R8 (mobile view).

## 6. Requirement traceability

| Req | Linked tests | Covered? |
|---|---|---|
| R1 | TC01 | Yes (Pass) |
| R2 | TC03 | Yes (Blocked) |
| R3 | TC05 | Yes (Fail) |
| R4 | TC06, TC07 | Yes (Pass) |
| R5 | TC08 | Yes (Pass) |
| R6 | none | **No** |
| R7 | none | **No** |
| R8 | none | **No** |
| R9 | TC02 | Yes (Blocked) |

6 of 9 requirements have at least one test. Only 4 of them (R1, R3, R4, R5) have a test that actually ran to a Pass/Fail result.

## 7. Defects

| ID | Linked tests | Title | Severity | Status |
|---|---|---|---|---|
| D01 (AFRIQ-3) | TC03, TC05 | Rejected coupon message gives no reason | Low | Open |
| D02 (AFRIQ-4) | TC05 | Invalid code replaces my applied coupon with a smaller discount | Medium | Open (needs confirmation) |

### D01 (Jira AFRIQ-3): Rejected coupon message gives no reason
- **Steps:** open the flight Review page → type MMTSTANC (or NEWUSERSPECIAL) in "Enter coupon code" → Apply.
- **Expected:** the message says why the code failed (invalid, expired, or not eligible).
- **Actual:** the popup says only that the coupon "isn't available".
- **Evidence:** `evidence_04_NEWUSERSPECIAL_not_available.png`

### D02 (Jira AFRIQ-4): Invalid code replaces my applied coupon with a smaller discount
- **Steps:** apply WELCOMEMMT (total ₹26,543) → type NEWUSERSPECIAL → Apply.
- **Expected:** an error message, and the ₹2,949 discount stays (total ₹26,543).
- **Actual:** the popup says the best available coupon was applied instead. The discount became TKMFOCUS, ₹2,650, and the total rose to ₹26,842, which is ₹299 more.
- **Evidence:** `evidence_03_WELCOMEMMT_applied_popup.png`, `evidence_04_NEWUSERSPECIAL_not_available.png`, `evidence_05_TKMFOCUS_replaced_coupon.png`
- **Open question:** this may be *by design* (the site tries to give the customer a coupon). In a real job I would ask the product owner. If it is by design, the requirement set has a gap: nothing says what should happen to an already-applied coupon when a bad code is typed.
- **Retest plan:** on a fresh Review page, apply WELCOMEMMT and screenshot the total, type a bad code and screenshot again. Not yet done.

**Defect status flow used:** Open → Reported → Fixed → Retested → Closed. Neither defect was reported to MakeMyTrip, so none moved past Open in the real world.

## 8. Follow-up tracking in Jira

I created a Jira project, **"UAT for a Promo Voucher Feature"** (key AFRIQ), and logged each defect as a ticket of work type **Bug**.

| Ticket | Defect | Title | Story points | Jira status | Label |
|---|---|---|---|---|---|
| AFRIQ-3 | D01 | Rejected coupon message gives no reason | 3 | In Progress | promo-code |
| AFRIQ-4 | D02 | Invalid code replaces my applied coupon with a smaller discount | 4 | To Do | promo-code |

Both tickets currently sit in the project backlog (7 story points in total). The Jira status reflects my own workflow only: neither defect has been reported to MakeMyTrip or fixed, so the defect status in Section 7 remains Open. See `test-evidence/jira_01_bugs_in_backlog.png` and `jira_02_create_bug_AFRIQ-3.png`.

## 9. Summary

| Item | Count |
|---|---|
| Requirements | 9 |
| Test cases run | 7 |
| Passed | 4 |
| Failed | 1 |
| Blocked | 2 |
| Pass rate (of the 5 tests that reached a result) | 80% |
| Defects: High / Medium / Low | 0 / 1 / 1 |
| Requirements with a test | 6 of 9 |

## 10. Recommendation

**Not ready for release (practice assessment).**

The core behaviour works: a valid code reduces the total and the discount maths is correct to the rupee. But D02 (an invalid code replacing an applied coupon with a smaller discount) needs to be confirmed and a decision made by the product owner. D01 should also be fixed so customers know why a code failed. Before sign-off, finish TC02 and TC03, retest D02, and run tests for R6, R7 and R8.

## 11. Limitations (stated honestly)

- Live commercial site: offers change, so results may not repeat.
- Only one session on one browser; I did not record whether I was logged in.
- TC02 and TC03 are Blocked because of missing evidence, not because the site failed.
- "Expired" could not be tested: the site's message does not say whether a code is expired or simply invalid.
- Requirements are my own, not from the company.

## 12. Skills shown

Requirement writing · test case design (positive, negative, edge) · requirement traceability · executing UAT and recording evidence · defect logging with severity · Jira bug tickets and backlog · reporting with a release recommendation · honest handling of blocked tests and unverified findings.

## Appendix: evidence files

| File | What it shows |
|---|---|
| evidence_01_MMTSECUREINT_adds_insurance.png | A coupon that also adds travel insurance automatically (not paid for, tab closed) |
| evidence_02_TKMSALE_applied_popup.png | TKMSALE applied, ₹1,200 saved (TC01, TC06) |
| evidence_03_WELCOMEMMT_applied_popup.png | WELCOMEMMT applied, ₹2,949 saved (TC02, TC07, TC08) |
| evidence_04_NEWUSERSPECIAL_not_available.png | "isn't available" with no reason (D01, D02) |
| evidence_05_TKMFOCUS_replaced_coupon.png | TKMFOCUS ₹2,650, total ₹26,842 (D02) |
| evidence_06_TKMSALE_fare_summary.png | Fare summary with TKMSALE, total ₹28,292 (TC01, TC08) |
| evidence_07_WELCOMEMMT_fare_summary.png | Fare summary with WELCOMEMMT, total ₹26,543 (TC07, TC08) |
| jira_01_bugs_in_backlog.png | Jira backlog showing AFRIQ-3 and AFRIQ-4 as Bugs with story points |
| jira_02_create_bug_AFRIQ-3.png | Creating the Bug work type in the project |

*The workbook also references a screenshot taken at 11:08 PM for TC03 (`Screenshot_2026-11-15_230835`). It was not in the files I had, so add it to `test-evidence/` if you still have it.*
