# Salesforce CRM Data Quality Case Study

## Context

Brokerpath is a fictional small B2B SaaS selling transaction management software to independent real estate brokerages. The five-person sales team was getting leads from web forms, industry events, referrals, and cold outreach, and the data was inconsistent enough that reps spent time on cleanup and duplicate outreach instead of selling.

## Approach

Prevention over cleanup. Admin controls over custom code. Stop bad data at intake, flag the exceptions that slip through, and give the team reports they can trust. No Apex, no Flow Builder past the basics, no custom objects. Everything sits on standard Lead and Contact objects.

## What I built

1. **Required fields on Lead** - Email, Lead Source, Country, and Company enforced at the page layout level. See `screenshots/01-required-fields.png`.
2. **Standardized picklists** - Lead Source converted from free text to a controlled picklist (Web, Event, Referral, Cold Outreach, Partner). Lead Status normalized to New, Working, Qualified, Unqualified, Nurture. See `screenshots/02-picklist-standardization.png`.
3. **Validation rules** - Email format check, phone number minimum length, and a rule blocking Lead Status from moving to Qualified without a Company name. See `screenshots/03-validation-rules.png`.
4. **Duplicate management** - Standard Duplicate Rule and Matching Rule on Lead. Blocks on exact email match, allows with alert on fuzzy name match. See `screenshots/04-duplicate-rule-config.png`.
5. **Three exception reports** - Leads missing required fields, leads with stale status (no activity in 30+ days), and possible duplicates pending review. See screenshots 05 through 07.
6. **Standard operating procedure** - One-page lead entry SOP at `SOP-Lead-Entry.md`.

## Design decisions

**Why these required fields.** Email is the primary contact channel and the duplicate match key, so it has to be present and clean. Company is the brokerage name. A lead without a brokerage is useless, because Brokerpath sells to the brokerage and not to individual agents. Country matters because real estate software is jurisdiction-dependent (MLS rules, licensing, and compliance all vary), and a Canadian brokerage lead should never land in the queue of a US-only rep. Lead Source is required because without it, attribution and channel ROI reporting fall apart.

**Why convert Lead Source to a picklist.** Free text guarantees variants. "tradeshow," "Trade Show," "trade-show," and "Inman 2024" all mean the same thing to a human and four different things to a report. Five controlled options cover every channel the team actually uses without forcing reps to guess.

**Why block duplicates on email but only alert on name.** Brokerage owners often work from multiple email addresses: personal Gmail, brokerage domain, sometimes a franchise domain. The same person can legitimately fill out a demo form from a different address weeks later. Hard-blocking on name would stop legitimate second inquiries. Hard-blocking on exact email stops the obvious case where the same person submits the same form twice. The fuzzy name alert gives the rep a chance to merge when it really is the same person.

**Why three validation rules and not more.** Each one prevents a specific downstream problem. Email format check prevents bounced outreach. Phone minimum length catches garbage entries. Blocking Qualified status without a Company name keeps leads from advancing into the pipeline without enough information for a rep to work them. Any more rules than this and reps start looking for workarounds.

## Reporting

Three reports surface exceptions the prevention layer can't catch on its own.

**Leads missing required fields** catches anything that slipped in before the required-field rules went live, plus anything imported in bulk where validation can be bypassed. Run weekly.

**Leads with stale status** flags leads sitting untouched for 30+ days. They're either ready for nurture, ready to be disqualified, or sitting on a rep's desk when they shouldn't be. Run weekly.

**Possible duplicates pending review** shows the fuzzy-match alerts the duplicate rule generated but didn't block. Gives the team a queue to work instead of letting them pile up.

## Outcome framing

In a real deployment: cleaner intake, fewer manual cleanup cycles, more reliable handoff from marketing to sales, and reports the team trusts because the underlying data is consistent. The prevention layer does most of the work. The reports catch what slips through. The SOP keeps new hires from breaking the standards in their first week.
