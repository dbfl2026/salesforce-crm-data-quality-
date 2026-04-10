# Salesforce CRM Data Quality Case Study

## Context

Brokerpath is a fictional small B2B SaaS selling transaction management software to independent real estate brokerages. The five-person sales team was getting leads from web forms, industry events, referrals, and cold outreach, and the data was inconsistent enough that reps spent time on cleanup and duplicate outreach instead of selling.

## Approach

Prevention and Admin controls. Stop bad data at intake, flag exceptions, and give the team reliable reports. Everything lives on standard Lead and Contact objects.

## What I built

1. **Required fields on Lead** - Email, Lead Source, Country, and Company enforced at the page layout level. See `screenshots/01-required-fields.png`.
2. **Standardized picklists** - Lead Source converted from free text to a controlled picklist (Web, Event, Referral, Cold Outreach, Partner). Lead Status normalized to New, Working, Qualified, Unqualified, Nurture. See `screenshots/02-picklist-standardization.png`.
3. **Validation rules** - Email format check, phone number minimum length, and a rule blocking Lead Status from moving to Qualified without a Company name. See `screenshots/03-validation-rules.png`.
4. **Duplicate management** - Standard Duplicate Rule and Matching Rule on Lead. Blocks on exact email match, allows with alert on fuzzy name match. See `screenshots/04-duplicate-rule-config.png`.
5. **Three exception reports** - Leads missing required fields, leads with stale status (no activity in 30+ days), and possible duplicates pending review. See screenshots 05 through 07.
6. **Standard operating procedure** - One-page lead entry SOP at `SOP-Lead-Entry.md`.

## Design decisions

**Why these required fields.** Email is the primary contact channel and the duplicate match key, so it has to be present and clean. Company is the brokerage name. A lead without a brokerage is useless, because Brokerpath sells to the brokerage and not to individual agents. Country matters because real estate software is jurisdiction-dependent, and a Canadian brokerage lead should not land on a US-only rep. Lead Source is required for attribution and channel ROI reporting.

**Why convert Lead Source to a picklist.** Free text guarantees a data nightmare. Five controlled options cover every channel the team actually uses.

**Why block duplicates on email but only alert on name.** Prmary Brokers, and Realtors in general, often use multiple email addresses: personal Gmail, personal business domain, brokerage domain. The same person could potentially fill out a demo form from a different address weeks later. Blocking on name would stop legitimate second inquiries. Blocking on exact email stops the obvious case where the same person submits the same form twice. The fuzzy name alert gives the rep a chance to merge when it really is the same person.

**Why three validation rules and not more.** Each one prevents a specific downstream problem. Email format check prevents or greatly reduces bounced emails from the sales team. Phone minimum length catches bad numbers. Blocking Qualified status without a Company name keeps leads from entering the pipeline without enough information for the reps. More rules than this could invite reps start looking for workarounds.

## Reporting

Three reports show exceptions the prevention step can't catch on its own.

**Leads missing required fields** is run weekly and catches anything that slipped may have slipped through.

**Leads with stale status** is run weekly and flags leads sitting untouched for 30+ days. They're either ready for nurture, ready to be disqualified, or sitting in a reps list when someone shold be taking care of them.

**Possible duplicates pending review** shows the fuzzy-match alerts the duplicate rule generated but didn't block. This gives the team a queue to work with instead of letting them pile up.

## Outcome framing

In a real deployment: cleaner intake, less manual cleanup, more reliable handoff from marketing to sales, and reports the team trusts because the underlying data is consistent. The prevention step does most of the work. The reports catch what slips through. The SOP helps keep new reps from breaking things.
