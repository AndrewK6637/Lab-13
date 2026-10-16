# FIN 43900 — Lab 13: Portfolio Mandate Defense

**Source and scope.** `Pasted markdown(9).md` was not found in the accessible course workspace. This report follows the complete assignment supplied in the task. All monetary amounts are USD; allocations are percentages of the $100 million portfolio. The checker uses only Python's standard library. Executed outputs are in `normal_results.txt`, `probe_results.txt`, and `verification.txt`.

**Independent-work notice.** This is AI-assisted preparation. Sections E–H contain clearly labeled drafts for independent review; they must not be represented as independently written responses where the course requires “no AI” or “AI closed” work.

## A. Statement classification

| Statement | Classification | Reason and distinctions |
|---|---|---|
| **S1:** Seven-year horizon and annual rebalancing. | Hard constraint / policy requirement | The horizon bounds the planning period; annual rebalancing specifies a management schedule. Neither is a return objective. Compliance requires dates and trading history, beyond a single allocation snapshot. |
| **S2:** CPI + 4% annually, net of costs, over rolling five-year periods. | Objective | This is a real-return target with a cost basis and evaluation period. It is an aspiration to evaluate, not a guaranteed outcome or a mechanically enforceable weight constraint. |
| **S3:** Quarterly distributions of $0.75 million and twelve months of reserve coverage. | Hard constraint | Distributions create a spending requirement; twelve months of coverage requires four quarterly payments, or $3 million of eligible reserves. |
| **S4:** Benchmark of 60% global equities and 40% U.S. investment-grade bonds. | Preference / policy reference | The benchmark provides a performance comparator and indicates a balanced investment reference. It does not, by itself, impose exact portfolio weights or a tracking-error limit. Benchmark selection is a policy decision; permissible deviation remains unspecified. |
| **S5:** Long-only; no borrowing, short sales, or derivatives. | Hard constraint | Negative weights are prohibited. With the permitted holdings and a fully invested, unlevered weight model, the checker tests long-only and the 100% total; it cannot audit off-balance-sheet borrowing or fund look-through derivatives. |
| **S6:** No single issuer above 8%. | Hard constraint | Aggregate company exposure must not exceed 8%. For this assignment, cash, U.S. Treasury securities, and diversified funds are explicitly exempt. |
| **S7:** Annual turnover at most 25% without committee approval. | Hard constraint with an approval exception | The limit is conditional on approval. Testing requires trades, a defined turnover convention, and approval records; portfolio weights alone are insufficient. Annual rebalancing under S1 does not waive S7. |
| **S8:** At least 90% redeemable within five business days. | Hard constraint | The eligible holdings' weights must total at least 90%, including holdings redeemable on day five. |
| **S9:** Reserve cannot have a calendar-year loss greater than 10%, with no measurement or breach rule specified. | Unverifiable promise as written; intended risk constraint | It resembles a loss limit, but does not define the reserve account, valuation/return method, cash-flow treatment, monitoring, or breach response. Treating it as a guaranteed outcome would be unjustified. |
| **S10:** No sector, ESG, tax, or home-bias preference; optimization target unspecified. | Preference (explicit absence) and missing decision | The first clause states no preferences in those categories; it does not demand particular exposures. The missing optimization objective prevents choosing a defensible “best” feasible portfolio. |

S9 cannot be treated as an enforceable numerical rule until the CIO clarifies its meaning. A reserve-eligibility label does not establish a security's future loss behavior. The program therefore prints exactly `requires CIO clarification` for every candidate and performs no calendar-year loss calculation. S10 leaves the decision criterion missing even though S2 supplies a return target: a target alone does not determine which risk, shortfall, or benchmark trade-off to optimize.

## B. Investment Policy Statement

An Investment Policy Statement (IPS) is the governing document that translates a client's purpose, spending needs, risk tolerance, and investment restrictions into an agreed framework for managing and evaluating a portfolio. It specifies decision authority, monitoring, and review procedures as well as investment requirements.

Objectives describe what the portfolio is intended to achieve; constraints define the feasible set. A portfolio can satisfy every allocation restriction while being poorly suited to the objective. Conversely, an attractive expected return does not excuse a liquidity or concentration breach.

For Meridian, S2 supplies the return objective; S1 supplies the horizon and rebalancing schedule; S3 supplies spending and reserve needs; S4 supplies the benchmark; S5–S8 govern implementation, concentration, turnover, and liquidity. S9 needs a measurable risk definition and breach process before becoming operational. S10 records absent preferences and an unresolved optimization decision. The IPS should distinguish rules checked from current holdings from rules needing future observations, transaction records, or explicit approvals.

Establishing the IPS before optimization fixes the objective, constraints, and governance against which an allocation will be judged. Otherwise, a mathematically optimal portfolio may solve the wrong problem, and its designer may choose the objective after seeing the result. No expected-return estimates are introduced in this lab.

## C. Portfolio checker results

Required reserve = $0.75 million per quarter × 4 quarters = **$3.00 million**.

Required reserve percentage = ($3.00 million / $100.00 million) × 100 = **3.0%**.

The following values are from the executed checker. All numeric cells are portfolio percentages; the minimum-weight row is the smallest individual holding weight.

| Rule | A | B | C |
|---|---:|---:|---:|
| Weight total = 100% | 100.0 pass | 100.0 pass | 100.0 pass |
| Minimum weight ≥ 0% | 0.0 pass | 0.0 pass | 0.0 pass |
| Reserve ≥ 3.0% | 14.0 pass | 1.0 FAIL | 2.0 FAIL |
| Five-day liquidity ≥ 90% | 100.0 pass | 85.0 FAIL | 100.0 pass |
| Largest nonexempt company ≤ 8% | 0.0 pass | 9.0 FAIL | 8.0 pass |
| Overall | passes every checkable rule | fails 3 rule(s) | fails 1 rule(s) |

Acme bonds and stock are claims on the same issuer. B's 5% bonds plus 4% stock therefore create **9% company exposure**, exceeding the 8% ceiling even though each individual security is below it. C's 8% stock sits exactly at the permitted boundary. Treasury holdings are excluded from this company calculation under the assignment's exemption.

These are five checkable allocation rules, not a certification of the entire IPS. No trading history, realized returns, or committee approvals were supplied. Reserve and liquidity eligibility use the assignment's stated attributes, including the 10-year Treasury note's reserve eligibility and one-day redemption assumption.

## D. Negative-weight test

Candidate A was temporarily changed on disk: cash **2% → −2%** and global equity **60% → 64%**. The offsetting changes preserve total weight while introducing a short position. The checker was executed with those inputs, and its actual output was saved in `probe_results.txt`.

| Measure | Expected | Actual |
|---|---:|---:|
| Weight total | 100.0 pass | 100.0 pass |
| Minimum weight | −2.0 FAIL | −2.0 FAIL |
| Reserve | 10.0 pass | 10.0 pass |
| Five-day liquidity | 100.0 pass | 100.0 pass |
| Largest company | 0.0 pass | 0.0 pass |
| Failure count | 1 | 1 |

The checker correctly identified the long-only violation. It calculates the reserve as −2 + 2 + 10 = 10%; it does not silently clip a negative weight to zero. All other checks pass. The test harness restores the original source in a `finally` block, reruns it, and verifies that its output exactly matches the original normal run. Final A is **[2, 2, 10, 60, 26, 0, 0, 0]**.

## E. Candidate D

| Permitted holding | Allocation |
|---|---:|
| Cash | 0% |
| Treasury bills | 0% |
| Treasury 10-year note | 100% |
| Global equity index fund | 0% |
| Investment-grade bond fund | 0% |
| Private real estate fund | 0% |
| Acme Corp bonds | 0% |
| Acme Corp stock | 0% |
| **Total** | **100%** |

Actual program output:

```text
Candidate D
Weight total: 100.0% pass
Minimum weight: 0.0% pass
Reserve: 100.0% pass
Five-day liquidity: 100.0% pass
Largest company: 0.0% pass
requires CIO clarification
passes every checkable rule
```

The checker accepts D because it is fully invested and long-only, the entire allocation is reserve eligible and redeemable within one business day under the supplied assumptions, and the Treasury is issuer-exempt. This result follows the stated inputs, even though a long-duration security is not equivalent to cash in economic risk.

The CIO could reasonably reject placing all assets in one nominal 10-year Treasury note. It concentrates interest-rate risk, exposes spending-related sales to market-price changes, and lacks the global-equity exposure in S4's balanced benchmark. Its nominal cash flows do not directly track CPI, raising a suitability concern under S2's real-return objective. The seven-year horizon in S1 also ends before the note's initial ten-year maturity. None of these observations proves a future loss or an inability to achieve S2; no return forecast or unstated benchmark-deviation limit is assumed. S9 remains unresolved rather than numerically failed.

**Draft judgment for independent review — two sentences:** I would reject D as a suitable choice for the foundation because putting 100% in a 10-year Treasury note creates substantial interest-rate exposure and does not directly protect the purchasing power needed for S2's CPI + 4% objective. D also looks very different from S4's 60/40 benchmark, but these are suitability concerns, not proven rule violations, since the mandate gives no limit on benchmark deviation and we have not forecast returns.

## F. Question for the CIO

Before I optimize the portfolio, should I prioritize reducing the chance of missing S2's net CPI + 4% target, keeping volatility low while targeting that return, or staying close to S4's 60/40 benchmark?

This answer would fill the gap in S10 and tell me what the portfolio is supposed to optimize. If the CIO prioritizes the return target, I would focus on the probability of falling short over rolling five-year periods. If the priority is low volatility, I would compare overall portfolio risk while accounting for the return target. If the priority is the benchmark, I would focus on tracking error and allocations closer to 60/40. Those choices could produce different portfolios even with the same constraints, and none guarantees the target return.

## G. Reflection


1. **Passing versus suitability:** Passing the checker means the portfolio meets the five rules we can calculate from the inputs. It does not mean the portfolio is a good fit for the foundation. D passes, but putting everything in a 10-year Treasury note leaves the foundation exposed to interest-rate changes and does not directly address its real-return objective. I still need to judge whether the portfolio supports the client's needs.
2. **Most ambiguous classification:** S9 was the hardest to classify. It sounds like a hard constraint, but it does not explain how to measure the reserve's loss or what happens if the loss exceeds 10%. I called it an unverifiable promise as written because I cannot honestly mark it pass or fail without those details. With clarification, it could become a measurable risk limit.

## H. Project 2 mandate

**Client and investment objective:** My proposed client is the fictional Cedar Scholarship Foundation. It has $20 million to invest over a ten-year horizon and pays $150,000 in scholarships each quarter. The goal is to support those payments while preserving purchasing power, with a target of CPI + 3% annually after costs over rolling five-year periods. That return is a goal, not a guarantee.

**Hard constraints:**

- Use only cash, U.S. Treasury securities, diversified global equity funds, and diversified U.S. investment-grade bond funds; weights must total 100%, with no negative weights, borrowing, short selling, or direct derivatives.
- Maintain at least $600,000 in cash or Treasury bills maturing within twelve months, covering four quarterly payments; at inception this equals 3% of portfolio value.
- Keep at least 95% redeemable within five business days.
- Limit direct nonexempt single-company issuer exposure to 5%; cash, U.S. Treasuries, and diversified funds are exempt.
- Rebalance annually; annual one-way turnover, defined as the smaller of purchases and sales divided by average portfolio market value, must not exceed 20% without prior committee approval.


## I. Code understanding

If a candidate holds 6% in Acme bonds and 3% in Acme stock, with no other company securities, what is its largest nonexempt issuer exposure and does it pass the 8% rule?

<details>
<summary>Suggested answer and explanation</summary>

The exposure is **9%**, so it fails. `companies[issuer] = companies.get(issuer, 0) + weight` accumulates weights in a dictionary keyed by issuer, not security name. The first Acme holding starts the total at 6%; the second adds 3% to that same entry. The checker then compares the largest company total with 8%. Exempt issuer categories never enter this dictionary.

</details>

