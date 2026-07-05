---
name: Meal Planner
description: Builds a realistic weekly meal plan — protein anchors first, a batch-cook prep schedule, and a deduplicated grocery list grouped by store section — sized to household, budget, and weeknight time limits. Use when someone asks "plan my meals for the week", "make me a grocery list", "what should we eat this week", or "help me meal prep on Sunday". Do NOT use for setting calorie and macro targets or nutrition strategy — use nutrition-planner instead; this skill turns targets into cookable weeks.
---

# Meal Planner

Plan meals people will actually cook: realistic effort, minimal waste, and a grocery trip that happens once. The costly failure this skill prevents is the aspirational plan — seven distinct ambitious dinners that collapse by Wednesday into takeout, with a fridge of unused specialty ingredients rotting as a reminder. Repetition and batch-cooking are features, not compromises.

This skill owns planning logistics: what to cook, when to prep it, and what to buy. Nutrition strategy — calorie targets, macro splits, protein per kilogram — belongs to nutrition-planner. When the user brings targets from there, honor them per meal; when they bring none, default each main meal to protein + vegetable + starchy carb and do not invent numeric targets.

## Inputs to collect

Ask for what's missing; label assumptions as guesses.

1. People and appetites (adults, kids). Default: 2 adults.
2. Days and meals to cover. Default: 5 weeknight dinners plus lunches from leftovers.
3. Restrictions and allergies. Allergies are absolute — a plan that violates one is a failed plan, not a flawed one.
4. Calorie/macro targets, if any (typically from nutrition-planner). Default: none; balanced plate rule applies.
5. Weeknight cooking time limit. Default: 30 minutes.
6. Budget for the week. Default: none stated; still minimize waste.
7. Disliked foods and equipment constraints (no oven, tiny freezer).

## Operating procedure

Order matters: proteins are chosen first because they are the most expensive line on the list, the longest to cook, and the axis everything else rotates around.

### Step 1: Anchor the week on 2–3 proteins

Pick 2–3 proteins and plan every dinner around them — for example a whole roast chicken, a tray of baked tofu, and ground beef. Two or three proteins cooked in bulk cover five dinners; five different proteins cover five dinners at twice the cost and triple the prep.

### Step 2: Assign each protein 2+ formats

Repetition with variation: the same protein reappears in a different format so it never feels like leftovers. Chicken night one is roast chicken with potatoes; night three it is chicken tacos; the carcass is stock for a soup. Three base formats — bowl (grain + protein + veg + sauce), taco/wrap, and traybake — cover most weeks; rotate sauces, not recipes.

### Step 3: Route every perishable to 2–3 uses

Any fresh ingredient entering the list must appear in at least two meals, three for herbs and delicate greens (a bunch of cilantro goes into tacos, a rice bowl sauce, and the soup garnish). One-use specialty ingredients need explicit justification or they get swapped. This single rule eliminates most household food waste.

### Step 4: Fit the clock

Every weeknight dinner must land within the stated time limit (default 30 minutes) counting from walking into the kitchen, which is only achievable because Step 6 pre-cooks the slow parts. Ambitious cooking is scheduled on the weekend or cut. Build 1–2 leftover lunch loops: cook dinner at +2 portions twice a week and lunch is solved without separate cooking.

### Step 5: Generate the grocery list

Aggregate every ingredient across the week, deduplicate, convert to purchasable quantities (grams of rice → one bag), and group by store section: produce, protein/meat, dairy, pantry, frozen. Check the list against pantry staples the household likely owns and mark those "check first." If a budget was given, sanity-check the protein lines first — protein is where a list blows its budget.

### Step 6: Write the prep schedule

One or two batch-cook sessions of 60–90 minutes each (typically Sunday, optionally Wednesday). In one 90-minute session, run three tracks in parallel: oven (roast the chicken and a tray of vegetables), stovetop (a pot of grains), counter (chop hardy vegetables, mix two sauces, portion snacks). Everything slow happens here so weeknights are assembly. Label containers with contents and day.

## Worked artifact: weekly plan template

```
WEEK OF [FILL] — [FILL: # people] people — weeknight limit [FILL: 30] min
Protein anchors: [FILL: e.g. roast chicken / baked tofu / ground beef]
Targets: [FILL: per-meal kcal/protein from nutrition-planner, or "balanced plate"]

Mon  Roast chicken + potatoes + green beans        (cook 25' | oven-heavy: prep day did the work)
Tue  Tofu grain bowls, peanut sauce                 (assemble 15' | +2 portions → Wed lunch)
Wed  Chicken tacos, cabbage slaw, cilantro          (cook 20')
Thu  Beef & vegetable traybake                      (cook 30' | +2 portions → Fri lunch)
Fri  Chicken-carcass soup w/ leftover grains        (cook 25')

GROCERY LIST
Produce: potatoes 1kg, green beans 400g, cabbage 1/2 head, cilantro 1 bunch (3 uses), limes 3, [FILL]
Protein: whole chicken ~1.8kg, firm tofu 2 blocks, ground beef 500g
Dairy: yogurt 500g (slaw + sauce), [FILL]
Pantry (check first): rice, peanut butter, soy sauce, taco shells, olive oil, stock ingredients
Frozen: [FILL]

PREP — Sunday, 90 min
Oven: roast chicken; tray of root veg           (0:00-1:15)
Stove: big pot of rice; simmer stock base        (0:10-0:50)
Counter: chop cabbage+veg; mix peanut sauce + slaw dressing; portion (0:15-1:00)
```

## Deliverable

Produce a weekly package containing: the day-by-day plan with per-meal cook times inside the stated limit (and macros per meal only when targets were given), the deduplicated grocery list grouped by store section with pantry check-first items marked, and the batch-cook prep schedule with parallel oven/stove/counter tracks.

## Do NOT

- Do not plan seven unrelated recipes for variety's sake — variety comes from formats and sauces, not from seven mise-en-places.
- Do not let any perishable into the list with a single use.
- Do not treat allergies as preferences; one violation invalidates the plan.
- Do not invent calorie or macro numbers when none were given — route target-setting to nutrition-planner.
- Do not schedule a 45-minute recipe on a 30-minute weeknight and call the shortfall "quick if you're efficient."

## Quality bar

- Every weeknight dinner fits the stated time limit, honestly counted.
- 2–3 protein anchors cover the week, each in at least two formats.
- Every perishable appears in 2+ meals; herbs and delicate greens in 3.
- Grocery list is deduplicated, in purchasable units, grouped by section.
- Prep session(s) fit in 60–90 minutes each and front-load everything slow.

## Escalation

Not medical or dietetic advice. Clinical diets (renal, celiac beyond avoidance, diabetes management, eating-disorder recovery) belong with a registered dietitian or physician. Macro and calorie target-setting routes to nutrition-planner; training-driven eating pairs with fitness-program.
