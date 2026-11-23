# Task 2.8 Increment 136 — Quantity precision, units and timestamp handling (G-06/G-07 evidence)

Date: 2026-11-23  
Evidence: E1 — read-only source sampling.

## Observed numerical behavior
- Project quantity validator requires positive whole-number quantity in sampled create/update paths.
- Material specification consumption is numeric and positive; wastage percentage bounded 0–100; basis quantity numeric positive. Calculation formula rounds up to four decimal places after multiplication by consumption and wastage, unless an explicit calculation quantity override is supplied.
- Procurement quantities and prices use JavaScript Number validation, with quantity >0 and price optionally nonnegative. Allocation/receipt validation compares sums and ceilings using floating-point arithmetic. Explicit decimal scale, currency and unit conversion semantics are not established.
- Material specifications and needs carry Unit text; procurement item/order lines carry Unit text. No source-supported universal unit catalog, conversion table or dimensional validation is demonstrated in this sample.
- Information-type number fields use JavaScript Number validation with optional min/max; decimal precision/scale is not defined by this validator.

## Observed timestamp behavior
- Project/event/material/procurement timestamps in sampled services commonly use ISO strings via Date.toISOString; OccurredAt is validated by constructing a Date and checking parseability in the event validator.
- Material need NeededBy and procurement order/expected-delivery/receipt dates use distinct date fields and UI parsing paths; a universal timezone policy for business dates, occurrence instants and user-entered dates is not established.
- Tenant configuration contains a timezone field (default observed elsewhere), but source sampling does not prove all date consumers apply it consistently. Jalali calendar presentation is frontend behavior and is not equivalent to a storage timezone policy.

## V2 implications
1. Use exact decimal types (PostgreSQL numeric) with domain-specific precision/scale, and define rounding/ceiling per calculation and procurement operation.
2. Quantity units should be normalized or constrained through a validated unit catalog; conversion rules, compatibility and display units must be explicitly decided.
3. Separate integer garment production counts from fractional material consumption/receipts, and preserve legacy rounding semantics where business-approved.
4. Define currency and unit-price precision, tax/currency scope, and rounding for surplus cost and report totals.
5. Store instants in UTC and separately model business dates/local timezone where date-only values are intended. Define Jalali input/output and timezone conversion at API/UI boundaries.
6. Validate date ranges and precision at API and database boundaries; avoid floating-point arithmetic for persisted quantities and financial values.
7. Build sanitized tests for rounding boundaries, precision overflow, unit mismatch, timezone/Jalali conversions, daylight/timezone edge cases and receipt aggregate consistency.

## Limits
- Source-only sample; no live values or tests executed. This does not define business policy.
- G-06 and G-07 remain HIGH/open; G-01/G-10 remain blockers; schema readiness NOT APPROVED.
