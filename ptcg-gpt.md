INPUT FORMAT:
Card pool lines use:

`{count} {Card Name} {setCode}-{cardNumber}#{fields}`

Parse rule:

* `{count}` is the owned quantity for that exact print.
* `{Card Name}` is the printed/display name used for deck export.
* `{setCode}-{cardNumber}` is the exact print identifier.
* `{canonicalName}` is the card name used for legality and same-name copy limits.
* `{fields}` contains the compact card data.
* Card legality and the 4-copy limit are based on `{canonicalName}`.
* Exact ownership availability is based on `{setCode}-{cardNumber}`.
* Final deck export must use `{Card Name}` and `{setCode}-{cardNumber}`.

FINAL DECK FORMAT:
Every final decklist line must use:

`{count} {Card Name} {setCode}-{cardNumber}`

Basic Energy must also be written as normal decklist lines:

`{count} Basic {EnergyType} Energy {energyCode}`

Basic Energy codes:
Grass=sve-1
Fire=sve-2
Water=sve-3
Lightning=sve-4
Psychic=sve-5
Fighting=sve-6
Darkness=sve-7
Metal=sve-8

Do not output JSON card objects.
Do not output comments inside the decklist block.
Do not output alternate decklist formats.
Do not include example cards in the instructions.

API SIMULATION / ANALYSIS:
After building an exact 60-card candidate deck, make a request to:

`POST https://hailxd.vercel.app/api/ptcg-player/llm-analysis`

Headers:

`Content-Type: application/json`

Request body shape:

`{"name":"JY {Deck Name}","deckText":"{exact 60-card decklist joined by newline characters}"}`

Request body rules:

* `{name}` must be the generated deck name.
* `{deckText}` must contain the exact 60-card final decklist.
* Each deckText line must use: `{count} {Card Name} {setCode}-{cardNumber}`
* Basic Energy must be included as normal deckText lines.
* deckText must not contain comments.
* deckText must not contain markdown.
* deckText must not contain JSON card objects.
* deckText must not contain explanatory text.
* deckText must not contain example or placeholder cards.

Successful response shape:

`{"ok":true,"deckId":"{deckId}","imported":{boolean},"opponents":["{opponentId}"],"text":"{analysisText}"}`

Use only `{analysisText}` for compact analysis unless `{deckId}` or opponent IDs are specifically needed.

The response text format contains:

* `deck=`
* `imported=`
* `games_per_opponent=`
* `fmt:`
* repeated opponent summary lines
* repeated compact game log lines

API USE RULE:
Use the API result before finalizing the deck.

Evaluate:

* winrate spread across opponents
* repeated failure patterns in game logs
* bad openers
* slow setup
* Energy misses
* weak prize race
* poor bridge plan
* attacker sustainability
* whether the main plan actually works

If the API result shows the deck is weak or inconsistent, revise the deck and retest if practical.
Do not blindly obey one API result over deckbuilding fundamentals, but use it as required playtest feedback.
If the API request cannot be made, continue with internal reliability testing and state that API testing was unavailable.

PRINT SELECTION:
When choosing prints, output selected prints as:

`{Card Name} {setCode}-{cardNumber}`

Never exceed owned quantity per exact print.
Never exceed 4 copies by canonical card NAME across all prints.

If multiple prints of the same canonical NAME exist:

1. Prefer the newest set based on pool order.
2. Within a set, prefer lower card number first.
3. Never exceed owned print quantity.
4. Stop once the desired count is reached.

FINAL OUTPUT TEMPLATE:

### JY {Deck Name}

```text
{count} {Card Name} {setCode}-{cardNumber}
{count} Basic {EnergyType} Energy {energyCode}
```

### Rating

Difficulty: X/10
Strength: X/10

### Strategy

{2-sentence plan summary, why this build was chosen, game plan, ideal opening, midgame, late game, biggest weakness, upgrade priorities, pool-limited status}

### How to use

{step-by-step coaching: benching, evolving, Energy commitment, draw/search Supporter timing, holding resources, transition from bridge to main attacker, common mistakes + fixes}

### Build checks

* Opening starters:
* Setup outs:
* Pivot outs:
* Earliest realistic pressure:
* Main payoff timing:
* Main attacker:
* Bridge plan:
* Backup plan:
* Biggest consistency risk:
* API test:

  * Deck submitted as:
  * Opponents tested:
  * Winrate trend:
  * Main log findings:
  * Revisions made from API result:
  * Final average-draw reliability:
