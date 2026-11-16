# BDA course project plan 
Report deadline: Nov 29 (next sunday)
Presentation: Dec 4, 13.00 -14.45

## Datset selection 

`https://raw.githubusercontent.com/vincentarelbundock/Rdatasets/master/csv/medicaldata/indo_rct.csv`
Use only `indo_rct.csv` data from the medicaldata package. 

## Question 

How much does a single does of rectal indomethacin reduce the risk of post-ERCP pancreatitis in high-risk patients, after accountin for paient risk factors? Does baseline risk vary across sites, and does the treatment effect vary by SOD type? 

## Models

- M1 pooled logistic 
- M2 hierachica sites 
- M3 treatment heterigeneity 
- All risks factors with regularized horseshoe prior 

## Priors 

- coefficients: Normal(0,1)
- Intercept: Normal(logit(0.15),1)
- Site SD: HalfNormal(0,5)
- Treatment effect prior variants: weakly informative: Normal(0,1), skeptical: Normal(0, 0.35), enthusiastic: centered on the pre-2012 meta-analysis cited in introduction of https://pubmed.ncbi.nlm.nih.gov/18375470/ (it seemed that Aalto doesn't buy this article, but the number in the introduction should be enough)

## Inference 

Bambi/PyMC NUTS, 4 chains × 1000 warmup × 1000 draws,  target_accept 0.95 for hierarchical (non-centered by default)

## Workflow for all three models 

- Convergence: R-hat < 1.01, ESS bulk/tail > 400, 0 divergences, and what was done to fix issues
- PPC for binary outcome: calibration plot, not KDE, grouped PPC of event rate by site × arm and SOD type × arm
- Prior sensitivity: az.psense / az.psense_summary + refit with skeptical/enthusiastic priors
- Model comparison: PSIS-LOO with SE of elpd_diff, check Pareto k, practical measure (Brier score)
- Main inference: posterior of OR, absolute risk difference, NNT, P(benefit)

## Works 

- EDA (rates by arm/site/SOD type), pick covariates, standardize
- M1: priors, prior predictive, fit, diagnostics, PPC
- M2: fit, fix divergences, site-level PPC
- M3 (treatment × SOD type)
- LOO comparison, practical measures, risk difference/NNT
- Prior sensitivity incl. skeptical/enthusiastic priors; TA check-in
- Write report, figure polish
