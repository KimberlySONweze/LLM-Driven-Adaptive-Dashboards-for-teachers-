# LLM-Driven Adaptive Dashboard for Teachers

A Streamlit dashboard in which a large language model decides which chart best explains a classroom group discussion, then tells the teacher why.

 **Paper:** Nweze, K. and Bulathwela, S. (2026). *Towards Adaptive Visualisation of Collaborative Learning Patterns: An LLM-Based Approach.* First-author paper accepted at the CROSSMMLA Workshop, Festival of Learning 2026. 

## The problem

Teacher dashboards usually show the same charts for every group discussion, whatever actually happened in it. That leaves teachers to work out which view matters, in the middle of a lesson. This project treats choosing the visualisation as a reasoning task: the system reads what happened in an episode and picks the chart that best shows it.

## How it works

1. **Feature extraction.** For each discussion episode, a pipeline computes 23 features in four groups:
   - temporal: duration, turn count, response latency
   - participation: speaker count, speaking time per person, Gini coefficient
   - conversational: question density, utterance length
   - challenge and regulation rates
2. **Selection.** Llama 3.3 70B (via the Groq API) receives the features plus a pedagogical selection framework, grounded in Socially Shared Regulation of Learning theory, and chooses one of five templates.
3. **Explanation.** The model writes a short annotation explaining its choice and what it means for the teacher.
4. **Display.** Everything is shown in a three-page Streamlit dashboard.

| Template | Chosen when… |
|---|---|
| Timeline | the order of challenges and regulation over time matters most |
| Participation Chart | one or two speakers dominate (high Gini) |
| Network Graph | the shape of turn-taking matters, e.g. whether talk runs through one person |
| Stacked Bar | challenge and regulation are both present and changing across the episode |
| Heatmap | it matters who is carrying the cognitive and regulatory load |

The data comes from 28 real postgraduate collaboration sessions: **10,799 utterances across 882 discussion episodes**.

## Evaluation

Three raters independently scored 20 stratified episodes from 0 to 2:

| Criterion | Mean score | Agreement (Fleiss' κ) |
|---|---|---|
| Visualisation appropriateness | **1.55 / 2** | 0.713 |
| Reasoning accuracy | 1.50 / 2 | 0.732 |
| Annotation usefulness | 1.15 / 2 | 0.625 |

Agreement between raters was substantial on every criterion. I also compared the LLM against a rule-based baseline using the same framework. They agreed on 45% of episodes, and some of the LLM's divergent choices were rated fully appropriate.

## What went wrong, and what it taught me

The failure analysis was as useful as the results. I identified three distinct failure types:
- **Threshold hallucination:** the model chose a chart whose conditions weren't met, then wrote reasoning claiming they were.
- **Borderline numerical errors:** it treated 0.087 as above a 0.1 threshold, even with explicit instructions not to round.
- **Framework design failure:** the rules were applied correctly but produced an uninformative chart.

The first two point to a known LLM weakness with numbers. Passing threshold checks to deterministic code is the natural next step. Annotations were rarely wrong but often too descriptive to act on, which is the main area for improvement.

## Repository contents

- `Dashboard.py`: Streamlit dashboard
- `Feature_Extraction`: computes the 23 episode features
- `Visualisation.py`: generate all visualisations
- `Adaptive Visualisation of collaborative learning`: full workshop accepted paper 

The classroom discussion dataset is not included because it is not publicly shareable.


## Tech stack

Python · Streamlit · Llama 3.3 70B (Groq API) · pandas · data visualisation

---
Kimberly Nweze · [Portfolio](https://kimberlysonweze.github.io/kimberlynweze.github.io/) · MSc AI for Sustainable Development, UCL
