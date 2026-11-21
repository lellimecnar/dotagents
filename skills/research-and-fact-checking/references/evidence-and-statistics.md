# Evidence hierarchies and reading statistics

## GRADE: certainty of a body of evidence
Cochrane uses GRADE. Four levels of certainty per outcome: high, moderate, low, very low. Five domains
rate down: risk of bias, inconsistency, indirectness, imprecision, publication bias. Three further
domains can rate non-randomised evidence up. [1]
| Level | Meaning (GRADE wording) [2] |
|---|---|
| High | Very confident the true effect lies close to the estimate |
| Moderate | Moderately confident; true effect likely close, but possibly substantially different |
| Low | Confidence limited; true effect may be substantially different |
| Very low | Very little confidence; true effect likely substantially different |

Starting points and moves:
- Randomised trials start high; non-randomised studies traditionally start low (they start high only if
  assessed with a tool like ROBINS-I that handles confounding). [3]
- Upgrade criteria for non-randomised evidence: strength of association, dose-response, plausible
  residual confounding that would oppose the effect. RCTs are typically not upgraded. Evidence already
  rated down is usually not rated back up. [3]
- Certainty cannot fall below very low however many problems there are. [4]
- Cochrane's large-effect thresholds: report whether the relative effect is >2 or >5. [1]
- Imprecision: Cochrane mentions a rule of thumb of about 400 events as an optimal information size, and
  says to check whether the confidence interval includes both little/no effect and important benefit or
  harm; avoid reporting results as "statistically significant" or not. [1]
- Summary tables should give both relative and absolute effects, the baseline risk, and the number of
  participants and studies, for at most seven outcomes. [1]

Note on disagreement: NHMRC writes that randomised and non-randomised trials begin high and
observational studies low [4]; CDC's ACIP handbook says non-randomised studies start low unless assessed
with ROBINS-I [3]. Both agree uncontrolled observational evidence starts low. I follow the ACIP/Cochrane
phrasing because it states the exception explicitly.

## OCEBM 2011 levels: the design depends on the question
Systematic reviews are generally better than single studies. Level may go down for quality, imprecision,
indirectness, inconsistency or a very small absolute effect, and up for a large effect. [5]
| Question | Level 1 | Level 2 | Level 3 | Level 4 | Level 5 |
|---|---|---|---|---|---|
| How common is it? | Local, current random-sample survey or census | SR of surveys matchable locally | Local non-random sample | Case series | n/a |
| Is this test accurate? | SR of cross-sectional studies, consistent reference standard, blinding | Individual such study | Non-consecutive / inconsistent reference | Case-control, poor reference | Mechanism-based reasoning |
| Does it help? | SR of RCTs or n-of-1 trials | RCT or observational study with dramatic effect | Non-randomised controlled cohort | Case series, case-control, historical controls | Mechanism-based reasoning |
| Rare harms? | SR of RCTs or n-of-1 | RCT or (exceptionally) dramatic observational | Post-marketing cohort large enough | Case series, case-control | Mechanism-based reasoning |
| Is screening worthwhile? | SR of RCTs | RCT | Non-randomised controlled cohort | Case series, case-control | Mechanism-based reasoning |

Conflict: popular "evidence pyramids" (e.g. a university library guide listing guidelines, then
systematic reviews, RCTs, cohort, case reports, expert opinion) [6] present a fixed ladder. I follow GRADE
and OCEBM because they make design a starting point that quality can move, and because the best design
differs by question.

Plain-language ladder for household reports: systematic review of good trials > single good trial >
controlled observational study > uncontrolled observation/case reports > expert opinion or mechanism
("it should work because...") > testimonial.

## Statistics translation rules
| If the source gives | Translate to | Why |
|---|---|---|
| Relative risk reduction ("cuts risk 20%") | Absolute: from X in 1,000 to Y in 1,000, over a stated time | Gigerenzer et al.: mammography's "about 20%" relative reduction is about 1 in 1,000 (5 to 4 in 1,000) [7]; PSA screening's touted 20% was 3.7 to 3.0 in 1,000 [8] |
| Number needed to treat | Keep it, but also give the absolute difference | NNT is the reciprocal of the absolute risk reduction [9] |
| Sensitivity, specificity, prevalence | Natural frequencies out of 1,000 | With 1% prevalence, 90% sensitivity, 9% false-positive rate, about 9 of ~98 positives truly have the disease (~1 in 10). Most gynaecologists got this wrong as probabilities; 87% got it right with frequencies [8] |
| Survival rate (screening) | Mortality rate | 5-year survival 98% (US) vs 71% (UK) for prostate cancer, yet mortality 26 vs 27 per 100,000; survival rises with early diagnosis without longer life [7] |
| Single-event probability ("30% chance of side effect") | Frequency ("3 of 10 people who take it") | Single-event statements are misread as "30% of occasions" [7] |
| p-value | Effect size + confidence interval | ASA: p-values do not measure the probability the hypothesis is true, nor effect size or importance; decisions should not rest on a threshold alone [10] |

Why this matters: people understand absolute and relative reductions about equally, but relative
reductions are more persuasive; a Cochrane review of 35 papers found this, and presenting absolute risks
maximised accuracy without changing decisions to accept therapy (AAFP summary). [11]

## Numbers checklist (ask of every load-bearing number)
1. Compared with what? (baseline, control group, previous period)
2. Over what time, in what population? Does that population match ours (indirectness)?
3. How many people or events? (tiny event counts = imprecision)
4. Absolute or relative? Mean or median? Survival or mortality?
5. Who funded and who reported it? Is this the study or a press release about it?
6. Has the effect been replicated? Is it one study or a systematic review?
7. Correlation offered as causation? What confounders could explain it?
8. Is the chart axis, time window or unit chosen to exaggerate?

## Sources
1. Schünemann HJ et al. Chapter 14: Completing 'Summary of findings' tables and grading the certainty of the evidence. Cochrane Handbook v6.5, 2024 (chapter updated Aug 2023). https://training.cochrane.org/handbook/current/chapter-14
2. Cochrane Style Manual, "Common terms and terminology" (GRADE Working Group grades). https://www.cochrane.org/authors/handbooks-and-manuals/style-manual/names-common-terms-and-terminology/common-terms-and-terminology
3. CDC. ACIP GRADE Handbook, Chapter 7: GRADE criteria determining certainty of evidence. https://www.cdc.gov/acip-grade-handbook/hcp/chapter-7-grade-criteria-determining-certainty-of-evidence/index.html
4. NHMRC. Guidelines for Guidelines: Assessing certainty of evidence. https://www.nhmrc.gov.au/guidelinesforguidelines/develop/assessing-certainty-evidence
5. OCEBM Levels of Evidence Working Group. "The Oxford 2011 Levels of Evidence." Table reproduced at https://asht.org/sites/asht/files/CEBM-Levels-of-Evidence-2.1.pdf ; CEBM home page: https://www.cebm.ox.ac.uk/resources/levels-of-evidence/ocebm-levels-of-evidence
6. USF Health Libraries, Evidence Based Practice: Step 2. https://libraries.health.usf.edu/c.php?g=1224536&p=9095843
7. Gigerenzer G, Gaissmaier W, Kurz-Milcke E, Schwartz LM, Woloshin S. "Helping Doctors and Patients Make Sense of Health Statistics." Psychological Science in the Public Interest, 2007. https://journals.sagepub.com/doi/full/10.1111/j.1539-6053.2008.00033.x
8. Gigerenzer G. "Helping doctors and patients make sense of health statistics: towards an evidence-based society." ICOTS8 plenary, 2010. https://www.stat.auckland.ac.nz/~iase/publications/icots8/ICOTS8_PL3_GIGERENZER.pdf
9. Gigerenzer G, Edwards A. "Simple tools for understanding risks: from innumeracy to insight." BMJ 2003. https://pmc.ncbi.nlm.nih.gov/articles/PMC200816/
10. American Statistical Association. Statement on Statistical Significance and P-Values (press release), 2016-03-07. https://www.amstat.org/asa/files/pdfs/p-valuestatement.pdf
11. American Academy of Family Physicians, FPM. "Five Ways to Communicate Risks So That Patients Understand." 2018. https://aafp.org/fpm/2018/1100/p28
