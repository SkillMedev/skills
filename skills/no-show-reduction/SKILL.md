---
name: no-show-reduction
description: Cuts appointment no-shows for a local service business with a three-step reminder ladder (booking confirmation, day-before SMS, morning-of SMS with the tech's name and photo), confirmation-required policies, deposit rules for high-value appointments, and a reschedule-not-cancel script. Use when an owner says "customers keep not being home when the tech shows up", "how do I reduce no-shows", "should I take deposits for appointments", or "people cancel same-day and it kills my schedule". Do NOT use for chasing quotes after the visit happened - use quote-follow-up-sequences instead. Do NOT use for winning back lapsed customers - use win-back-campaign instead.
---

# No-Show Reduction

A no-show is the most expensive kind of nothing: the lead was paid for, the
slot was reserved, the tech drove out or sat idle, and no revenue arrived.
The costly mistake this skill prevents is treating no-shows as weather -
random and unmanageable - when a reminder ladder and a confirmation policy
reliably cut them by half or more. This skill and quote-follow-up-sequences
are the pack's two revenue-protection systems: both defend money the lead
spend already paid for.

Worked business throughout: Summit Plumbing, 4 techs, $450 average ticket,
$75,000 a month, booking roughly 300 appointments monthly. At its current 14
percent no-show rate that is about 42 missed visits; at the 65 percent close
rate that is roughly 14 lost jobs and $6,100 a month in gross revenue - the
same figures local-unit-economics uses when it flags show rate as Summit's
constrained lever.

## Operating procedure

### Step 1: Measure the real no-show rate

Count last month's booked appointments and how many the customer missed
(not home, not answering, same-hour cancellation). The red line: a no-show
rate above 10 percent is a systemic problem - process, not bad luck - and
this whole skill applies. Under 5 percent is healthy; run only the ladder's
first two rungs and move on. Summit measures 14 percent: systemic.

### Step 2: Install the reminder ladder

Three rungs, each with a distinct job. SMS for rungs two and three - texts
get read within minutes, email does not.

```
RUNG 1 - Booking confirmation (immediately at booking, SMS + email):
"You're booked! Summit Plumbing will be out [FILL: day, date],
arrival window [FILL: window], for [FILL: job]. Reply C to confirm,
R to reschedule. Questions? Call [FILL: office number]."

RUNG 2 - Day-before SMS (previous day, ~4 pm):
"Hi [FILL: name], reminder: Summit Plumbing is scheduled tomorrow,
[FILL: window], for [FILL: job]. Reply C to confirm or R to
reschedule - please have someone 18+ home and the area accessible."

RUNG 3 - Morning-of SMS with the tech's name and photo (2 hours ahead):
"Good morning [FILL: name]! [FILL: tech name] from Summit Plumbing
is headed your way, arriving [FILL: window]. Here's [FILL: tech
first name] so you know who's at the door: [FILL: photo link/MMS].
Running into a problem? Reply here or call [FILL: office]."
```

The tech name and photo on rung 3 does double duty: it humanizes the
appointment (people no-show a company, rarely a person named Marcus whose
face they have seen) and it is a safety courtesy that customers rate highly.

### Step 3: Decide confirmation-required vs assumed, by slot value

Two policies, applied by appointment type:

- Confirmation-assumed (default for standard repair calls): the appointment
  stands unless the customer cancels. Low friction, fits abundant slot
  supply.
- Confirmation-required (for high-value or long slots): if the customer has
  not replied C by rung 2, the office calls; no confirmation by 6 pm means
  the slot is released to the waitlist and the customer is told so in the
  rung-2 message. Use when a no-show costs half a day, not half an hour.

Summit runs assumed for standard calls, required for half-day jobs like
water heater replacements and sewer line work.

### Step 4: Take deposits on high-value appointments

Deposit rule: any appointment reserving 4+ tech-hours or requiring ordered
materials takes a deposit - 10 to 20 percent of the quoted job or a flat
$100, whichever is greater, credited to the invoice. Frame it as reserving
materials and the crew, not as distrust: "We order your unit and block the
crew's day, so we take a $150 deposit that comes straight off your bill."
Deposit-backed appointments no-show at a fraction of the open-slot rate.
Do not take deposits on standard diagnostic calls - the friction costs more
bookings than the no-shows cost.

### Step 5: Answer every cancellation with the reschedule-not-cancel script

Never end a cancellation call with the calendar empty. The script:

```
Customer: "I need to cancel Thursday."
Office:  "No problem at all, [FILL: name] - let's just move it so you
         don't lose your place. I've got [FILL: day] at [FILL: time]
         or [FILL: day] at [FILL: time] - which is easier?"
If they resist a date: "Totally fine - I'll pencil you for [FILL: day
+7] and text you the day before to confirm. If that doesn't work,
we'll shuffle it then. Sound good?"
```

A cancellation converted to a reschedule keeps the customer, the CAC already
spent, and the job. A pure cancellation usually means they call a competitor
the next time the pipe drips.

### Step 6: Handle the no-show that still happens

Same day, one friendly SMS - assume good faith: "Hi [FILL: name], we came by
at [FILL: time] but couldn't reach you. No worries - want me to find you a
new slot this week?" Track repeat offenders; two no-shows moves that
customer to confirmation-required permanently, three adds a deposit
requirement. Keep a same-day waitlist of flexible customers to backfill
released slots so a no-show costs a phone call, not a tech-hour.

### Step 7: Re-measure monthly and report the show rate

Track no-show rate on the monthly sheet and feed the show rate into
local-unit-economics. For Summit, cutting 14 percent to 7 percent recovers
roughly $6,100 a month gross and drops CAC from about $119 to about $110 -
the cheapest customer-acquisition improvement available, because it buys
zero new leads.

## Inputs to elicit

Collect: booked appointments and no-shows last month (count them - owners
guess low; label a guess as a guess), booking channel (phone, web, both),
whether SMS with photos is available, average slot length by job type, and
current deposit practice. Defaults: ladder at booking / day-before 4 pm /
2-hours-ahead; deposits only on 4+ hour jobs; assumed confirmation for
standard calls.

## Concrete thresholds

- Red line: no-show rate above 10 percent is systemic; under 5 percent is
  healthy.
- Ladder timing: confirmation at booking, reminder day before around 4 pm,
  morning-of 2 hours ahead. Three touches - a fourth is nagging.
- Deposits: 4+ tech-hours or ordered materials; 10-20 percent or $100
  minimum, credited to the invoice.
- Confirmation-required cutoff: unconfirmed by 6 pm day-before releases the
  slot.
- Expected result: a full ladder plus policies typically cuts no-shows by
  half within 60 days; if not, the booking process is overbooking or the
  arrival windows are unrealistic.

## Worked artifact: Summit's no-show economics

```
                          BEFORE          AFTER (60 days)
Booked appointments/mo:   300             300
No-show rate:             14%             7%
Missed visits:            42              21
Lost jobs (65% close):    ~27             ~14
Lost gross revenue:       ~$12,300/mo     ~$6,100/mo
Recovered:                                ~$6,100/mo (~$73k/yr)
Changes made: 3-rung ladder w/ tech photo, deposits on 4+ hr jobs,
reschedule-not-cancel script at the front desk, same-day waitlist.
```

## Deliverable

A one-page no-show system: the three ladder messages with fields filled, the
confirmation policy per appointment type, the deposit rule with its customer
framing sentence, the reschedule-not-cancel script at the booking desk, and
a monthly no-show log line that feeds the show rate into
local-unit-economics.

## Do NOT

- Do not add reminder rungs beyond three; past that, reminders train
  customers to ignore them.
- Do not take deposits on low-value diagnostic calls - the booking friction
  costs more than the no-shows.
- Do not scold no-show customers on the first offense; assume good faith
  once, tighten policy on repeats.
- Do not accept a bare cancellation when a reschedule was never offered -
  that is the script's whole job.
- Do not quietly overbook to compensate for no-shows; the double-booked day
  when everyone shows costs reviews (see review-engine's 1-star procedure)
  and burns techs.

## Quality bar

- The no-show rate is measured from real counts, before and 60 days after.
- Every ladder message states the job, the window, and a one-tap reply
  action.
- The deposit policy names its threshold and its invoice-credit framing.
- Repeat offenders have an escalation path written down, not improvised.

## Escalation and routing

If no-shows concentrate in one lead source, the problem is lead quality -
take it to the channel math in local-unit-economics before blaming the
ladder. Quotes that go silent after a completed visit belong to
quote-follow-up-sequences. Lapsed past customers belong to
win-back-campaign. Completed visits that end happy are review-engine's
peak-satisfaction moment - make sure the ask fires.
