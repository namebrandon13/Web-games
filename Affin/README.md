# Affix Tree

Four files. The HTML holds no content — everything comes from JSON.

| File | Role |
|---|---|
| `affix_tree.html` | The game. Reads the two JSON files, writes the third. |
| `game.config.json` | Main game file: modes, rules, scoring, wording, theme, tree geometry. |
| `questions.json` | The question bank. This is the file teachers edit. |
| `results.json` | Sample output. The game writes a file in this exact shape. |

## Running it

Browsers refuse to read local files from a page opened by double-click, so serve the folder:

```
cd affix-tree
python3 -m http.server
```

Then open `http://localhost:8000/affix_tree.html`.

If you can't run a server, open the HTML anyway — it will show a file picker where you select
`game.config.json` and `questions.json` together, and the game starts from those.

## Editing questions

Each mode holds an array under `sets`. A question looks like this:

```json
{
  "id": "syn-06",
  "definition": "Extremely tired.",
  "target": "exhausted",
  "validLeft": "weary",
  "validRight": "drained",
  "leftOptions": ["weary", "alert", "fresh", "eager", "lively"],
  "rightOptions": ["drained", "rested", "energetic", "keen", "brisk"],
  "explanation": "Optional. Shown once the question is answered."
}
```

Rules the game relies on:

- `id` must be unique across the file — results are recorded against it.
- `validLeft` must appear in `leftOptions`; same for the right side.
- Up to **5 options per side** — one per branch. Fewer is fine, extra branches are left bare.
- Sentence-mode questions use a `sentence` field for the clue; the trunk shows a blank instead of the word.

To split the bank across several files, set `game.questionsFile` in the config to an array:

```json
"questionsFile": ["questions.synonyms.json", "questions.antonyms.json"]
```

They are merged by mode.

## Config worth knowing

- `rules.maxAttemptsPerQuestion` — set to `0` for unlimited. At `3`, a third wrong answer reveals the
  correct branches and records the question as missed, which is what keeps the results meaningful.
- `rules.shuffleQuestions` / `shuffleOptions` — off by default so every student sees the same paper.
- `scoring` — points for first-try vs. later correct answers.
- `feedback` — all on-screen wording, with `{left}` and `{right}` placeholders.
- `results.endpoint` — set a URL and finished sessions are POSTed there as JSON as well as being downloadable.
- `layout` — trunk shape, branch positions and button sizing. Add a sixth branch to each side and the
  game will accept six options per question.

## Results

A session records every press of **Check**, not just the final answer. Per question you get the
attempt list with what was chosen each time, thinking time before the first attempt, total time on
the question, whether it was right first try, and the points awarded. The summary adds accuracy,
total and average time, and fastest/slowest question.

Results are saved to `localStorage` as you go (key set in the config) and downloaded from the
**Download results.json** button on the summary screen.
