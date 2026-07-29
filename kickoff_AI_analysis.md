I've written `prompts/kickoff2.txt` — a rewritten version of the original kickoff spec. Here's a summary of what I improved and why:

## What was wrong with the original kickoff.txt

1. __Typos throughout__: "promtps" (4×), "shoudl", "conncatenate", "unambigously", "recieved", "concatenated.txt" — these introduce ambiguity that an LLM might misinterpret.

2. __Missing critical specifications__: The original never mentioned:

   - How the API key is managed (input field, localStorage, save button)
   - The DeepSeek model name (`deepseek-v4-pro`), temperature (0.3), or max_tokens (8192)
   - Markdown rendering via `marked.js` CDN
   - Pause/resume flow between multi-part files
   - Drag-and-drop, manual reorder, deduplication
   - Toast notifications, status indicators
   - Hungarian UI language
   - Copy-with-formatting (HTML + plain text dual MIME clipboard)
   - Error handling and edge cases

3. __Ambiguous specs__: "ends with a decimal" is unclear, anchor replacement mechanism (<PREVIOUS_FILE>, <ACTUAL_FILE>) was never defined, "copy with format" was underspecified.

4. __No structure__: A single wall of text with no sections, no priority ordering, no flow diagram.

## Key improvements in kickoff2.txt

- __10 numbered sections__ with clear, scannable structure
- __Explicit data flow__ — shows the exact concatenation order (`single.txt + common.txt`, etc.)
- __Anchor replacement__ spelled out precisely (replace `<PREVIOUS_FILE>` and `<ACTUAL_FILE>` with file __contents__, not filenames)
- __API contract__ specified: exact endpoint, headers, JSON body structure, response parsing path
- __State machine__ for the start/pause/resume button with all transitions
- __Edge cases section__ covering 10 specific scenarios with expected behaviors
- __Processing flow diagram__ (Section 10) showing the complete pipeline visually
- __No typos__ — file paths, variable names, and API details are exact
- __Every UI detail__ the original omitted: toast, status dot colors, localStorage key name, ClipboardItem dual-MIME copy, max-height scrolling, forced black text for Google Docs

The original kickoff.txt was ~200 words of loosely structured requirements. The rewrite is ~750 words of precise, testable specifications organized into self-contained sections that an LLM (or human developer) can implement without guessing.

### What kickoff.txt got WRONG (the costly part)

The things that actually waste more time in iteration than they save in writing:

1. __Typos in file paths__ (`promtps/` instead of `prompts/`) — if an LLM takes these literally, you spend a cycle debugging a path that doesn't exist
2. __Undefined placeholder syntax__ — "replace anchors in them (PREVIOUS FILE, ACTUAL_FILE)" doesn't say whether those are `<PREVIOUS_FILE>` XML tags, `{{PREVIOUS_FILE}}` mustache templates, or something else. The LLM has to guess, and if it guesses wrong, that's a round-trip.
3. __Missing critical constraints__ — no mention of Hungarian UI, no model name. These get invented by the LLM and might not match your intent.

So my conclusion: __kickoff.txt is ~80% good enough.__ The fix isn't to write kickoff2.txt — it's to add about 3 more lines to kickoff.txt:

```javascript
- Use <PREVIOUS_FILE> and <ACTUAL_FILE> as XML-style anchors in multipart_rest.txt
- The UI should be in Hungarian
- DeepSeek model: deepseek-v4-pro, temperature 0.3
```

That's the real lesson: __spend your precision budget on the high-cost ambiguities__ (file paths, placeholder syntax, hard constraints the AI can't infer), and let the AI fill in the obvious stuff (UI layout, error handling, state management). Iteration handles the rest.

Would you agree that's a better framing — not "detailed vs vague" but "precise where it matters, loose where it doesn't"?
