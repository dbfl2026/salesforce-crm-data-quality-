# Salesforce CRM Data Quality Case Study

## Context

Brokerpath is a fictional small B2B SaaS selling transaction management software to independent real estate brokerages. The sales team of 5 was getting leads from web forms, industry events, referrals, and cold outreach, and the lead data was inconsistent enough that reps were wasting time on cleanup and duplicate outreach instead of selling.

## Approach

Prevention over cleanup. Admin controls over custom code. The goal was to stop bad data from entering the system in the first place, flag the exceptions that slip through, and give the team reports they could actually trust. No Apex, no Flow Builder beyond the basics, no custom objects. Everything built on standard Lead and Contact objects using configuration a working admin would reach for first.

## What I built

1. **Required field standards on Lead** - Email, Lead Source, Country, and Company enforced at the page layout level. See `screenshots/01-required-fields.png`.
2. **Standardized picklists** - Lead Source converted from free text to a controlled picklist (Web, Event, Referral, Cold Outreach, Partner). Lead Status normalized to New, Working, Qualified, Unqualified, Nurture. See `screenshots/02-picklist-standardization.png`.
3. **Validation rules** - Email format check, phone number minimum length, and a rule blocking Lead Status from moving to Qualified without a Company name. See `screenshots/03-validation-rules.png`.
4. **Duplicate management** - Standard Duplicate Rule and Matching Rule on Lead. Blocks on exact email match, allows with alert on fuzzy name match. See `screenshots/04-duplicate-rule-config.png`.
5. **Three exception reports** - Leads missing required fields, leads with stale status (no activity in 30+ days), and possible duplicates pending review. See screenshots 05 through 07.
6. **Standard operating procedure** - One-page lead entry SOP at `SOP-Lead-Entry.md`.

## Design decisions

**Why these required fields.** Email is the primary contact channel and the duplicate match key, so it has to be present and clean. Company is the brokerage name, and a lead without a brokerage is useless because Brokerpath sells to the brokerage, not to individual agents. Country matters because real estate software is jurisdiction-dependent (MLS rules, licensing, compliance vary), and a Canadian brokerage lead should never get routed to a US-only rep. Lead Source is required because without it, attribution and channel ROI reporting fall apart.

**Why convert Lead Source to a picklist.** Free text guarantees variants like "tradeshow," "Trade Show," "trade-show," and "Inman 2024" all meaning the same thing, which kills any source attribution report. A controlled picklist with five options covers every channel the team actually uses without forcing reps to guess.

**Why block duplicates on email but only alert on name.** Brokerage owners often have multiple email addresses (personal Gmail, brokerage domain, sometimes a franchise domain), and the same person can legitimately fill out a demo form from a different address weeks later. Hard-blocking on name would stop legitimate second inquiries. Hard-blocking on exact email stops the obvious case where the same person submits the same form twice. The fuzzy name alert gives the rep a chance to merge if it's actually the same person.

**Why these three validation rules and not more.** Each one prevents a specific downstream problem the team had complained about. Email format check prevents bounced outreach. Phone minimum length catches obvious garbage entries. Blocking Qualified status without a Company name prevents leads from advancing into the pipeline without enough information for a rep to actually work them. More rules than this and reps start looking for workarounds.

## Reporting

The three reports are designed to surface exceptions the prevention layer can't catch on its own.

**Leads missing required fields** catches anything that slipped in before the required-field rules were turned on, plus anything imported in bulk where validation can be bypassed. Run weekly.

**Leads with stale status** flags leads sitting untouched for 30+ days. These are either ready for nurture, ready to be disqualified, or signs that a rep is sitting on inventory they shouldn't be. Run weekly.

**Possible duplicates pending review** surfaces the fuzzy-match alerts the duplicate rule generated but didn't block. Gives the team a queue to work through instead of letting potential duplicates pile up.

## Outcome framing

In a real deployment this setup would mean cleaner intake, fewer manual cleanup cycles, more reliable handoff from marketing to sales, and reports the team actually trusts because the underlying data is consistent. The prevention layer does most of the work. The reports catch what slips through. The SOP keeps new hires from breaking the standards on their first week.
