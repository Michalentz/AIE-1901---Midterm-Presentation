# Nutriqo v2 — University Canteen Meal Optimizer

A minimalist, responsive, offline-capable research prototype built with plain HTML, CSS, and JavaScript. No backend, account, installations, or external libraries are required.

## Open it

**Easiest:** Open `Nutriqo_v2_Standalone.html` (distributed separately from this ZIP) in Chrome or Edge. It includes the photographs, stylesheet, and JavaScript in one file.

**Editable source:** Extract the ZIP, then open `Nutriqo_v2/index.html` in a browser. Keep `index.html`, `style.css`, `app.js`, and the `assets` folder together. To run it as a local dev server, `cd Nutriqo_v2 && python -m http.server 8000`.

## Four pages

- **Dashboard** — a clear overview of the canteen, meal package choices, and quick-start presets.
- **Meal Builder** — make favorite dishes **Must include**, **Prefer**, or **Avoid**. Must-have portions can be repeated by default, or **Unique dishes only** can be enabled to restrict every recommended meal to one portion of each distinct dish. The limit applies within each meal, not across the five recommendations. Adjust the package, maximum price, rice, calorie target, hunger, next meal timing, and optimization weights. An expandable panel provides optional adult energy targets using age, sex coefficient, height, weight, activity and goal.
- **My Results** — five recommendation categories (best overall, budget, nutrition, filling, calorie match), a 100%-sum pie chart of user-defined objective weights, independent horizontal performance bars, calorie uncertainty ranges, export to CSV, and a 100-scenario stability check.
- **Food Library** — actual canteen food photos, estimated nutritional profiles, recipe assumptions, and methods/reading links. The item detail pop-up links back to the builder.

Preferences are saved in the device's browser local storage only. There is no account, analytics, or remote server.

## Canteen constraints

| Package | Meat portions | Vegetable portions | Price |
|---|---:|---:|---:|
| A | 1 | 2 | ¥16 |
| B | 2 | 1 | ¥20 |
| C | 3 | 1 | ¥25 |

Meat dish nominal size: 150 g. Vegetable or tofu dish nominal size: 200 g. Rice is included with a 200 g nominal starting serving and free refills; the optimizer considers adjustable portions from 0–600 g for computational convenience, not a canteen cap. Nominal portion weights are modeled with an assumed ±8% variation.

Ten dishes are represented (7 meats and 3 vegetables, where tofu belongs to the vegetable category under the canteen's meal rules). Repeated portions are supported by default. Turning on **Unique dishes only** filters out every candidate with a repeated dish. The engine exhaustively enumerates 42 + 84 + 252 = **378 combinations** with repeats or 21 + 63 + 105 = **189 combinations** without repeats, before rice variation. Existing multi-portion must-have selections are automatically reduced to one when the setting is enabled, with an on-screen explanation. It then filters for package rules, price, required portions, and excluded foods.

## Optimization design

For each candidate, estimate calories, protein, carbohydrates, fat, fiber, sodium, saturated fat, sugar, and edible mass. Four model-based 0–100 performance scores are calculated:

1. Cost and value — affordability, protein/yuan, fiber/yuan.
2. Nutrition — proxy based on protein, fiber, vegetables, sodium and saturated fat.
3. Calorie fit — distance from selected calorie target (with tolerance).
4. Fullness fit — a **heuristic**, from estimated satiety potential compared to subjective hunger/next-meal target.

The user sets relative sliders that normalize to sum to 100%, with defaults at 25% each. Ranking = weighted sum of the four scores + a **clearly separate** soft preference bonus of 6 points per preferred dish satisfied (maximum 12). Mandatory dish minimum quantities are hard feasibility constraints, not bonuses.

Multiple categories are selected via exhaustive search: best overall under weighted scoring; budget among cheapest; best nutrition; most filling within a broad calorie-relevance window; and best calorie match. Recommendations attempt to use distinct dish combinations across categories, falling back to rice variants where necessary. These results are globally best for the chosen discrete search and scoring method, *not* claims of global nutritional optimality in the physical world.

## Accuracy limitations and validation

**Important:** Nutrition values are provisional recipe-level estimates based on general cooking methods and visible ingredients. They have NOT been independently matched to an official nutrient record for these specific canteen dishes, measured chemically, or confirmed with canteen recipes. Do not present them as verified nutritional facts or rely on the exclusion control for food allergy safety.

- ±8% represents assumed serving-weight fluctuations. Actual canteen variance has not been measured.
- A separate dish-dependent recipe variation estimate reflects unknown ingredients, oils and sauce composition. The ranges are scenario-model ranges, **not clinical confidence intervals**.
- The 100-scenario Monte Carlo test reports model stability under assumed uncertainty distributions. It is not measured prediction accuracy.
- The estimated fullness model is unvalidated for individual students and should not be used as a medical satiety forecast.
- The optional energy equation uses Mifflin–St Jeor, a simplified activity factor, a chosen goal adjustment, and a one-third-per-meal assumption. It is for adults age 18 or above only and is not medical advice.

External scientific methodology links available in Food Library:
- USDA FoodData Central: https://fdc.nal.usda.gov/
- FAO/INFOODS recipe calculation guidance: https://www.fao.org/infoods/infoods/recipes/en/
- Mifflin–St Jeor original citation: https://pubmed.ncbi.nlm.nih.gov/2305711/

## How to make the results academically defensible

For a stronger report, measure at least some actual serving weights to estimate the true distribution; reconstruct ingredient recipes using published reference data; compare independent reference estimates; do sensitivity analysis on oil, sauces, rice and edible bones; and collect volunteer feedback at standardized intervals to evaluate the fullness model. The algorithm can be mathematically verified now, but real-world nutritional or satiety validity requires such observations.
