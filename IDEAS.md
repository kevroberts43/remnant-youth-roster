# Remnant Roster App — Ideas & Roadmap

A running log for the roster app. Add to this anytime a feature idea comes up, even a half-formed one — nothing here is committed to, it's just a place to keep ideas from getting lost between conversations.

## What's already built

- Roster of all students: name, parent/guardian, phone, email, program, payment method
- Filter chips: All, RYLA, Remnant Kids, 3x3 Basketball, Other — each shows Active/Total counts
- Search by student, parent, or email
- Sort by Name, Parent, or Payment (with reverse toggle)
- Per-student card with tap-to-expand payment details: Paid Through, Invoice Sent, Paid (Yes/No toggles), Notes
- Color-coded payment status bar (green = Paid, red = Invoice sent/unpaid, yellow = Needs invoice)
- Active/Inactive toggle per student (iOS-style switch) — inactive students auto-sort to the bottom and the card grays out
- Overall Active/Total counter, live-updating
- Refresh button to reload latest data
- Custom branding: black/lime/blue/green color scheme matching The Remnant Youth logo, crown logo as the home screen icon
- Installed on iPhone as a home-screen app (via GitHub Pages + Add to Home Screen) — opens full-screen, no browser bar
- Hosted on GitHub Pages (free, no usage caps)
- Edits (Paid Through / Invoice / Paid / Active / Notes) save automatically per-device via localStorage
- **Balance owed badge** on every student card, pulled from a Wix Receipts snapshot (registration + supply fee + tuition + tutoring/1-on-1 training billed, minus payments)
- **Finance view** (toggle at the top, next to Roster) — org-wide totals paid/owed, breakdown by program, families ranked by amount owed, recent payment activity feed

## Data notes

- Finance numbers are a **snapshot**, not live — pulled from Wix Receipts on 2026-08-04. The app is static (GitHub Pages), so there's no live connection to Wix; refreshing the numbers means asking Claude to re-pull and rebuild the FINANCE data block.
- When a Wix invoice bills multiple siblings or mixes tuition with tutoring in one payment, the amount paid is **split proportionally** across the line items (Wix doesn't record which specific charge a payment covered).
- Owed totals include core program fees (Registration/Supply/Tuition) plus tutoring and 1-on-1 training add-ons, per Kevin's direction. A separate, unrelated one-off service (Tamatha Mallard / creative studio) was excluded — it's not a Remnant Youth program.

## Data correction found via Wix enrollment form (2026-08-04)

Pulled the actual "Remnant Youth Program Enrollment Form" submissions to populate Grade Level and T-Shirt Size defaults. This surfaced a correction: **Francisco Cruz and Gabriel Cruz (ids 42/43) are actually named Francisco Cubilla and Gabriel Cubilla**, and are enrolled in **RYLA**, not "Other" as I'd guessed from the invoice data alone. I've updated the roster to reflect this — student names and program badge now read correctly, grade level (9th/8th) and shirt size (Large/Medium) are filled in. Their mom Gabriela Cruz appears to use two email addresses (one for the enrollment form, one for Wix invoicing), which is why this didn't match up automatically.

Also found **3 students on the enrollment form who aren't in the roster yet** (no Wix invoice for them yet, so they didn't show up in the earlier financial pull): Christopher Guzman and Julianna Guzman (parent Carly Justo, RYLA, 7th/6th grade), and Evan Martin (parent Leanne Martin, RYLA, 9th grade). Let me know if you'd like these added.

## New students added from Wix invoices (need your review)

These showed up in Wix invoice data but weren't in the roster yet. Added with best guesses — please confirm program and phone number:
- **Braxtyn Clark & Braden Clark** (parent Katina Clark) — program guess needed, unclear if RYLA or Remnant Kids
- **Francisco Cruz & Gabriel Cruz** (parent Gabriela Cruz) — program guess needed, unclear if RYLA or Remnant Kids
- **Emi Cruz** (parent Gabriela Cruz) — only tutoring charges on file, no core program registration found
- **Isaac Zechmann** (3x3 Basketball) — parent name "Danielle Zechmann" inferred from email, please confirm
- **Sophia Rodriguez & Amelia Rodriguez** (parent Nicole Rodriguez) — only tutoring charges on file ($1,080 combined owed), no core program registration found

## Full roster rebuild from enrollment form (2026-08-05)

Rebuilt the roster from scratch using only real submissions of "The Remnant Youth Leadership Academy Enrollment Application" form, instead of the mix of invoice-derived guesses used before. Result: 44 students, ids renumbered 0-43 alphabetically.

- Merged true duplicate submissions (same kid re-registering) automatically by matching child name + parent email.
- Two special-case merges done by hand per your direction: Ronin Fenton (3 submissions → 1) and **Elijah Rodriguez**, son of both Joel and Liannie Rodriguez — he'd registered under both parents for different basketball offerings but is the same kid, so now appears once, with both parents listed as contacts (using Liannie's phone/email as primary).
  - **Flagging this one for you:** Joel's submission (May 2026) listed Elijah as 3rd Grade, while Liannie's submission (Feb 2026) listed him as 6th Grade — a 3-grade jump in 3 months that's almost certainly a data entry error on one form. I used **6th Grade** (Liannie's) since she's the financial contact, but you may want to confirm the correct grade directly.
- No other genuinely ambiguous duplicates turned up after removing your own test submissions and applying the two merges above.
- Kept the 9 students who don't have an enrollment form on file (they only showed up via Wix invoice history), recategorized per your instructions: Andres, Liam, Marcellus, Isaac → 3x3 Basketball (Play Lab); Braxtyn & Braden → RYLA; Emi → Tutoring; Sophia & Amelia → Remnant Kids.
- Grade Level / T-Shirt Size defaults now come from the actual enrollment form answers wherever a form exists.

## Finance data rebuilt as per-transaction records + month/year selector (2026-08-05)

- Finance numbers used to be one fixed all-time snapshot per student. Rewired to store every individual Wix receipt as its own dated transaction, so billed/paid/owed can be computed for any month on the fly.
- Finance tab now has a month/year dropdown at the top ("Showing financials for..."), defaulting to the current month, with an "All Time" option at the bottom. Every section — the 4 summary pills, all 5 program flip-cards (front **and** back), Families Ranked by Amount Owed, and Recent Payment Activity — filters to whatever period is selected.
- Recent Payment Activity shows a flat dated list when one month is selected, and switches back to the grouped/collapsible by-month view when "All Time" is selected.
- The **Roster tab's balance badge stays all-time** regardless of what's selected on the Finance tab — that badge answers "does this family owe anything at all," which shouldn't change just because you're browsing a past month's financial report.

## Barfield family additions (2026-08-05)

- Aaron Barfield now enrolled in both 3x3 Basketball and RYLA. RYLA invoice sent for $415 ($240 tuition + $100 supply fee + $75 registration fee), currently unpaid — shows on his card as a $415 balance.
- Added his three sisters, same contact info as Aaron (parent Tiffany Barfield): **Sophie Barfield** (RYLA, $415 invoice sent, unpaid) and **Madelyn & Penelope Barfield** (Remnant Kids, $415 invoice sent each, unpaid). None have an enrollment form on file yet.
- Under the hood: transactions can now optionally be tagged with which program they belong to, so a student enrolled in two programs (like Aaron) shows the right billed/owed amount under each program on the Finance tab instead of double-counting. Only Aaron's records and the three new siblings use this tagging so far — everyone else's historical data is untouched and behaves exactly as before.

## Cross-device sync added (2026-08-06)

Edits (Active/Inactive, Notes, Grade Level, T-Shirt Size, 3x3 Play Lab/1-on-1) used to save to each phone's local storage only — that's why changes on your phone weren't showing up on your wife's. Now they sync live through a free Firebase (Firestore) database: any edit on either phone pushes to the cloud and appears on the other device automatically, usually within a second or two. Each phone still keeps a local cache too, so the app still opens instantly and still works if wifi drops for a moment.

This uses the free Firebase "Spark" plan — no cost at your usage level (limit is 50,000 reads / 20,000 writes per day; realistically you'll use a tiny fraction of that).

## New Lovett family signups (2026-08-07)

Pulled the 4 newest enrollment form submissions (since our last full rebuild) — all from Kaceria Lovett, all Remnant Kids. Added incrementally, not a full rebuild, so nothing you'd manually added (the Barfields) was touched.

- **Rashad Lovett Jr** — moved to **RYLA** instead of the "Remnant Kids" he was checked for on the form, since he's listed as 6th grade and Remnant Kids is K-5. Flagging this for you to confirm with the parent — it's possible she just didn't realize the age cutoff.
- **Ra'Shelle Lovett** — Remnant Kids, 4th grade.
- **Rahmarr "Prince" Lovett** — Remnant Kids, 2nd grade (goes by "Prince," per Kevin).
- **Update 2026-08-07:** all 4 Lovett kids are now added, per Kevin — birthdays ignored (unreliable on the forms), grade level used instead. The 4th child really is also named Rahmarr; his roster entry is labeled **"Rahmarr Lovett (Kindergarten)"** just to tell him apart from his 2nd-grade sibling "Rahmarr ('Prince') Lovett" on the roster.
- **Rashad Lovett Jr is a special case:** kept in **Remnant Kids** (not RYLA) despite being 6th grade, past the usual K-5 range — per Kevin, his maturity level fits the Remnant Kids class better.
- None of these 4 have a Wix invoice yet, so they show "Needs invoice" on their cards until you send one through Wix.

## Full Wix refresh + birthday alerts + dual-program fixes (2026-08-13)

**Finance data — fully rebuilt, and a real bug fixed.** Re-pulled all 42 receipts from Wix. Discovered that several invoices get paid in multiple installments, each generating its own receipt row with the *full* invoice line-items repeated — the old finance build was treating each of those rows as a separate bill, which double- or triple-counted "billed" for anyone who paid in installments (Neigha Tooley, Isaac Zechmann, and the Barfield family invoice all hit this). Rebuilt to group receipts by their underlying invoice, so billed is counted once and paid is the sum of installments. Net effect: everyone who's actually paid in full now correctly shows $0 owed. The **only balance left org-wide is Braden & Braxtyn Clark, $207.50 each** ($415 of their $830 family invoice paid).

**Aaron Barfield and Christopher Guzman are now in both RYLA and 3x3 Basketball**, per Kevin — a Wix invoice for Christopher's family also independently confirmed he's now billed for 3x3, matching the request.

**Birthdays added** for 33 students, pulled from their Wix enrollment form answers. The 4 Lovett kids intentionally have no birthday on file — Kevin flagged the form data as unreliable, so nothing was guessed.

**New feature: Upcoming Birthdays banner.** The Roster tab now shows a banner (uses the device's real clock, so it's always current) whenever an active student's birthday is today, tomorrow, or in 2 days — e.g. "🎂 Ra'Shelle's birthday is tomorrow — Celebrate at Remnant Kids (Wednesday)." True push notifications outside the app aren't possible for a static site with no backend, but this banner shows automatically every time the app is opened within that 2-day window, which covers the "so we can celebrate in class" goal.

**Flagged for Kevin — need your read on these:**
- **Elijah Rodriguez's grade**: his birthday (2017-03-01) is now confirmed consistent across both parents' submissions, and it points to 3rd Grade (Joel's answer) rather than the 6th Grade I used previously (from Liannie's submission) — 6th grade would be an unusually big jump for his age. Worth double-checking with the family.
- **Ayden Ortiz vs. Brayden Ortiz**: previously flagged as a possible duplicate/typo. New evidence from Wix invoices shows the family has been billed separately and consistently for "Ayden" and "Bryden/Brayden" across multiple invoices — this now looks more like two real (possibly twin) siblings than a typo, but wasn't explicitly confirmed.
- **New family showing up only in Wix billing, not added to the roster yet**: Tori Schurdell (victoria_kathryn@rocketmail.com), billing for tutoring for kids listed only as "Ronnie," "Riley," and "TJ" — $720 billed, $600 paid, $120 owed. No grade, program, or phone on file, and those look like nicknames, so nothing was added. Let me know their real names/programs and I'll get them on the roster.

## Follow-ups resolved (2026-08-13, later same day)

- **Tori Schurdell's 3 kids added**: Ronnie, Riley, and TJ Schurdell (Tutoring only, no core program) — those are their real names, confirmed by Kevin. Ronnie/Riley owe $50 each, TJ owes $20 (from a partially-paid $720 tutoring invoice). Also refreshed the Recent Payment Activity feed from the full fresh receipt pull while in there (41 entries now, was stale since 2026-08-04).
- **Ayden & Brayden Ortiz confirmed as twins**, per Kevin — no roster change needed, they were already listed as separate active students.
- **Elijah Rodriguez's grade left as 6th Grade** for now, per Kevin — he'll confirm the correct birthday separately.

## Cross-device sync fix + birthday on cards + shirt size audit (2026-08-14)

- **Found why sync wasn't working live**: Firebase blocks connections from domains that aren't explicitly approved, and the GitHub Pages address was never added to that list — so on the real site (not just my testing), the app was silently failing to reach the shared database and falling back to local-only saves on each phone. Fix is a one-time Firebase console setting (Authentication → Settings → Authorized domains → add `7hjd4k8jjz-gif.github.io`), no code change needed.
- **Birthday now shows directly on each student's card** (front, below the balance) for the 33 students who have one on file, formatted like "Mar 22, 2013."
- **Shirt sizes audited against Wix**: all match exactly, no corrections needed. Note that only 10 students have a shirt size on file at all — the shirt-size question was added to the enrollment form partway through the year, so older submissions (and manually-added students) simply never got asked.

## Ideas / backlog (not yet built)

- **Add/edit/delete students in-app** — a form directly in the app instead of messaging changes to Claude each time. Now that edits sync live via Firestore (see 2026-08-06 below), this is more feasible than before — new students added this way could sync to every device too, though it'd still need the roster's core data (contact info, programs) added to the same shared store rather than the static ROSTER array in the HTML.
- **Automatic overdue flags** — compare "Paid Through" against today's date and auto-flag/highlight students who are behind, instead of relying on the manual Paid Yes/No toggle alone.
- **Export/share a filtered list** — a button to export the current view (e.g. "everyone in RYLA who hasn't paid") as text or CSV to share or email.
- **Move roster/finance data itself into Firestore too** — right now only your edits (Active, Notes, Grade, etc.) live in the cloud; the actual student list and Wix financial snapshot are still baked into the HTML file and only update when Claude rebuilds and you re-upload to GitHub. Moving those into Firestore too would mean no more manual re-uploads for data refreshes — a bigger lift, but the sync infrastructure now exists to build on.
- **Monthly scheduled reminder** — e.g. a recurring Claude task that checks for unpaid/overdue students and pings you.
- **Payment amounts per program** — track the actual dollar amount ($240/month mentioned in original enrollment data) alongside status, maybe a running total collected vs. outstanding.
- **Print-friendly view** — a clean, non-app layout for printing a physical roster if ever needed.

## Open questions

- Do we ever need more than one person (e.g. another staff/volunteer) updating payment status? **Answered 2026-08-04:** app is mainly for Kevin + his wife, possibly one future hire to help track students/finances. **2026-08-06:** cross-device sync is now live (see above), so a future hire could use the app from their own phone too.

## Process note

- Building this in the main Cowork chat (not handing off to Claude Code) — the app is small, internal-use only (2-3 people max), and this chat already has the live Wix data connection. Revisit Claude Code if the app ever needs a real backend (e.g. true live refresh, deploy automation) or a bigger rebuild.
