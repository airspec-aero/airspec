# AirSpec
An open-source, community-driven specification for crew compliance and payroll rules for aviation.

### The idea
Create an open, permissively licensed and vendor-neutral way to describe airline crew rules, their meaning and their relationships.

The first goal is deliberately small: do enough to explain the idea publicly, demonstrate why it matters and invite others to help shape it.

## The problem

Airlines and vendors spend an enormous amount of time gathering, interpreting and implementing crew rules. This makes implementation and migration projects expensive, slow and risky.

One recent crew tracking project estimated tens of thousands of programming hours for rule modelling alone, plus thousands of hours of internal airline effort to gather the requirements. This was despite the airline already having an experienced team and an existing implementation of the rules in another system using the same underlying language.

The problem is therefore larger than translating policy into code. The meaning of the rules is trapped inside documents, local knowledge and vendor-specific implementations. It cannot be moved, reviewed or reused easily.

This has wider consequences:

- Airlines become dependent on particular vendors and a small number of specialists.
- Moving to a new system becomes unusually costly and risky.
- New vendors face a high barrier to entry.
- Existing implementations remain on older technology for longer.
- The same interpretation and modelling work is repeated across the industry.

## What we are proposing

We are proposing a shared model of the concepts involved in airline crew rules and the relationships between them. The model should be:

- **Vendor-neutral:** it describes what a rule means without depending on one vendor's code or product.
- **Open:** anyone can inspect, use and contribute to it.
- **Permissively licensed:** airlines, vendors, consultants and open-source projects can build on it.
- **Machine-readable:** software and AI agents can use it as a common foundation.
- **Understandable by domain experts:** rule meaning and changes can be reviewed without first reading vendor-specific code.
- **Traceable:** a modelled rule can retain links to its source, interpretation and eventual implementation.

The spec would sit between source material and system-specific implementation:

> Regulation, agreement or airline policy → vendor-neutral rule model → human verification → vendor-specific implementation

It could also provide a path in the other direction:

> Existing vendor implementation → vendor-neutral rule model → review, comparison, visualisation or migration

## What it could enable

The shared layer could eventually support tools and agents that:

- turn rule documents into a structured rule model;
- map an existing RAVE or other vendor implementation into the model;
- make rule interpretations and dependencies visible to non-technical stakeholders;
- show the effect and scope of a proposed rule change before code is changed;
- compare rules or interpretations across systems, fleets, bases or agreements;
- generate or assist with vendor-specific implementations;
- provide visualisations of complex rules and their relationships; and
- preserve knowledge when people, suppliers or systems change.

## Why now

Software development is changing quickly, and vendors and airlines are starting to build AI agents to help interpret and implement rules. Each tool currently risks creating another format tied to one system.

There is an opportunity to establish a common, vendor-neutral foundation before those approaches become more fragmented. If the foundation succeeds, airlines could change systems with less repeated work, while vendors could deliver useful functionality faster and at lower cost.
