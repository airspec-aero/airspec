---
title: From crew rules to formal methods
description: How AirSpec is related to ontology and formal methods and the need for guiding principles.
author: Viktor Forsman
math: true
---
## Rest Periods

The beauty of working in open source is that you get inspiration from people with different backgrounds who look at the problem in a different way than you and your colleagues are used to. In one of our early discussions, the idea of treating rest periods as first-class citizens of a spec was brought up. Having a rest period “fail” a rule, rather than a duty, was for me, a completely new way of thinking about rules. I thought it was a really cool approach and could potentially eliminate common classes of bugs that occur when one expresses a rest rule using properties of duties.

But then I immediately started to struggle with the question of how to make that choice. Should we allow rest periods as first-class citizens in our spec, or only reason about them as properties of duties (e.g., `duty.rest_before` and `duty.rest_after`)? Is this just a matter of taste and personal preference? If so, we may end up in endless arguments or discussions about the “best” or “most natural way” of describing things. It would be preferable to have clear design goals for the spec that would then guide these decisions.

## What really is AirSpec?

Before we start thinking about the design goals of the spec, we need to break down the specification into its components. One concept that came up early in our discussions was ontology. An ontology maps out entities and their relations in a domain. This concept is clearly related to the ideas behind AirSpec and the problem it intends to solve. Having a clearly defined and mapped-out ontology for our crew rules would give us a lot of benefits. Having the entities and concepts for crew rules clearly mapped out would really help in discussions about rules between different departments. The training department may have a slightly different intuition about what a duty is than the scheduling department, which could lead to miscommunications during rule design and implementation. Having the duty concept described in an ontology could help the departments to speak the same language. Using ontologies in business and software design is not new, but they are enjoying a revival with the rise of AI agents. I’m seeing much more discussion about how they can help agents understand the concepts and relationships in a domain.

But AirSpec is more than just an ontology. We also define what can be said about the entities described in the ontology and exactly what those statements mean. Should we allow general conditions involving the entities in our ontology—for example, requiring every duty to satisfy a condition, written as $$\forall d,\; p(d)$$? Or should we restrict rules to specific forms, such as requiring the rest time for every duty to meet a minimum: $$\forall d,\; d.\mathrm{rest\_time} \geq \mathrm{limit}$$? And if we are using decision tables, exactly how should those be interpreted? By answering these questions we're defining the underlying logic behind AirSpec. We'll get back to the implications of these choices in the next section.

Finally, AirSpec also includes a specific notation (which we have defined as typed variables and decision tables). The notation must be easily machine-readable and able to express our ontology and our logic.

Now let’s go back to the concept of rest periods and whether we should include them in our ontology or not. If we were to include rest periods as first-class citizens of AirSpec, they would have to be defined in the ontology as entities rather than calculated properties of a duty. We would also have to describe how rest periods connect consecutive duties. A minimum rest rule could then be described as being required to hold for all duties and their rest periods before or after, or as being required to hold for all rest periods. By extending the ontology in this way, we would allow some rules to be expressed more naturally using rest periods directly rather than through properties of duties, but we would also increase the number of ways that we can express a rule in our spec.

When these goals pull in different directions, which should be the stronger guiding principle in our design? That there should be one obvious way of expressing our rules,[^python] or that the specification should let us express rules in the most natural way? This is not an easy question to answer, but needs to be answered to guide the decisions on whether to extend the ontology or not.

## Logic and formal methods

Let’s say we have two different formulations of a minimum rest rule: one using rest periods as the main entity and the other using duties and their properties. How can we guarantee that they represent the same rule? In a “normal” software system, we use regression and unit tests to check that the system’s behavior is the same after a refactoring (reformulation) of a rule. But how do we do this with a specification that isn’t itself an executable system? We could build a tool that implements the AirSpec format and checks trips or rosters, and basically do the same thing that we do for a system implementation. But now our assessment of the spec also depends on whether the tool interprets it correctly. And if two different people build two different tools implementing AirSpec, they may behave differently.

Ideally, we want to reason about the spec without building a rule engine. What other options do we have? We can pull out semantic pieces and translate them into something more understandable, such as flowchart visualizations. This can be a powerful method of working with the spec and can give experts assurance that the rewritten rule is equivalent to the original. Additionally, one can use an AI agent to convert the spec back into natural language to support the experts’ assessment. While these can be very useful tools for reasoning about the rules in the spec, they will never give us absolute certainty. Flowcharts may look different, and two AI agents can come up with two different formulations. Additionally, these methods require manual work.

Crew rules such as minimum rest can be expressed as logical conditions on scheduled activities and their relationships. And the beauty of logic is that it is an exact science in which conclusions can be derived formally. This, if done right, can be incredibly powerful and open up a whole world of possibilities.

Let’s say we have two different formulations of a rule in AirSpec, $$t_1$$ and $$t_2$$, and we have a way of translating the formulations into a formal logical language. Then we could express the equivalence we want to establish as a logical statement:

$$
\forall t \in T,\quad t_1(t) \iff t_2(t).
$$

Here, $$T$$ is the domain of trips we are considering, and $$t_1(t)$$ and $$t_2(t)$$ mean that trip $$t$$ satisfies each formulation. The statement says that every trip satisfies either both formulations or neither.

We can then use a formal method, such as a SAT or SMT solver, to try to prove this or find a counterexample,[^1] or use a proof assistant such as [Lean](https://lean-lang.org/).

This makes getting the definitions right even more important: they become the foundation for what we can prove about our rules.

This enables us to create a “regression test suite” for our spec, i.e., a set of schedules/trips that must always be legal or fail, which is a necessity when updating shared definitions.

It also enables us to express and prove statements such as:

> “There exists no superfluous rule in the system.”

That is, each rule excludes something the others permit.

Or:

> “This version of a rule is more restrictive than the previous version.”

That is, it allows no previously forbidden schedules while excluding some previously allowed ones.

Etc., etc.

## The Zen of AirSpec

Getting the foundations right is not easy, and the choices we make will have big implications for the applicability and expressive power of the spec. Different rule documents describe rules differently and use a vast range of concepts, so describing an ontology that can serve as a baseline for all cases is quite the challenge. Being able to reason about rules and the schedules they entail is incredibly powerful, but mathematical certainty is often very difficult to achieve, and it mustn’t get in the way of making the spec practical to use.

Making our design goals and guiding principles explicit will help us make better choices for AirSpec. These principles will develop as we explore concrete rules, compare approaches, and discover where the trade-offs lie. I’d like us to capture and revisit them as we go, so each design discussion helps inform the next.

Alongside this, I want to explore which underlying logical systems are suitable for crew rules and what each would allow us to express and prove.

[^1]: The [Z3 guide](https://microsoft.github.io/z3guide/docs/logic/propositional-logic/) explains checking a claim by searching for a counterexample. Some analyses, such as [Alloy’s SAT-based analysis](https://alloytools.org/faq/how_does_the_alloy_analyzer_work.html), use bounded domains. [SMT arithmetic](https://microsoft.github.io/z3guide/docs/theories/Arithmetic/) can also range over mathematical integers and reals, so bounds are not always required. A bounded result applies within its bounds, and a solver may also return an inconclusive result.

[^python]: Tim Peters, [The Zen of Python (PEP 20)](https://peps.python.org/pep-0020/), includes the principle of one obvious way to do something.
