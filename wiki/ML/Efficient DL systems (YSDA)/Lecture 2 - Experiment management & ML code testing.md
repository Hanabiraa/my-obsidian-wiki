---
created:
  - " 25-01-2024 18:01"
aliases: 
tags:
  - article/
---

# Experiment management & ML code testing

## Teaser and reality

ML code:
- modelling, experiments
- offline quality evaluation
- data preprocessing
- code/model efficiency

Reality:
![[Pasted image 20240125181756.png]]

## Tracking the experiments

### Motivation
Training a model once is not enough:
- data gets updated
- hyperparameters tuning
- modify training code for better quality

**For all these acses, we need a way to keep track of our experiments**

### What to track
- run IDS and final metrics
- random seed
- start time & time needed
- resource management
- per-step/per-second metrics (convergence & performance)
- git commit hash for reproducibility (and diff for local changes)
- visualizations of model inputs/outputs
- stdout/stderr of training scripts (good logs)
- in some cases full info about the environment

* there are many tools for that
![[Pasted image 20240125183002.png]]

Also **aim**, **mlflow**

## Data versioning
- code is not the only component in a system
- data is a **crucial** dependency, especially in complicated pipelines
- tracking changes in it is equally important
- pinning each experiment to its data enhances reproducibility

**solutions** - artifact versioning
![[Pasted image 20240125183957.png]]

## Configuration
- infrastructure: API endpoints, data URLs, etc.
- Model hyperparameters and components

changing them manually across entire repo is not suitable

- `argparse/click`-based solutions are hard to write and properly version
- hardcoding values in dedicated Python files is not flexible enough

**Hydra** framework (*personally, very hard to maintain*)
![[Pasted image 20240125185517.png]]

## Testing
* verifying the intended code properties
	* Correctness, performance, handling inputs etc.
**Why test code?**
- helps to avoid bugs (now and when refactoring)
- But it **diesn't** prevent them!!
- improves the overall code quality by decoupling
- essentially, get self-documented code for free

### types of software tests
Unittest - of single components with single responsibilities
Integration tests - correct integration
acceptance - test application work - should have intended behaviour
![[Pasted image 20240125190918.png]]

![[Pasted image 20240125191021.png]]
Will be the focus on 1 and 2 on the course

### Test of Python code

![[Pasted image 20240125191327.png]]

**Better: pytest**
- flexible, works with assert statements, has plenty of integrations via plugins
![[Pasted image 20240125191423.png]]

**Raw asserts** - too hard without frameworks to determine all tests. Useful in the code where guarantees are met during code execution.


## Test-driven development in ML context

- write a test for before a function - failing test first



- can start from business requirements
- **keep tests a natural part of ypur workflow** - get a convenient setup both locally and in CI
- Leverage TDD for ML code as well
- Test the expected changes in behaviour of a model


## Property-based testing
How to generate test cases?
- coming up with own inputs is not exhausttive
- basically, we test that **the code works for given inputs**
- furthermore, our **requirements become unclear**

Property-based testing aims to sole this problem
- specify what inputs should be
- framework tests the code on many inputs and tries to simplify failing cases
### Python framework `hypothesis`

![[Pasted image 20240125192549.png]]


## Takeaways
1) Invest time in good and convenient logging
2) Make sure to keep track of data and models
3) Flexible configuration makes it easy to alter your experiment setups
4) Good tests simplify debugginh and maintenance