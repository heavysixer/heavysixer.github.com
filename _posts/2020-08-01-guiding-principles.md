---
layout: post
title:  "Guiding Principles of Software Teams"
author: mark
categories: [ Software, Leadership ]
image: assets/images/guiding.jpg
featured: true
description: "Guiding Principles of Software Teams"
comments: false
---

What follows is a snapshot of a living document that my teams and I have crafted over several years. It is the guiding principles for writing high-quality software in a team environment. I am hopeful that it helps others as well.

## Guiding Principles

The following are a collection of interlocking beliefs, aspirations and working agreements that we as a team have aligned on. The goal of this document is to ensure our collaborations are positive and productive as we pursue quality in our technical products over the long term. These are not commandments to be adhered to in a dogmatic way. Rather they should be considered as a constellation of stars to use for wayfinding when trying to navigate the murky landscape of software development.

#### 1. You Can't Add Quality at the End
Quality software cannot be created at the end of the process, by backfilling tests, refactoring brittle code, or writing documentation. It needs to be reinforced at every step, with processes to measure, evaluate and improve the product along the way. We measure quality across the following dimensions:

* **Aesthetics** – Is the code well written from the standpoint of logical organization, coherence, visual cohesion, and efficient use of language?
* **Completeness** – Does the code solve the stated problem?
* **Performance** – Does the code perform against known benchmarks?
* **Effort** – What are the costs to write, test, document, maintain, and deploy this code?
* **Durability** – How reliable, scalable and long-lived is this code?
* **Reception** – How understandable is this code, does it build off existing best practices, and is it enjoyable to use?

#### 2. Write code for others
Write for humans not compilers. We strive to reduce the cognitive load others experience when reading our code. This goes beyond the typical advice of “prefer clarity over cleverness” or “write self-documenting code.” Writing collaborative code means focusing on:

* **Clarity** – Code with clarity uses direct and specific language to describe its intent to the reader. Meaningful variable and function names provide clarity. Comments can be used to provide crucial context for the reader which cannot be inferred from the code itself.
* **Completenes** - Units of code should focus on a singular idea or problem. Meandering and unfocused code which has multiple responsibilities causes the reader to wander through the codebase mentally compiling the program as they go to understand what it does. Completeness means that the code has been sufficiently documented, reviewed, and tested.
* **Cohesion** – Each unit of code should expand or enhance on the central idea but also be distinct and unique from other units of code. Weed out duplication, repetitiveness and instead look for patterns and abstractions which can effectively drive the code forward.
* **Coherence** – Units of code should be logically and lexicographically organized. Code which is connected should be grouped together where possible. Repeated code is a sign of low cohesion.
* **Correctness** – Code should free of stylistic and compiler errors. It should respect the project’s style guides and pass static program analysis (e.g. linting, complexity scoring) and adhere to industry best practices.

#### 3. Expect & Enforce Empathy
The act of writing software like all forms of self-expression can make oneself vulnerable to being wounded by others. Many programmers link their output to their own intrinsic self-esteem. As a team we agree to positively reinforce one another, nurture, celebrate successes and help each other grow as developers and as people. Teams without empathy can lead to members from experiencing a self-imposed shame in not knowing something. This can make team members guarded and tamp down communication. In the long run this insecurity can make teams less innovative and effective. We ensure our teammates are mentally and emotionally supported in the following ways:

* **Feedback is a Gift** – Provided a teammate is interested in feedback ensure it is fact-based, growth focused, and actionable.
* **Psychological Safety** – No one will be punished or humiliated for speaking up with ideas, questions, concerns or mistakes.
* **Observations and Evaluations** – When reviewing a team member’s work make it clear to the reader when you are providing feedback which requires rework (evaluation) or simply providing an opinion or insight (observation.)
* **Obligation to dissent** – Anyone regardless of role or seniority is capable to disagree with any other team member.

#### 4. Succeed and Fail Together
Solving hard problems with emerging technologies under tight deadlines means there is a risk in failure. While failure is a possible outcome we should not be surprised if it occurs. To ensure we fail together or succeed together we agree to the following norms:

* **Externalize & Enumerate Risks** – At regular intervals enumerate the risks to success. We will rank and size the risks as well as determine the potential upsides, downsides and long-term ramifications.
* **Trust Through Transparency** – Measuring velocity, having retrospectives, and providing stakeholder feedback is all in service of being as transparent as possible about the state of the product at any given time. We agree to also be transparent with each other when we a struggling with a task. We should not wait until the end of the sprint to announce we were unable to complete our work. Instead you should recruit and solicit help from teammates or leadership to help get you the resources and help you need.

#### 5. Technical Debt is ok, Defaulting is not

Technical debt is fundamentally misunderstood in the product development community.

Technical debt like stakeholder investments is a means to fund software development. Unlike external capital sources, technical debt is an instant (albeit finite) funding stream the development team controls. When used correctly, technical debt allows software teams to borrow from their future productivity to fund aspects of the product. Good technical debt is self-liquidating, meaning it creates value greater than the initial investment and overhead (e.g. interest) incurred to fund the loan.

Teams run into trouble when they take on technical debt in secret to hide poor planning, misunderstandings, or wasted effort. Economist John Stuart Mill could have been writing about the poor use technical debt [when he wrote](https://archive.org/stream/oncreditcyclesor00manc/oncreditcyclesor00manc_djvu.txt),  “Panics do not destroy capital; they merely reveal the extent to which it has been previously destroyed by its betrayal into hopelessly unproductive works.”
