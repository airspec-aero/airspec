# Answers to the AirSpec draft questions

These are research and suggested additions, separate from the transcription. They describe what a suitably defined AirSpec could support, not capabilities verified in its current implementation. The draft does not include AirSpec's grammar, full semantics, or implementation.

## The main distinction

Typed variables and decision tables can be the notation people use to write a specification. Formal logic can give that notation a precise meaning. SAT/SMT solvers and proof assistants can then analyze that meaning. These are complementary layers, rather than competing ways to describe a rule.

The important limit is that a proof establishes a formal statement under stated assumptions. It does not, by itself, establish that the statement captures the intended operational requirement. Expert review, examples, and formal analysis all have a role. [Lean's guidance on validating proofs](https://lean-lang.org/doc/reference/latest/ValidatingProofs/)

## R1 Add more examples

The following are useful questions to ask about a scheduling specification. They are proposed checks, not claims about existing AirSpec features.

1. **Can the rules be satisfied together?** Is there any admissible schedule satisfying every mandatory constraint? A failure may concern one particular scheduling instance, not every possible use of the rules.
2. **Can an accepted schedule violate an intended invariant?** For example, can a trip pass the rules while a required connection is shorter than its minimum connection time?
3. **Does a decision table cover every intended input?** Is there an admissible combination of inputs that matches no row? Coverage is relative to the input domain and any defined default behavior.
4. **Can rows overlap or disagree?** Can two rows match the same input, and can they return incompatible results? Overlap is not necessarily a defect: its meaning depends on whether the table uses unique, first-match, priority, or another hit policy. [OMG DMN decision-table semantics](https://www.omg.org/spec/DMN/1.4/PDF)
5. **Did a refactoring preserve meaning?** Can the old and new specifications classify the same schedule differently? This can detect an unintended effect of changing a shared definition.
6. **Does an exception have any effect?** Can its condition ever be reached, and can it change the outcome? An exception may be unreachable or shadowed by an earlier rule.
7. **Does each rule actually restrict the allowed schedules?** For conjunctive constraints, can a schedule satisfy every other rule while violating this one? If not, this rule is redundant relative to the others and the domain assumptions. This definition needs adapting for ordered or aggregating tables.
8. **Is the new version more restrictive?** Does every schedule accepted by the new version also pass the old one? To show it is *strictly* more restrictive, also find a schedule accepted by the old version and rejected by the new one.

These checks follow the standard strategy of asking whether a counterexample exists. A satisfying assignment can supply a concrete witness; unsatisfiability rules out such a witness for the encoded problem. [Z3's explanation of validity through unsatisfiability](https://microsoft.github.io/z3guide/docs/logic/propositional-logic/)

### Precise forms of the draft's two existing examples

Let `D(s)` mean that `s` is an admissible schedule under the shared domain assumptions. Suppose `R1(s), ..., Rn(s)` are mandatory constraints combined with AND.

- **Rule i is nonredundant:** find a schedule satisfying `D(s) AND every other Rj(s) AND NOT Ri(s)`. Such a witness demonstrates that removing rule i admits something previously forbidden. To establish that *no* rule is redundant, perform this check for every rule.
- **New is no less restrictive than old:** show that `D(s) AND New(s) AND NOT Old(s)` is unsatisfiable.
- **New is strictly more restrictive:** additionally show that `D(s) AND Old(s) AND NOT New(s)` is satisfiable.

Keep the domain and unchanged rules the same when comparing versions. Also check that the full rule set is satisfiable: an inconsistent specification can imply every property vacuously. A rule need not be deleted merely because it is logically redundant; it may still be valuable for explanation or traceability.

**Suggested addition to the blog:**

> We can ask whether the rules can be satisfied together, whether a decision table leaves any cases uncovered, or whether two matching rows disagree. We can check that a refactoring preserves the allowed schedules, and identify exactly which schedules a new rule excludes. When a property fails, a solver can often give us a concrete example to discuss with a domain expert.

## R2 Does formal analysis depend on what AirSpec allows us to express

**Yes, with a qualification:** expressive power is only one part of the answer. Separate four issues:

- **Expressiveness:** Can we describe the relevant rules and properties accurately?
- **Semantics and translation:** Does the analysis preserve their meaning?
- **Decidability:** Is there an algorithm guaranteed to answer every question in the selected logical fragment?
- **Practical cost:** Can it answer this instance within the available time and memory?

Adding features can make a language more expressive while making complete automatic analysis harder or impossible in general. Restricting a language can preserve useful capabilities while making analysis more predictable. For example, many timing constraints are differences between time points, a useful restricted arithmetic form discussed in Z3's scheduling examples. [Z3 arithmetic documentation](https://microsoft.github.io/z3guide/docs/theories/Arithmetic/)

That does not mean a property outside one solver's supported fragment cannot be proved. It may need a different encoding, explicit bounds, a stronger tool, or a manually guided proof. Even a decidable problem can be expensive.

**Suggested replacement for the sentence:**

> What we can verify depends on what AirSpec can express, the meaning we assign to those expressions, and the analysis tools we use. Some questions can be answered automatically for the full modeled domain. Others require explicit bounds, additional assumptions, or a separately constructed proof.

## R3 Are typed variables and decision tables reducible to relational logic

**Often yes, once their semantics are specified.** Types can become domains or membership predicates. Relationships become relations. A function can be represented by a relation together with constraints requiring a unique output for each valid input. Alloy is an established example of a specification language built around relations, including representations of sets, scalars, and functions. [Alloy language specification](https://alloytools.org/spec.html)

For a simple decision table, a row's condition is the conjunction of its input tests. Its result can be represented as a constraint or conditional expression. But encoding every row as an independent implication is not correct for every kind of table. In a first-match table, for example, row 2 applies only when it matches **and row 1 does not**. Defaults, null or missing values, priorities, multiple outputs, and aggregation also need precise definitions. DMN makes these distinctions explicit through its table semantics and hit policies. [OMG DMN standard](https://www.omg.org/spec/DMN/1.4/PDF)

Arithmetic and time operations must retain their meaning. Treating `lessThan(x,y)` as an arbitrary relation does not, by itself, give it the semantics of numerical comparison. Similarly, arbitrary functions or recursive expressions do not become easy to analyze just because they appear inside a typed table.

**Suggested wording:**

> Typed variables and decision tables give us a structured starting point for a logical model. We can translate their conditions, relationships, and outputs into constraints, provided we also define details such as precedence, missing values, and arithmetic. The table is the notation; its logical interpretation is what makes formal analysis possible.

Whether this translation works for *all of AirSpec* needs a review of AirSpec's actual language definition. The draft alone cannot establish that.

## R4 What can SAT and SMT express

### SAT

Boolean satisfiability asks whether a **finite Boolean formula** has a satisfying assignment. A variable might mean “crew C is assigned to flight F.” Boolean constraints can require exactly one assignment or prohibit incompatible assignments.

Finite-domain numbers and relations can also be encoded using Boolean variables, although the resulting formula may be large. Alloy's analyzer illustrates this by translating relational models with finite object scopes into Boolean formulas. [How the Alloy Analyzer works](https://alloytools.org/faq/how_does_the_alloy_analyzer_work.html)

### SMT

Satisfiability modulo theories keeps the Boolean structure and adds theories such as integer and real arithmetic, fixed-width bit-vectors, arrays, and uninterpreted functions. A connection constraint can then be written directly as `departure >= arrival + minimumConnectionTime`.

SMT is a family of logical fragments, not one equally solvable language. SMT-LIB distinguishes, for example, quantifier-free linear integer arithmetic from other combinations of theories and quantifiers. [SMT-LIB logic catalog](https://smt-lib.org/logics.shtml)

Important qualifications:

- SMT is **not inherently bounded**. Mathematical integers and reals can range over infinite domains.
- Quantifiers do not automatically imply undecidability. Finite quantification can be expanded, and some quantified theories are decidable. General quantified reasoning can nevertheless require incomplete methods. [Z3 quantifier documentation](https://microsoft.github.io/z3guide/docs/logic/Quantifiers/)
- Quantifier-free does not automatically mean decidable: general nonlinear integer arithmetic is already undecidable. [Z3 arithmetic documentation](https://microsoft.github.io/z3guide/docs/theories/Arithmetic/)
- A solver can return `unknown`, or run out of resources. Neither outcome proves or disproves the property. [Z3 basic commands](https://microsoft.github.io/z3guide/docs/logic/basiccommands/)

### How a solver establishes a property

To check that assumptions `A` imply a property `P`, ask whether `A AND NOT P` is satisfiable:

- `sat`: a counterexample exists in the encoded model.
- `unsat`: the property follows from those assumptions in the encoded domain.
- `unknown`: no conclusion.

A universally stated claim can sometimes be checked without an explicit universal quantifier in the query: the solver searches for one violating assignment. [Z3 propositional logic documentation](https://microsoft.github.io/z3guide/docs/logic/propositional-logic/)

When an analysis uses bounds, the result must state them. No counterexample among schedules containing at most ten flights does not by itself prove a claim about schedules of arbitrary size. If the bound covers the entire intended domain, the result can be complete for that domain. [Alloy scope semantics](https://alloytools.org/spec.html)

## R5 Where Lean and proof assistants fit

Lean is useful when we want to construct and check a mathematical proof, including a theorem about arbitrarily long schedules using induction. It could also help prove that an AirSpec-to-logic translation preserves meaning. These are potential applications, not verified AirSpec features. [Theorem Proving in Lean on induction and recursion](https://lean-lang.org/theorem_proving_in_lean4/Induction-and-Recursion/)

Lean is not an automatic oracle for every proposition. Proofs may require definitions, intermediate lemmas, induction, and human guidance. Tactics help construct proof terms that Lean's kernel checks. [Lean tactic proofs](https://lean-lang.org/doc/reference/latest/Tactic-Proofs/)

Even a checked proof must be interpreted relative to its definitions and assumptions. Unfinished proofs and additional axioms need scrutiny, and a correct theorem may still formalize the wrong requirement. [Lean proof-validation guidance](https://lean-lang.org/doc/reference/latest/ValidatingProofs/)

**Practical recommendation:** start with a precisely defined subset of AirSpec and a few valuable SAT/SMT checks. Consider Lean where a general theorem, an inductive argument, or confidence in the translator justifies the additional work. SAT/SMT and proof assistants can complement one another.

**Suggested wording:**

> SAT and SMT solvers can automate many useful checks. Proof assistants such as Lean offer another route when we need general mathematical proofs or guarantees about the translation itself. In every case, the result concerns the formal statement we supplied. Connecting that statement to the intended operational rule remains essential.

## A concrete minimum connection time example

This is an illustrative model, not an actual airline policy or AirSpec syntax. Assume arrival and departure are integer minutes on one consistent timeline, and that the relevant connection pairs have already been identified. Real rules may depend on airport, terminal, flight type, exceptions, and other context.

```text
ConnectionLegal_m(arrival, departure) := departure - arrival >= m
```

Compare a 30-minute minimum with a 40-minute minimum:

```text
For all arrival and departure times:
    ConnectionLegal_40(arrival, departure)
    implies ConnectionLegal_30(arrival, departure)
```

Search for a counterexample:

```text
departure - arrival >= 40
AND
departure - arrival < 30
```

These constraints are unsatisfiable, so the implication holds for this arithmetic model. This is a statement about all integer-valued arrival and departure times, not just a few example times.

A 35-minute connection satisfies the old rule and fails the new one. If that connection is admissible under the other domain assumptions, it demonstrates that the new rule is **strictly** more restrictive.

For a whole trip, a possible definition is:

```text
Legal_m(trip) :=
    WellFormed(trip)
    AND every required connection in trip satisfies ConnectionLegal_m
```

This is a **definition**, not yet a theorem. A theorem could then state that `Legal_40(trip)` implies `Legal_30(trip)`, using the same well-formedness conditions and connection relation. Note also that connection rules concern pairs of flights; testing each flight individually is not sufficient.

## Other passages worth tightening before publication

1. **“A spec does nothing, so there is no expected behaviour.”** A specification may have no execution engine, but a formal semantics can still define whether a trip is accepted or what result a table should produce. Example-based validation is meaningful. The remaining issue is whether the evaluator and the formalization faithfully capture the intended rules.
2. **“Automatically prove our theorem holds.”** Prefer “automatically check selected properties” unless the domain, assumptions, and completeness of the method justify the stronger statement. A bounded result must not be presented as an unbounded theorem.
3. **“Without building a rule engine.”** The useful claim is that analysis need not require a production rule engine or scheduling optimizer. It still needs an implementation of the specification's semantics, such as a translator to logic, and that translator can contain errors.
4. **“Without having to produce a solution.”** More precisely, without producing a full optimized operational schedule. Formal analysis may still produce witness schedules or counterexamples internally.
5. **The unfinished theorem on page 4.** Complete the definition first, including the domain, relevant connections, and exceptions. Then state a separate property that is not merely a restatement of that definition.
6. **“Introduce Luo et al.”** The manuscript does not identify the work. Add its title or link before relying on it; no paper has been guessed here.

### Source for the Dijkstra placeholder

> “Program testing can be used to show the presence of bugs, but never to show their absence!”

Edsger W. Dijkstra, *Notes on Structured Programming*, EWD249, manuscript page 7. [Original manuscript in the Dijkstra Archive](https://www.cs.utexas.edu/~EWD/ewd02xx/EWD249.PDF)

Use the quotation as motivation for stronger reasoning, without implying that formal verification eliminates mistakes in the chosen model, assumptions, or translation.
