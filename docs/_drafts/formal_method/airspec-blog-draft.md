# How does formal logic fit into AirSpec

*Transcribed from the seven-page handwritten draft. Clear cross-outs have been omitted and clear handwritten replacements incorporated. Line breaks, punctuation, and AirSpec capitalization have been lightly normalized; the wording and unfinished passages have not been rewritten. Square-bracketed notes identify uncertain readings or unfinished text. Red annotations are labeled explicitly because plain Markdown does not preserve ink color. Answers and suggested additions are in the separate companion file, `airspec-red-question-answers.md`.*

## Page 1

How does formal logic fit into AirSpec.

How is it different from typed variables and decision tables

Can it help us build a spec. Can it help us use the spec?

We are shifting work from the system implementation phases to a single formalization phase. Formalization is the process where we translate the specification we have in natural language into AirSpec.

Imagine we have done a rule in AirSpec and it sits there in its format. Are we content? Does it “work”? What does it even mean that a rule that have been formalise in AirSpec “works”. Well, it needs to capture the intent of the [unclear handwritten insertion] natural language specification.

So how can we check that? In a normal system implementation this is done by testing. We feed some example data into the system and assert that the system behaves according to our expectation.

## Page 2

Insert Dijkstra quote about testing. This approach can show the existence of bugs but never the absence of bugs. Additionally, its very difficult to create test cases for solvers as they may come up with solution a human would never have dreamed of.

So while testing has its problems it's at least much better than nothing. So how can we test a spec? By definition a spec “does” nothing so there's no expected behaviour to assert. We could of course build a tool that implements the AirSpec format and checks trips or rosters and basically does the same thing that we do for a system implementation. But now we are actually testing the behaviour of the system rather than the spec. And if two different people builds two checkers based on AirSpec there's no guarantee that they'd behave exactly the same.

So ideally we want to reason about the spec without building a rule engine. What other options do we have. We can pull out semantic pieces and translate them into something more understandable

## Page 3

like i.e. flowchart visualizations. This can be a powerful method of working with the spec and can give experts assurance that the spec follows its natural language intent. Additionally one can use an agent to convert spec back into NL to support the experts assessment. While this is very useful its manual work. We accept the fact that our rule domain is dynamic and thus the spec will have to be changed often. It would be fantastic if we could guarantee properties of the spec automatically with the help of a computer. Something like a unit-/regression test suite but for a specification.

Enter the world of formal methods.

Rules are logical formulation that expresses relation between scheduled activities. (This is a very strong formulation, not sure if it always holds) And the beauty of logic is that is an exact science and can be derived formally.

Let's take a simplified minimum connection time rule.

AirSpec format and flowchart.

## Page 4

Now if we want a certain property to hold we can express it as a theorem.

> A trip t is legal with respect to rule [set?] iff ∀ flights associated with trip t fulfills

*[The theorem stops here in the manuscript.]*

Now we can manually create a trip and logically deduce that the trip is legal. That wouldn't be fun and is a much more cumbersome process than just reason about it using natural language and visual support. But computers can help. By translating or formalizing our spec to a logical formulation we can use proof assistance or satisfiability solvers to automatically prove our theorem holds without Building a Rule Engine.

This, if done right, can be incredibly powerful and open up a whole world of possibilities.

Not only can we create a “Regression test suite” for our spec, i.e., a set of schedules/trips that must always be legal or fail which is a necessity when updating shared definition

## Page 5

We can also prove statements such as

> “There exists no superfluous rule in the system”

i.e., all rules actually restrict the solution [space?]

Or “this version of a rule is more restrictive than the previous version”

etc. etc.

**[Red annotation R1] ADD MORE EXAMPLE**

We would be able to reason about the ruleset and the schedules it entails without having to produce a solution.

Introduce Luo et al.

Now what we are able to do formally depends on what we allow to be expressed in AIRSPEC

**[Red annotation R2] IS THIS TRUE?**

Is typed variables and decision tables reducable to relational logic

**[Red annotation R3] ???**

## Page 6

**[Red question R4] What Type of propositions can be expressed as a Boolean SAT, as a SMT.**

**[Red question R5] How does Lean or proof assistance come into the picture (if at all?)**

Maybe the angle or title should be

- What led me into a deep dive into formal methods?

I was struggling to find a way to reason about the properties of a spec. How can we compare two versions of a spec and decide which is “better” without only relying on just personal preference? Should we be able to use e.g. “rest periods” as a first class citizen of a spec or not? One could claim that it helps describing rules, or by having it in our main vocabulary we will be able to more naturally express some rules using rest as a first class citizen rather than simply expressing rest periods as properties on duties (e.g. `∀d duty.%rest_after% ≥ 10:00`) vs `∀r ∈ RestPeriods r.duration ≥ 10:00`.

Is this just a matter of taste or does it

## Page 7

have implications on the spec?

This led me to start to think about the underlying logics behind AirSpec. Something that we have not yet discussed. What type of logic is best suited to express crew rules. And what implications does the choice of formal logic have. I would hate to have the community split because one part “liked” to express rules using rest periods directly and others preferred properties of activities or duties. I wanted to be able to reason about the implications more objectively.

## Transcription points to confirm

- Page 1 has a small overwritten or inserted word above “the” in “the natural language specification.” It is marked rather than guessed.
- Page 4's “rule set” could also read “rule ref”; the word is marked `[set?]`. The theorem is unfinished after “fulfills.” Nothing has been added to complete it.
- Page 5's “solution space” reaches the right edge of the scan. The partly obscured final word is marked `[space?]`.
- “Introduce Luo et al.” is preserved as a drafting note. The manuscript does not identify a title or publication.
