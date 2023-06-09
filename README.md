**What is this paper?**

The goal of this paper is to introduce PTL to a broad audience, extend the
framework from being specific to latent representation of networks 
[@Strydom2022FooWeb;@Strydom2022GraEmb] to arbitrary traits, and provide
guidelines for when PTL is appropriate and how to validate its prediction using
both simulated and empirical data 

# What is Phylogenetic Transfer Learning

The goal of PTL is to take a species pool for which a given trait is only
_partially_ observed, and impute the value of that trait for the rest of the
species based on the evolutionary relatedness of each species. 

## Ancestral State Reconstruction 

_Ancestral State Reconstruction_ (ASR; also called ancestral character
reconstruction, character estimation, or character mapping) is a core topic in
phylogenetics [@Joy2016AncRec]. The goal is, given an estimate of a phylogeny
$\mathcal{P}$ and trait values $T_i$ for each extant species in the phylogeny
$i$, to estimate the value of that trait at some historical point in the
phylogeny (typically at the node representing the MRCA of a clade of interest).

To do this, one assumes a statistical model of evolution (and often tries to
infer the best among a set of candidate models). Depending on whether the trait
of interest is discrete or continuous, the models are typically discrete-space
Markov chains (where the transition matrix is a target of inference) or, in the
continuous setting, either Brownian Motion or an Ornstein-Uhlenbeck (OU) process
(where the parameters of the model are the target of inference). The former is
used to model neutral evolution and the latter for when there is hypothesized
selection on the trait. 

The methodology used to fit models has followed the historical progression from
maximum parsimony models (a naive approach that favors as few evolutionary
changes as possible in discrete traits) to maximum likelihood estimation. Modern
ASR typically uses Bayesian methods, which has the direct benefit of
including uncertainty estimates in inferred ancestral states, and potentially
uncertainty in the true topology of the phylogeny itself [@Huelsenbeck2001EmpHie]. 

Typically rate of trait evolution is learned as a single value across the whole
phylogeny, and branch length enables 'amount' of evolution. Although in
principle a hierarchical* model could be used to infer both a global rate of
evolution and rate values specific to each branch (*sadly 'hierarchical' in this
is sense different than 'hierarchical' as it is used in @Huelsenbeck2001EmpHie,
which refers to different tree models but a single set of parameters across all
branches---this is because these models are constructed in the context of DNA
substitution rates, which are assumed to be fixed). 

## Phylogenetic Transfer Learning

The core goal of Phylogenetic Transfer Learning (PTL) is to take a phylogeny
$\mathcal{P}$ where the species pool consists of two types of species: (1)
species with trait observations, which we denote $\mathcal{O}$ and call the
_observed_ species and (2) species _without_ trait observations, which we denote
$\mathcal{U}$ and call _unobserved_, and produce predicted trait values for the
unobserved species $\mathcal{U}$.

PTL does this by using ASR to infer a parameterized model of evolution and
ancestral state of the partially observed trait, and then to simulate the
parameterized evolutionary model forward from the most recent ancestral node
with an estimate state to get a prediction of trait values (with uncertainty)
for each unobserved species.

The _transfer learning_ terminology comes from the first use of this
method to impute latent representations of species based on their position in
food-webs [@Strydom2022FooWeb;@Strydom2022GraEmb], although the method is
flexible enough to be applied to either latent or observed traits. 

There are two possible ways for PTL to be done: (1) As in
[@Strydom2022FooWeb], where the evolutionary model is inferred only from the
observed trait values $\mathcal{O}$, or (2) the evolutionary model is infered
from a trait for which there are observations available for the entire species
pool. It may be the case that evolutionary dynamics inferred with auxiliary
"global" traits available for every species (e.g. the raw sequences from which
the tree is built) could improve imputation accuracy. 

![Conceptual visualization of PTL. Here, the trait of interest is partially
observed only for cows and elephants. Using ASR, we can obtain an estimate of
the ancestral state for the MRCA of the species considered here. Here we consider
the second version of PTL described above, where there are "global" traits
available for each species, which are used to infer the rate of evolutionary
change. Using this parameterized model of evolutionary dynamics, we can then
project forward to get imputed estimates of the trait value for the unobserved
species, elk and lions.](./figures/concept.png)

# Substance of the paper

The main substance of this paper is to provide guidelines on when PTL is robust,
and diagnostics to validate PTL estimates. This will be done in two parts:
**(1)** using simulated phylogenies and trait values to compare efficacy of PTL
imputation across known "ground-truth" evolutionary dynamics, and **(2)** using
published ASR datasets, with known values for all extant species, to test
imputation efficacy empirically. 

## Simulated phylogenies

First goal is to test predictive efficacy of PTL under various parameterizations
of the "ground-truth" evolutionary dynamics, e.g.

- Rate of speciation
- Rate of evolution
- Trait dimensionality & correlation across dimensions
- Frequency of "punctuations" (i.e. perturbations to trait value
at a speciation event)

and second to compare efficacy based on different properties of the data, e.g.

- Proportion of species with trait values
- Predictive efficacy vs. distance to MRCA w/ data
- Is there a set of "global" traits for all species that can be used to infer evolutionary dynamics?
    - How correlated are evolution between traits for all species vs.
    traits we want to impute

## Real data 

Thankfully there are lots of ACR studies out there with data for each extant
species. So, in short, find as many ACR studies as we can, and do
crossvalidation where we drop the trait values for ~20% of the species and
impute them with PTL, and compare. 

## Questions to address

- What phylogenetic scales can PTL give robust predictions?
    - There is a clear upper limit (if the MRCA for set of species is too long
      ago, there will be massive variance in predicted state at the tip)
    - There is also a lower limit (if there is horizontal-gene-transfer and the
      phylogeny is not a reliable proxy for how traits are evolving)
    - Robustness to noise in trait measurements?
    - How does this very w/ amount and different types of selection 
- When is PTL overkill?
    - Weighted average of neighbors by distance as alternative (ack. David
      Rolnick for idea) 
- What diagnostics can we use to be confident a PTL imputated trait is
  statistically robust?

# Possible Applications

The core idea of PTL is to fill in gaps for data-sparse processes in ecology, so
naturally the applications are going to be focused toward things that are hard
to sample.  

**Link prediction in networks**

This is the inciting question for which the idea was
conceived[@Strydom2022FooWeb]. Interactions are hard to sample
[@Catchen2023MisLin]. Not much to say here that isn't in [@Strydom2022GraEmb].

**Forecasting species range shifts**

Many projections of species range shifts under climate change are based on
statistical associations between historical species observations and climatic
conditions. The gap between so-called correlative vs. mechanistic species
distribution models [@Shabani2016ComAbs] is of critical interest for forecasting 
species ranges, but robust mechanistic understandings about what and why climatic
conditions limit where a species' range require detailed sampling and
potentially experimental conditions [@Lee-Yaw2016SynTra], which are difficult to scale. PTL could potentially alleviate this by giving good proxies of the
physiological limitations of species ranges for more species. 

**Connectivity and movement ecology**

Reliable information about species movement is sparse, and PTL could fill this gap [@Catchen2023ImpEco].

**Model species and ecological monitoring**

There are a lot of species on Earth. Monitoring them all would be hard.
Can we use single species as a proxy for a larger group of species? Maybe a
little. PTL can guide us on what good proxy species are. 

# References
