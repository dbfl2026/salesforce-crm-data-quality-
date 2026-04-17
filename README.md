# Salesforce CRM Data Quality Case Study

## Context

Brokerpath is a fictional small B2B SaaS selling transaction management software to independent real estate brokerages. The five-person sales team was getting leads from web forms, industry events, referrals, and cold outreach, and the data was inconsistent enough that reps spent time on cleanup and duplicate outreach instead of selling.

## Approach

Prevention over cleanup. Admin controls over custom code. Stop bad data at intake, flag the exceptions that slip through, and give the team reports they can trust. No Apex, no Flow Builder past the basics, no custom objects. Everything sits on standard Lead and Contact objects.

## What I built

1. **Required fields on Lead** - Email, Lead Source, and Company are required by the Lead page. Country can't be made required the same way because Salesforce treats the address as a single grouped field, so I enforced it with a validation rule instead. See [01-required-fields.jpg](screenshots/01-required-fields.jpg).
2. **Standardized picklists** - Lead Source was a free-text field where reps could type anything. I replaced it with a dropdown limited to five options: Web, Event, Referral, Cold Outreach, and Partner. Lead Status is set to New, Working, Qualified, Unqualified, and Nurture. Salesforce also requires a Closed - Converted value for its built-in lead conversion process, so that stays as a system value. See [02a-picklist-standardization.jpg](screenshots/02a-picklist-standardization.jpg) and [02b-picklist-standardization.jpg](screenshots/02b-picklist-standardization.jpg).
3. **Validation rules** - Four rules that block a save when something's wrong. Email has to be in a valid format. Phone number has to be at least 10 characters. A Lead can't be marked Qualified without a Company name. And Country can't be left blank. See [03-validation-rules.jpg](screenshots/03-validation-rules.jpg).
4. **Duplicate management** - Salesforce's built-in matching checks new Leads against existing records using name, email, phone, company, and address. I set the duplicate rule to block the save when a match is found. In a real org with more time, you'd split this into a hard block for exact email matches and a softer warning for fuzzy name matches. One rule was enough here. See [04-duplicate-rule-config.jpg](screenshots/04-duplicate-rule-config.jpg).
5. **Three exception reports** - Leads missing required fields, leads with no activity in 30+ days, and possible duplicates that were flagged but not blocked. See [05-report-missing-fields.jpg](screenshots/05-report-missing-fields.jpg), [06-report-stale-leads.jpg](screenshots/06-report-stale-leads.jpg), [07-report-possible-duplicates.png](screenshots/07-report-possible-duplicates.jpg).
6. **Standard operating procedure** - A one-page guide for how reps should create a new Lead, what to check before saving, and what to do when a duplicate alert comes up. See [SOP-Lead-Entry.md](SOP-Lead-Entry.md).

## Design decisions

**Why these required fields.** Email is how reps contact prospects and how the system checks for duplicates, so it has to be there and it has to be right. Company is the brokerage name. A lead without a brokerage is worthless because Brokerpath sells to the brokerage, not to individual agents. Country matters because real estate software depends on jurisdiction (MLS rules, licensing, and compliance all vary by country), and a Canadian brokerage lead should never end up with a US-only rep. Lead Source is required because if you don't know where a lead came from, you can't tell which channels are working. Country is the only one I couldn't make required from the Lead page directly. Salesforce treats the address as one grouped field and won't let you require individual pieces of it, so I used a validation rule instead.

**Why convert Lead Source to a picklist.** When it's a text field, reps type whatever they want. "tradeshow," "Trade Show," "trade-show," and "Inman 2024" all mean the same thing to a person but show up as four different values in a report. A dropdown with five options covers every channel the team uses and takes the guesswork out of it.

**Why Lead Status has six values, not five.** Salesforce won't let you remove the last value flagged as Converted. If you do, lead conversion breaks entirely. So the five values reps actually use are New, Working, Qualified, Unqualified, and Nurture. Closed - Converted stays as a system value that gets set automatically when a lead is converted. Reps never pick it themselves.

**Why block duplicates on create.** The matching rule checks new Leads against existing records using name, email, phone, company, and address. Blocking on create stops the most common problem: same person filling out a demo form twice, or a rep creating a Lead without searching first. Ideally you'd split this into two rules, a hard block for exact email matches and a softer warning for fuzzy name matches, so reps can see possible matches without getting blocked on legitimate new leads. For a five-person team, one rule that blocks everything is a reasonable starting point.

**Why four validation rules.** Each one stops a specific problem. The email format check stops bounced outreach. The phone minimum length catches junk entries. Requiring a Company name before a Lead can be marked Qualified keeps leads from moving forward without enough information for a rep to actually work them. Requiring Country fills the gap left by the address field limitation described above. More rules than this and reps start finding ways around them.

## Reporting

Three reports catch what the prevention layer misses.

**Leads missing required fields** picks up anything that got in before the rules were turned on, or anything imported in bulk where validation doesn't run. Check it weekly.

**Leads with stale status** finds leads that haven't been touched in 30+ days. They're either ready to be moved to nurture, ready to be disqualified, or sitting on a rep's desk when they shouldn't be. Check it weekly.

**Possible duplicates pending review** pulls up records the matching rule flagged but the duplicate rule didn't block. It gives the team a list to work through instead of letting possible duplicates sit there unnoticed.

## Outcome

In a real deployment: cleaner data coming in, less time spent on manual cleanup, a more reliable handoff from marketing to sales, and reports the team can actually trust because the data behind them is consistent. The required fields and validation rules do most of the work up front. The reports catch what slips through. The SOP keeps new hires from undoing the whole thing in their first week.
