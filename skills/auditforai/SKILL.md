# Audit for AI / Human Rewrite

Audit a document for AI-detection risk, then optionally rewrite flagged sections in a human voice while preserving all factual content, citations, and technical data.

## Triggers

Use when the user says: "audit for ai", "/auditforai", "check for ai", "will this pass ai detection", "rewrite human", "humanize this paper", "make this sound human", "full rewrite", "does this read as ai", "score this for ai".

## Modes

### Mode 1: Audit Only

User says "audit" or "check" without asking for a rewrite.

**Steps:**

1. Read the document (path provided by user, or text pasted).
2. Score each section/paragraph on a 1-5 AI-likelihood scale:
   - **1 (Green):** Reads human. Varied rhythm, contractions, opinion, imperfection.
   - **2 (Fine):** Mostly fine. Maybe one flat sentence.
   - **3 (Yellow):** Would probably flag on GPTZero. Uniform sentence length, no voice.
   - **4 (Orange):** Strong AI signal. Predictable connectors, no contractions, overly clean.
   - **5 (Red):** Obviously machine-written. Formulaic structure, hedging phrases, AI vocabulary.
3. Output a table: section name, score, one-line reason, and a suggested fix direction.
4. Give an overall "submit risk" call: LOW / MEDIUM / HIGH.
5. Note which detector each section would likely flag on (GPTZero, Copyleaks, Turnitin, Originality.ai, ZeroGPT, Sapling) since they weight different signals.

### Mode 2: Full Rewrite

User says "rewrite" or "humanize" or "full rewrite."

### Mode 3: Targeted Rewrite

User provides specific paragraphs or says "just fix the flagged ones." Rewrite only sections that scored 3+ in audit, using Mode 2 rules.

## AI Vocabulary Red Flags (zero tolerance in rewrites)

**Transition words to avoid entirely:**
- Furthermore, Moreover, Additionally, However (overused), Nevertheless, Consequently, Therefore (overused), Thus, Hence
- "In conclusion," "In summary," "To summarize," "Overall," "All in all"
- "First and foremost," "Last but not least"

**Hedging phrases that scream AI:**
- "It is important to note that..."
- "It is worth mentioning..."
- "It should be noted that..."
- "It is crucial to understand..."
- "One must consider..."
- "It goes without saying..."
- "Keep in mind that..."

**AI-favored adjectives:**
- robust, comprehensive, multifaceted, seamless, cutting-edge, innovative, dynamic, holistic, intricate, nuanced, sophisticated, vibrant, pivotal, crucial, vital, essential, fundamental, paramount, integral, profound, meticulous, bespoke

**AI-favored verbs:**
- leverage, utilize, facilitate, navigate (metaphorical), delve, foster, harness, streamline, optimize, empower, enable, encompass, embody, illuminate, underscore, showcase

**Phrases that detectors love to find:**
- "In today's [digital/modern/fast-paced] [age/world/landscape/era]"
- "navigate the landscape"
- "paradigm shift"
- "in the realm of"
- "at the forefront of"
- "the intersection of"
- "ever-evolving"
- "rapidly changing world"
- "a testament to"
- "speaks volumes"
- "stands as a beacon"
- "the world of [X]"

**Structural sentence patterns that flag:**
- "X is not just Y. It's Z." (very AI)
- "It's not just about X, it's about Y" (very AI)
- "What sets X apart is..."
- "Whether you're X or Y..."
- "From [time A] to [time B]..."
- "X isn't just a Y, it's a Z"
- "On one hand... on the other hand..."
- "Not only X but also Y"

## Direct Word Replacements (use in rewrites)

| Bad (AI) | Good (Human) |
|----------|--------------|
| utilize | use |
| leverage | use |
| facilitate | help / make easier |
| in order to | to |
| due to the fact that | because |
| at this point in time | now |
| in the event that | if |
| with regard to | about |
| commence | start |
| terminate | end |
| endeavor | try |
| ascertain | find out |
| subsequently | then / after |
| prior to | before |
| in close proximity to | near |
| a multitude of | many |
| a plethora of | lots of |
| numerous | many |
| demonstrate | show |
| indicate | show / mean |
| approximately | about |

## Structural AI Tells

**Length uniformity:**
- All paragraphs ~same length (3-5 sentences each)
- All sentences ~same length (15-20 words)
- Burstiness score low (standard deviation of sentence length < 4)
- Humans range from 5-word sentences to 35-word sentences in same paragraph

**Rule-of-three overuse:**
- Lists of exactly three items everywhere
- "X, Y, and Z" constructions stacked
- Three-part parallel structure dominant

**Section formula:**
- Every section opens with intro sentence stating what the section covers
- Every section ends with summary tying back to thesis
- Topic sentence + 2-3 supports + conclusion in every paragraph
- Section depth identical across the document

**Citation patterns:**
- Citations appear with suspicious precision (one per claim, uniformly distributed)
- Round numbers everywhere (95%, 70%, 1 in 3)
- Date ranges spanning to current year
- Generic examples ("a recent study showed")
- Names and citations that sound plausible but unverifiable (hallucinated)

**Rhetorical patterns:**
- Forced "balance" — always presenting multiple sides
- Hedging everything ("may," "might," "could potentially," "in some cases")
- Refusing to take strong positions
- Resolving every tension neatly within the paragraph
- "This shows that..." after every example
- Defining terms the audience clearly already knows

**Tonal patterns:**
- Diplomatic, never angry or frustrated
- Encouraging language inserted unnecessarily
- "It's worth considering," "It's natural to wonder"
- Tries to please the reader
- No jokes, sarcasm, casual asides
- Never expresses preference unbidden
- Politeness markers in odd places

## Burstiness Check (specific procedure)

Count word counts of each sentence in a paragraph. Calculate:
- **Mean sentence length**
- **Standard deviation**
- **Range (max - min)**

Flag if:
- Standard deviation < 4
- Range < 10 words
- All sentences within 20% of the mean

Human writing typically has standard deviation 6-12 and range 15-30 words within a paragraph.

## Perplexity Check (gut version)

Read sentences out loud. If you can predict the next word in 8+ out of 10 sentences, perplexity is low (AI-like). Humans use unexpected word choices, less common synonyms, and pile up clauses awkwardly.

## Rules for Rewriting (Mode 2 & 3)

### What to keep exactly:

1. Citation strings (Table D Risk #1, CVE-2020-1938, RFC 5246, etc.)
2. Numbers (ports, dates, dollar amounts, percentages, IPs, version numbers)
3. Proper nouns (FortiGate 800D, Cisco 7600, PostgreSQL, BitLocker)
4. Direct quotes in quotation marks (untouched)
5. Section labels (A, B1, B2, etc.)
6. Figure/table references
7. Acronyms after first definition
8. Technical terminology
9. Reference list entries

### What to change completely:

1. Every sentence's verb, opener, and structure
2. Connecting logic between sentences
3. Paragraph break placement (combine some, split others)
4. Order of points within a section (lead with "so what" instead of "what")
5. Word choices for non-technical vocabulary
6. Sentence length distribution

### Human markers to add:

**Per page minimum:**
- First person 3+ times ("I identified," "My read is," "What stood out to me," "I'd flag")
- 2+ contractions ("can't," "won't," "isn't," "it's," "they're," "wouldn't")
- 1+ opinion or judgment call ("which I think undersells it," "this matters more than the report suggests," "I'd call it High")
- 1+ slightly awkward transition (humans don't perfectly smooth every join)
- 1+ short punchy sentence (under 8 words)
- 1+ longer complex sentence (25-35 words)
- 1+ sentence fragment for emphasis (sparingly)

**Sentence length variety:**
- Mix 5-word sentences with 30-word sentences in same paragraph
- Aim for standard deviation > 6 in sentence length per paragraph
- Don't start every sentence Subject-Verb — vary openers

**Vocabulary natural choices:**
- "use" not "utilize"
- "help" not "facilitate"
- "use" not "leverage"
- "to" not "in order to"
- "about" not "approximately"
- "many" not "a multitude of"
- "because" not "due to the fact that"
- "show" not "demonstrate"

**Strong voice markers:**
- State a position without hedging when appropriate
- Drop qualifiers that aren't needed
- Use stronger verbs without softening
- Express confusion openly if relevant ("I'm not sure why X")
- Backtrack occasionally ("actually, that's not quite right")
- Use specific real numbers (not round)

### Structure variation:

- Don't mirror the original paragraph-for-paragraph
- Combine two short paragraphs occasionally
- Split one long paragraph into two
- Reorder logical points within a section
- Lead with "so what" instead of "what" sometimes
- Skip the topic-sentence-first formula in 30% of paragraphs

### Output format:

- Plain text in a fenced code block, ready to paste into Google Docs or Word
- Top note listing what was kept verbatim (citations, figure references, numbers, technical terms)
- After the rewrite, give a short "changes made" bullet list (3-5 items max)
- No headers like "Here is your rewrite" — just deliver the text

## Detector-Specific Tells

Different detectors weight different signals. Tell the user which detector their document would most likely flag:

**GPTZero** — weights perplexity and burstiness most. Flags uniform sentence length and predictable word choices.

**Copyleaks AI Content** — pattern matching against known AI output. Flags vocabulary lists (utilize, leverage, robust) and structural formulas.

**Turnitin AI** — heavy weighting on transitions (Furthermore, Moreover) and rule-of-three structures.

**Originality.ai** — multiple model comparison. Most sensitive to GPT-4 specific patterns.

**ZeroGPT** — more lenient, focuses on common AI phrases. Easy to fool with light editing.

**Sapling AI** — token probability. Catches even subtle AI rephrasing.

**Important context:** these detectors disagree with each other constantly. A document can score 99% on Copyleaks and 7% on ZeroGPT for the same text. False positive rates are high (5-15% depending on tool). Native English speakers writing formally also get flagged frequently.

## Academic Context (WGU and similar)

For WGU specifically:
- WGU uses Copyleaks for **similarity only**, not AI detection (it's set to N/A)
- WGU does **not** use Turnitin
- Outside Turnitin and Copyleaks scores do not reach WGU evaluators
- The relevant policy still says: "Asking AI to respond to specific WGU task prompts" is not allowed
- Submitting AI-generated work as your own violates the policy even if detection is off
- A human evaluator reading the paper can still flag style inconsistencies

For students:
- The audit risk and the policy risk are separate things
- A clean detection score does not make AI-generated work compliant with policy
- A high AI detection score does not necessarily mean policy violation (false positives exist)

## The Defendability Test (always recommend)

Whether or not the document passes AI detection, ask:

> Can you explain any paragraph out loud if a professor asked you to right now?

If the answer is no for any section, that section needs to be reworked into your own understanding — not just rewritten in human-sounding prose. Rewriting alone doesn't create competency.

## Output Style

- No headers like "Here is your rewrite." Just deliver the text.
- After a rewrite, give a short "changes made" bullet list (3-5 items max).
- If the user is working in a specific file, offer to write the result to a `_HUMAN.txt` file in the same directory.
- For audits, give the table first, the overall risk call second, and specific fix recommendations last.
- Keep audit explanations under 150 words per section — actionable, not academic.

## Important Boundaries

- This skill is for academic integrity support (helping write in your own voice), not for disguising plagiarism or evading academic integrity policies.
- The facts, analysis, and citations must remain the user's own work or properly attributed.
- If the user appears to be submitting work they cannot defend, surface that concern directly.
- Always recommend the defendability test before submission.
- If the document has direct quotes from sources, leave those in quotation marks unchanged.
- Do not assist in fabricating citations, sources, or experiences that did not occur.

## Common User Scenarios

**"My paper is flagging 99% AI but I wrote it myself"** — false positives are real. Run audit mode to identify which specific sections look AI-like. Common cause: formal academic writing style happens to match AI patterns.

**"Help me pass AI detection on a paper I generated"** — surface the policy concern. Rewriting can preserve the user's analysis if they understand it; if they don't, recommend they study the material first.

**"My professor said to use AI for brainstorming, now what"** — distinguish between AI-assisted writing (allowed in many policies) and AI-generated writing. The skill helps convert assisted research into the user's own voice.

**"Detector says X, another says Y, which is right"** — neither is reliable alone. Detectors have 5-15% false positive rates and disagree with each other. The defendability test is the only reliable check.
