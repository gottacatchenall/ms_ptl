**What is this paper?**

# What is Phylogenetic Transfer Learning

## Ancestral State Reconstruction 

_Ancestral State Reconstruction_ (ASR; also called ancestral character
reconstruction, character estimation, or character mapping) is a core topic in
phylogenetics [@Joy2016AncRec]. 

The goal is, given an estimate of a phylogeny $\mathcal{P}$ and trait values
$T_i$ for each extant species in the phylogeny $i$, to estimate the value of that
trait at some historical point in the phylogeny (typically at the node
representing the MRCA of a clade of interest).

To do this, one assumes a statistical model of evolution (and often tries to
infer the best among a set of candidate models). Depending on whether the trait
of interest is discrete or continuous, the models are typically discrete-space
Markov chains (where the transition matrix is a target of inference) or, in the
continuous setting, either Brownian Motion or an Ornstein-Uhlenbeck (OU) process
(where the parameters of the model are the target of inference). The former is
models neutral evolution and the latter is used when there is hypothesized
selection on the trait. 

The methodology used to fit models has followed the historical progression from
maximum parsimony models (a naive approach that favors as few evolutionary
changes as possible in discrete traits) to maximum likelihood estimation. Modern
methods revolve on using Bayesian methods, which has the direct benefit of
including uncertainty estimates in infered ancestral states, and potentially
uncertainty in the true topology of the phelogeny itself [@Huelsenbeck2001EmpHie]. 

Typically rate of trait evolution is learned as a single value across the whole
phylogeny, and branch length enables 'amount' of evolution. 

Although in principle a hierarchical* model could be used to infer both a global
rate of evolution and rate values specific to each branch (*sadly 'hierarchical'
in this is sense different than 'hierarchical' as it is used in
@Huelsenbeck2001EmpHie, which refers to different tree models but a single set
of parameters across all branches---this is because these models are constructed
in the context of DNA substition rates, which are assumed to be fixed). 

## Phylogenetic Transfer Learning

The core goal of Phylogenetic Transfer Learning (PTL) is to take a phylogeny
$\mathcal{P}$ where the species pool consists of two types of species: (1)
species with trait observations, which we denote $\mathcal{O}$ and call the
_observed_ species and (2) species _without_ trait observatoins, which we denote
$\mathcal{U}$ and call _unobserved_, and produce predicted trait values for the
unobserved species $\mathcal{U}$.

PTL does this by using ASR to infer a parameterized model of evolution and
ancestral state of the partially observed trait, and then to simulate the
parameterized evolutionary model forward from the ancestral node to get an
estimate of trait values (with uncertainty) for each species in $U$.

The _transfer learning_ component in particular comes from the first use of this
methods to inpute latent representations of species based on their position in
food-webs [@Strydom2022FooWeb;@Strydom2022GraEmb], although the method if
flexible enough to be applied to either latent or observed traits. 

There are two possible models for PTL to be done in: (1) As in
[@Strydom2022FooWeb], the the evolutionary model is inferred only from the
observed trait values $O$. (2) The evolutionary model from a trait for
which there are observations available for the entire species pool. It may be
the case that evolutionary dynamics inferred with auxillary available
information for every species (e.g. the raw sequences from which the tree is
built) could improve imputation accuracy. 


# Substance of the paper

1. Simulated phylogenies
    -   Test predictive efficacy of PTL under various parameterization of the
        "ground-truth" evolutionary dynamics
        - Rate of speciation
        - Rate of evolution
        - Trait dimensionality & correlation
        - Frequency of "puntuactions" (i.e. pertubations to trait value
        at a speciation event)
    - Predictive efficancy under different data scenarios
        - Proportion of species with trait values
        - Predictive efficacy vs. distance to MRCA w/ data
        - Do we have a set of traits for all species to infer evolutionary dynaimcs?
            - How correlated are evolution between traits for all species vs.
            traits we want to impute

2. Real data 

Thankfully there are lots of ACR studies out there with data for each extant
species. So, in short, find as many ACR studies as we can,



Relevant questions we want to address:
- What phylogenetic scales can PTL give robust predictions
    - There is a clear upper limit (if the MRCA for set of species is too long
      ago, there will be massive variance in predicted state at the tip)
    - There is also a lower limit (if there is horizontal-gene-transfer and the
      phylogeny is not a reliable proxy for how traits are evolving)
    - Robustness to noise in trait measurements?
    - How does this very w/ amount and different types of selection 
- When is PTL overkill?
    - Weighted average of neighbors by distance as alternative (ack. David
      Rolnick for idea) 


# Possible Applications

The core idea of PTL is to fill in gaps for data-sparse processes in ecology. 

- Link prediction
- SDMs: mechanistic niche predictions on subsets of species 
- Connectivity [@Catchen2023ImpEco]