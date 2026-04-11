# SOP: Lead Entry

## Purpose

Keep new Lead records consistent so reports stay trustworthy and duplicates stay rare.

## Before you create a Lead

Search first. Type the prospect's email and company name into global search. If a matching Lead or Contact already exists, update it instead of creating a new one. Most duplicate problems start with a rep skipping this step.

## Creating the Lead

Fill in every required field. They're required because they break reporting when missing.

- **Email**: the best email you have. If all you've got is a generic info@ address, note it in the Description and try to get a personal one on first contact.
- **Company**: the brokerage name, spelled the way the brokerage spells it on their own website. Not the franchise. Not "Keller Williams." The actual brokerage DBA.
- **Country**: US or Canada. This routes the lead to the right rep and the right compliance track.
- **Lead Source**: pick the closest option from the picklist. Conference lead is Event. Current customer sent them, that's Referral. You cold-emailed them, that's Cold Outreach. When in doubt, ask the marketing lead before inventing a category.

Set Lead Status to **New** unless you've already had a real conversation, in which case **Working**.

## If you get a duplicate alert

Two things can happen when you save.

**Hard block on email.** A Lead or Contact with this exact email already exists. Stop. Open the existing record and update it with whatever new information you have. Don't create a workaround email like "name+new@domain.com" to bypass the rule.

**Soft alert on name.** Salesforce thinks this might be the same person as an existing record but isn't sure. Click through to the suggested matches. Same person? Merge. Different person at the same brokerage? Save the new Lead and add a note explaining the relationship.

## Moving a Lead to Qualified

You can't mark a Lead as Qualified without a Company name. This is intentional. If you have a real prospect but no brokerage name yet, get the brokerage name before changing the status. A Qualified lead without a brokerage can't be worked.

## When in doubt

Ask in the #sales-ops channel before saving something you're not sure about. Five minutes of asking saves an hour of cleanup.
