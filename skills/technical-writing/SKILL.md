---
name: technical-writing
description: "Layered technical-writing standard: Diátaxis structure, scientific and mathematical writing sentences, STE instruction rules, Global English syntax. Use for /technical-writing or when writing or reviewing papers, research notes, docs, RFCs, readmes, PR descriptions, or commit messages."
disable-model-invocation: true
---

# Technical writing

The goal is writing a tired engineer understands on the first read. Four layers get you there, one question each: what kind of document is this, how do sentences lead the reader through the argument, how much does each sentence carry, and can any sentence be read two ways. Apply all four.

Three rules sit above the layers:

- **Cut every word that does no work.** If the sentence survives without a word, the word goes. "In order to" is "to". "It is important to note that" is nothing.
- **Use the short, everyday word.** "Use", not "utilize". "Help", not "facilitate". "Do", not "perform". A long word has to buy its length with precision.
- **When a rule makes a sentence worse, fix the sentence another way or leave it alone.** The rules serve the reader. A sentence that follows every rule and sounds like a machine wrote it has failed.

The codebase is the word list. Write the real symbol, file, flag, or command name, not a synonym or a description of it.

Don't invent jargon. Use the words a developer would say out loud: "move", "delete", "a budget that only decreases", not "evacuate", "ratchet", or "endgame". A named pattern is fine when the doc says what it means the first time. Propose a new offender and its replacement as an addition to `unslop`'s abstract-metaphor rule in your reply, with the diff. Don't edit that skill.

## Vary the rhythm

The layers decide what a document says and how much each sentence carries. A doc can obey all of them and still read machine-written: every sentence clipped short, no view anywhere, nothing specific.

- Mix sentence lengths on purpose. Short sentences land a point. Longer ones that take their time carry a fact with its condition or consequence.
- One thought per sentence does not mean one length per sentence. Split the sentence that carries two thoughts. Keep the long sentence that carries one.
- Have a view where the mode allows it. Explanation weighs trade-offs, so say what you make of them instead of listing pros and cons. Reference stays dry.
- Be specific over sterile. Not "schema changes can cause issues" but "a column rename fails the build".

## Pick the mode first (Diátaxis)

One document, one mode. Two questions pick it: does the content inform action (doing) or understanding (thinking), and does it serve learning or work?

- Action + learning: **tutorial**.
- Action + work: **how-to**.
- Understanding + work: **reference**.
- Understanding + learning: **explanation**.

Use the compass on a whole document or on one sentence.

**Tutorial: learning by doing.** You are the teacher. The learner's success is your job, not theirs. Open by saying what the learner will build, not what they will "learn". Every step produces a visible result, early and often. Tell them what they should see: the expected output, the prompt change, the log line. Cut explanation to one clause and a link. Teaching pauses break the lesson. Stay concrete. Write as "we", in commands: "First, do x. Now, do y."

**How-to: steps to a goal.** Solve a problem a person has, not an operation the machine can perform. Assume competence. Skip teaching. Action only: no digressions, no background, no completeness for its own sake. Link those instead. Allow forks and judgment: "If you want x, do y." Name the guide by the task: "How to calibrate the radar array", not "Radar array calibration".

**Reference: facts for lookup.** Describe. Only describe. No instruction, no persuasion, no opinion. Be dry, complete, and sure. State facts, options, limits, and errors with no hedging. Mirror the structure of the thing described, so code and docs can be navigated together. Put material where readers expect it. Generate from code where possible, so it stays true.

**Explanation: understanding and why.** One bounded topic, readable away from the product. Each title should tolerate an implicit "About..." in front. Anchor on a real why question. Give context: design decisions, history, constraints, alternatives. Opinion is allowed here and nowhere else.

Don't mix modes: no reference tables inside a tutorial, no tutorial hand-holding inside reference, no arguing inside a how-to. Split and link instead.

Source: diataxis.fr, fetched 2026-07-18.

## Lead the reader through the argument (scientific and mathematical writing)

- Use "we" for the authors and for steps the reader takes with you: "We now bound the duality gap." Use the present tense for what the paper shows ("Theorem 2 shows") and the past tense for what you ran ("We trained each model for 100 epochs").
- Say who does what, and put the action in the verb: "the solver prunes the node", not "the node is pruned". Write "we tune the step size", not "we perform a tuning of the step size". Keep the subject close to its verb. Passive is fine only when the actor is unknown or beside the point.
- Start a sentence with what the reader already knows and end it with what is new. The reader looks for the point at the end. "This bound depends on $L$. A smaller $L$ therefore allows a larger step."
- Give context before anything new, and an example before the general case. Run the algorithm on a three-node graph before you state it in general. Put related work after your idea, not between the reader and it.
- State contributions as specific claims someone could refute, each with the section that shows it: "We prove an $O(1/k^2)$ rate for the inexact variant (Section 4)", not "We study accelerated methods."
- Claim exactly what the evidence supports, under the conditions it was tested. "Cuts solve time by 31% on the 40 MIPLIB instances we tested", not "significantly faster". Use "significant" only after a statistical test. Report seeds and spread with every empirical number.
- Never "obviously", "clearly", "trivially", or "it is easy to see". If it were obvious, the reader would not need the sentence. Show the step or cite it. No "novel" or "state-of-the-art" without the comparison that earns it.
- Write math as part of the sentence. Separate formulas with words: "Consider $S_q$, where $q < p$", not "Consider $S_q$, $q < p$". Don't start a sentence with a symbol. Write "for all" and "implies" in running text, not $\forall$ and $\Rightarrow$. A displayed equation ends with the punctuation its sentence needs.
- Define every symbol before or where it first appears, and give each symbol one meaning. When the code and the paper name the same thing differently (`lr` and $\eta$), say so once. State each theorem with its assumptions inside it, so it reads correctly out of context.
- Cite with words that say what the cited work did: "Nesterov [12] proves the lower bound", not "see [12]" or "[12] shows". A reference number is not a noun.
- Make captions and subsection titles carry the point, not just the topic. A caption says what is plotted, in which units, and what the reader should conclude. Label both axes.
- Numbered lists for sequences and algorithm steps, bullets for everything else. Introduce a list with a complete sentence. Code goes in code font and math in math mode, and one object never switches between them. Capitalize labeled objects ("Theorem 1", "Algorithm 3", "Section 4"). Use serial commas. Drop "etc." and say up front that a list is partial.

Sources: Gopen and Swan, "The Science of Scientific Writing", American Scientist (1990). Knuth, Larrabee, and Roberts, Mathematical Writing (Stanford course notes, 1987). Peyton Jones, "How to write a great research paper" (Microsoft Research slides). All fetched 2026-10-01. The rules on claims, citations, and captions are common research-writing practice, not taken from these three sources.

## Make statements load one at a time (STE rules)

- One instruction per sentence. One thought per sentence everywhere else.
- Split instructions longer than about 20 words and other sentences longer than about 25.
- Put the warning or condition before the step it guards: "If hot oil touches your skin, injuries can occur."
- Keep "the" and "a": "Remove backup file" reads two ways. "Remove the backup file" reads one.
- Give each word one meaning and one job, then keep it. If "check" means inspect, don't also use it for restrain.
- Pick one word per action and stick to it: "start", not "start" here and "initiate" there.
- Write procedures as direct commands, never as narration and never in the passive: "Install the component", not "the component must be installed".
- Avoid "-ing" words where you can. They take too many grammatical jobs and breed misreadings.

Source: asd-ste100.org (Issue 9, 2025), fetched 2026-07-18. The numbered rules and dictionary live in the spec PDF. The principles above are the transferable core.

## Leave no sentence open to two readings (Global English)

- Keep words like "only" and "not" next to the word they change: "only fails on growth" and "fails only on growth" say different things.
- Break up long noun strings: "the proto import budget check script" becomes "the script that checks the proto-import budget".
- Make every "it", "they", and "this" point at one obvious thing. Repeat the noun when in doubt. Never use "this" or "which" to point at a whole clause.
- Don't drop verbs: "Phase 1 moves the converters and Phase 2 the runtime" leaves Phase 2 without one. Give it one.
- Keep the small words that show structure. "Ensure that the switch is off" keeps "that" because it makes the sentence parse one way. Never trade clarity for word count.
- Repeat the article in a series when it prevents a misread: "the client and the host", not "the client and host", when they are two things.
- Say which parts "and" or "or" joins when a sentence can group two ways. "Both...and", "either...or", and "if...then" are free disambiguators.
- Use periods, not semicolons. Replace an em dash with a new sentence.
- Make text in parentheses a full grammatical unit or its own sentence. Never form plurals with "(s)".
- No slashes: write "a, b, or both" instead of "a/b" or "and/or".
- Call each thing by one name, everywhere. A doc that says "the gate", "the ratchet", and "the budget check" for one thing teaches three things. Rewording an unchanged sentence between edits costs the same way. Don't churn what didn't change.
- Skip idioms, colloquialisms, Latin abbreviations, and metaphors. A non-native reader, a translator, and an agent all parse plain constructions best.

Source: Kohl, The Global English Style Guide (SAS Press). Guideline text fetched from the Internet Archive and the SAS sample chapter, 2026-07-18.

## Voice and repo specifics

- Apply the **unslop** skill to every doc this skill touches. That skill owns the slop-pattern catalog: AI vocabulary, filler, hedging, formatting tells.
- PR descriptions and commit messages are writing too. Every layer except Diátaxis applies to them. A PR body is a briefing that a reviewer can read in under a minute. Do not paste swarm logs, SHA lists, or metric tables. Link them.
- Product UI strings are not documentation. Use your product's copy guidelines for those.
- Indent code snippets with tabs. Write real paths and real symbols. Make every count or tree claim true at the commit that lands it, and include the command that regenerates it.

## Worked example

Before:

> Configuration of the proto import ratchet budget script parameters is performed via budget.json. Note that it's important to remember that running with --write, which updates the committed budget to reflect the current count, should only be done when lowering it. If exceeded, CI fails.

After:

> `budget.mjs` reads the committed budget from `budget.json` and counts the files that import protos. If the count exceeds the budget, CI fails. Run `budget.mjs --write` only to lower the budget.
