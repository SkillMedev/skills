---
name: review-engine
description: Builds a systematic review generation and response engine for a local service business - the ask-at-peak-satisfaction timing rule, SMS ask scripts, a review-velocity target benchmarked to the top competitor, and word-for-word response procedures for 1-3 star reviews. Use when an owner asks "how do I get more Google reviews", "a customer left a bad review, what do I say", "should I text customers for reviews", or "why does my competitor have way more reviews". Do NOT use for the rest of the Google Business Profile setup - use google-business-profile-optimizer instead. Do NOT use for turning existing reviews into website FAQ content - use review-to-faq-builder instead.
---

# Review Engine

Reviews are the prominence engine of local ranking and the trust engine of
conversion: they feed the map pack (see local-seo-playbook) and they close the
customer who is comparing three plumbers at 9 pm. The costly mistake this
skill prevents is leaving reviews to chance - happy customers stay silent by
default, angry ones self-select, so an unmanaged profile drifts toward a
rating the business does not deserve. Run this after
google-business-profile-optimizer has the profile itself in order.

Worked business throughout: Summit Plumbing, 4 techs, $450 average ticket,
about 167 jobs a month at $75,000 monthly revenue.

## Operating procedure

### Step 1: Set the velocity target against the top competitor

Count the top map-pack competitor's total reviews and how many they gained in
the last 90 days (check their newest review dates). Target: match or exceed
their monthly velocity. Summit's top competitor adds about 10 reviews a
month; Summit gets 3. Target: 12 a month. At 167 jobs a month,
12 reviews is a 7 percent review rate - very achievable, since a well-timed
SMS ask converts around 10-20 percent of happy customers.

### Step 2: Install the ask-at-peak-satisfaction rule

Ask at the moment satisfaction peaks: the tech has just fixed the problem,
shown the customer the result, and the relief is fresh. For trades this is
on-site at job completion or within the hour; for clinics and salons, at
checkout or same evening. Every hour after peak, response rates fall. An ask
three days later via email is why the old system produced 3 a month.

### Step 3: Send the ask by SMS, primed by the tech

Text beats email roughly 5x for local review asks - it is opened in minutes
and the review is one tap away. The tech primes it verbally at handoff, then
the office (or automation) sends the SMS within the hour:

```
Tech, at handoff:
"Glad we could get that sorted. We're a small local shop, so Google
reviews genuinely decide whether people find us. You'll get a text from
us with a link - if you have 60 seconds, it'd mean a lot."

SMS, within 1 hour of job completion:
"Hi [FILL: first name], it's [FILL: tech name] from Summit Plumbing.
Thanks for having us out today! If you were happy with the work, would
you leave us a quick Google review? It takes about a minute and helps
our small team a lot: [FILL: review link]"

If no review after 3 days, one follow-up only:
"Hi [FILL: first name] - [FILL: tech name] from Summit Plumbing again.
No pressure at all, just a friendly nudge in case the review link got
buried: [FILL: review link]. Thanks either way!"
```

Use the direct review link from the profile (the short share URL). One nudge
maximum; a third message is pestering and earns the opposite of a five-star.

### Step 4: Respond to every review, positive ones included

Respond to five-star reviews within a week, two to three sentences, naming
the service and city where natural ("Thanks, Maria - glad the water heater
swap in Oak Hill went smoothly"). This is conversion copy for the next
prospect reading the profile and reinforces relevance.

### Step 5: Run the 1-3 star response procedure

The audience for a bad-review response is not the reviewer - it is the
hundred prospects who will read the exchange later. The procedure:

1. Respond within 24 hours. Speed signals a business that cares; a week of
   silence reads as guilt.
2. Take it offline. Apologize for the experience (not necessarily fault),
   give a direct name and phone number, invite the conversation off the
   public page.
3. Never argue, never share job details publicly, never blame the customer -
   even when the review is unfair. Health businesses: never confirm the
   person was a patient (privacy law).
4. If resolved, ask once, politely, whether they would consider updating the
   review. Never make a refund conditional on editing it.

Word-for-word script:

```
"Hi [FILL: name], I'm [FILL: owner], the owner of Summit Plumbing. I'm
sorry your experience didn't meet the standard we aim for - that's on
us to fix. I'd like to make it right. Please call me directly at
[FILL: phone] or email [FILL: email] and I'll take care of it
personally."
```

Good/bad contrast on a real one-star ("Tech showed up 2 hours late and
tracked mud through my hallway"):

```
BAD:  "We were late because of an emergency call, which we texted you
      about. Also our techs always wear boot covers, so the mud claim
      doesn't add up."
GOOD: "Hi Dan, I'm Alex, the owner. A 2-hour delay and a mess left
      behind is not how we operate, and I apologize. I'd like to make
      it right - please call me at (555) 014-2200 and I'll sort this
      out personally, including having the hallway cleaned."
```

The bad reply wins the argument and loses every reader. The good reply loses
the argument and wins the next twenty customers.

### Step 6: Track velocity monthly and feed the pack-mates

Log reviews gained per month against the target on one line of the KPI sheet.
Review velocity is a ranking input local-seo-playbook depends on - if
velocity is at target for 90 days and rankings still lag, the problem is
relevance or citations, not prominence. And every five-star moment this
engine detects is the referral trigger: hand those customers to
local-referral-engine for the referral ask.

## Inputs to elicit

Collect: current review count and rating, top competitor's count and 90-day
velocity, jobs per month, how job completion is recorded (this triggers the
SMS), who sends the ask (tech, office, or software), and the direct review
link. Defaults: ask 100 percent of completed jobs, SMS within 1 hour, one
nudge at day 3. If the competitor's velocity is estimated from review dates,
label it an estimate.

## Concrete thresholds

- Velocity target: at or above the top competitor's monthly rate; Summit's is
  12 a month.
- Ask timing: within 1 hour of completion; response rate decays after peak.
- Expected conversion: 10-20 percent of asked customers; below 5 percent
  means the ask is mistimed or the link is broken.
- Negative reviews: respond within 24 hours, always.
- Rating floor: a profile below 4.0 loses the comparison by default - pause
  paid spend into a sub-4.0 profile and fix service delivery first (check the
  economics with local-unit-economics).

## Deliverable

A one-page review operating system: the velocity target with the competitor
benchmark, the tech handoff line, the SMS ask and nudge templates with fields
filled for the business, the 24-hour negative-review procedure with the
owner's script, and a monthly velocity log line.

## Do NOT

- Do not buy or fabricate reviews, or review-gate (asking only pre-screened
  happy customers via a filter survey) - both violate policy and platforms
  remove the reviews, sometimes the profile.
- Do not offer discounts or payment for reviews; incentives for reviews are
  prohibited, and one screenshot can undo years of reputation. Incentivize
  referrals instead - that is local-referral-engine's lane.
- Do not argue publicly with a reviewer, even a lying one. The reply is for
  the readers.
- Do not send more than one nudge; pestering converts five-star intent into
  silence or worse.
- Do not batch asks weekly - the peak-satisfaction window is measured in
  hours, not days.

## Quality bar

- Every completed job triggers exactly one ask and at most one nudge, with a
  named owner or automation for the send.
- The velocity target cites the actual competitor number, not a wish.
- Negative-review scripts contain a real name and direct contact, not "the
  management team."
- The monthly log distinguishes velocity (new reviews) from totals, because
  velocity is what ranking rewards.

## Escalation and routing

Defamatory or fake reviews: flag through the profile, and consult a lawyer
before responding to anything with legal exposure - this skill is not legal
advice. Clinics and health practices must keep every response free of any
patient-relationship confirmation. Route profile setup to
google-business-profile-optimizer, ranking strategy to local-seo-playbook,
the post-five-star referral ask to local-referral-engine, and whether review
volume justifies its labor cost to local-unit-economics.
