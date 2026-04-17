---
layout: post
title: The Trade-off Between Breadth and Depth in Literature Reviews
date: 2026-03-16 11:12:00-0400
description: A personal reflection on the limitations of a strictly systematic approach when the literature is too broad to meaningfully constrain.
images:
  compare: true
  slider: true
---

Hello dear readers,

I am writing this article to document the process of creating my first revision article. So far I have encountered quite a few
struggles. For those unacquainted with my Ph.D. theme, this article will explore the use of tactile sensing in physical
human-robot(pHRI) interactions. Up until late 2010s early 2020s, robot systems relied heavily on vision systems for control. While
vision is great for broader perception, it lacks the precision required for finer manipulation. In the past five years, tactile sensing
has become a trend within the field. With tactile sensing emerging in robots, the field of dexterous robotic manipulation rose similarly.

Initially, I chose to follow the PRISMA framework to guide my article. The PRISMA framework is a conceptual process recommended for
elaborating systematic literature reviews.

{% include figure.liquid 
    loading="eager"
    path="assets/img/narrowing_review/prisma.avif"
    class="img-fluid rounded z-depth-1 text-center"
    zoomable=true
    width="800"
    caption="PRISMA Framework"
%}

By following these steps, it allows the reader to recreate the search should he choose to. I found PRISMA to favor broader reviews, as limiting a review to the results
of a few search equations can often make such equations very broad in nature, to encompass all topics of interest.

I chose to use these 3 databases for my search, with the following search equations:

### Scopus

```vb 
TITLE-ABS-KEY ( "contact" OR "contact-rich" AND ( "physical human-robot interaction" OR "pHRI" ) )
AND PUBYEAR > 2019 AND PUBYEAR < 2027
```

### IEEExplore

```vb
("contact" OR "contact-rich")
AND ("physical human-robot interaction" OR "pHRI")
```

### Web of Knowledge

```vb
TS=(
  ("contact" OR "contact-rich")
  AND
  ("physical human-robot interaction" OR "pHRI")
)
AND PY=(2020-2026)
```

## In practice

I used *Rayyan*, a web-based software which manages most of the trivial but cumbersome little tasks. It has built in tools to compare and delete duplicate papers from
different databases, tools to do both a initial and full text screening. The premium version unlocks some AI tools to expedite some parts of the process, but it is by no
means necessary. It also supports collaboration with other people and a blind mode, so that one's screening isn't biased by another.

### Stats

##### Initial number of articles - 467

##### After removing duplicates - 225

##### After abstract screening - 77 included, 43 maybe, 105 excluded

---


My struggles started with the sheer number of papers to go through. Reading 225 abstracts to figure out whether the papers belonged or not was very time-consuming. Out of
those 225, I excluded 105, and was unsure about 43. There were too many articles without interest due to my broad search equation, which at times killed my motivation to
keep screening, as I felt like I was getting nowhere. Nevertheless, I persevered and in the end grouped the main thematics
from the articles found with at least a slight relevance into the following categories:

- Safety:
    - Impact thresholds;
    - Force control;
- Perception:
    - Tactile Sensing methodologies:
        - Robotic skins;
        - Sensorless Force Estimation;
        - Robotic Fingertips;
    - Haptic Gestures;
    - User Intent Prediction;
    - Deliberate Touch vs Accidental Collision;
- Planning
- Control:
  - Traditional control:
    - Impedance Control;
    - Admittance Control;
  - **Deep learning;**
  - **Reinforcement Learning:**
    - **Meta Learning;**
  - **Learning from demonstration;**

# 1st Problem - Underrepresentation of Core Research Themes

Among the identified thematics, those highlighted in bold represent the areas I intend to explore in greater depth within this article. I would also be open to incorporating
some discussion on *Perception*. However, a clear imbalance emerges: the themes of primary interest account for only around 10 of the 77 selected papers. In contrast, the
majority of the literature is centered on traditional control approaches, which fall outside the scope of my interests.

A non-negligible portion of the works also addresses *Safety*, a topic I would consider including to a limited extent. Additionally, *Perception* appears as a recurring theme
across many papers. When focusing specifically on *Reinforcement Learning*, the situation becomes more dire, with
only two relevant papers identified -- an insufficient number given its central role in my Ph.D. research.

Ultimately, the primary motivation behind conducting this study is to develop a solid understanding of the state of the art relevant to my doctoral work; the resulting paper
is, in many ways, a secondary outcome of this process. Considering the title of my Ph.D. -- *Dream-Augmented Meta Reinforcement Learning for Contact-rich Human-Robot
Manipulation* -- the core thematics guiding this effort can be outlined as follows:

- Meta Reinforcement Learning;
- Model-based Reinforcement Learning:
    - Predictive Models (Dreamer - Partial Latent Representations of Future states);
- Contact-rich Human-Robot Manipulation;

Out of the three topics, the systematic screening leans heavily toward contact-rich human-robot manipulation, only scratches the surface of meta reinforcement learning, and
does not address model-based reinforcement learning at all.


# 2nd Problem - There are already good broad review papers

A central drawback of pursuing a broad review paper is that the broader the scope, the higher the likelihood that similar work has already been published. In fact, this was
confirmed during the literature search, where more than one strong review paper on the topic emerged. One notable example is “*The path towards contact-based physical
human–robot interaction*” ([DOI](https://doi.org/10.1016/j.robot.2024.104829)).

This work adopts a very broad perspective, covering most of the thematics identified through the search equation. However, its breadth is also its main limitation. The
discussion remains largely superficial—for instance, *Reinforcement Learning* is condensed into barely a single paragraph. Despite citing nearly 300 papers, the review offers
limited critical analysis, functioning more as a general introduction to the field of physical human–robot interaction (*pHRI*) than as an in-depth examination of specific
research directions.

From a practical standpoint, this also highlights a key constraint. Covering such a vast number of references in a meaningful way is not feasible within a reasonable
timeframe for a single researcher. Reviews of this scale are typically the result of coordinated efforts by larger research teams, whereas this work is being conducted individually. As such,
attempting to replicate that level of breadth would be unfeasible.

# Plan moving forward

Building on this limitation, the approach I aim to adopt aligns more closely with that of a narrative review. While systematic reviews rely on rigorous, predefined
methodologies to comprehensively search and analyze evidence—often with the goal of minimizing bias and supporting high-level decision-making—such an approach may not be the
most suitable for the present objectives. In contrast, narrative reviews provide a more flexible framework, allowing for broader exploration and synthesis guided by expert
interpretation, albeit at the cost of increased subjectivity and the absence of standardized procedures.

This flexibility is particularly valuable in the current context. It enables the inclusion of relevant works identified through more niche search queries, as well as papers
discovered organically during the review process itself. Rather than being constrained by strict inclusion criteria, this approach allows the literature to be shaped by
relevance and insight.

Moreover, a stronger emphasis on critical analysis naturally limits the total number of citations. This trade-off is not only intentional but also practical: conducting a
deeply analytical review of a smaller, carefully selected body of work is considerably more feasible for an individual researcher. In this sense, prioritizing depth over
breadth is not merely a constraint, but a deliberate methodological choice aligned with the overarching goals of this study.
