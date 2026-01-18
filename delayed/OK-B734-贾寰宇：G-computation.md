# B734：G-computation

从 [The causal cookbook: Recipes for propensity scores, g-computation, and doubly robust standardization](https://journals.sagepub.com/doi/abs/10.1177/25152459241236149) 中抽取内容，再借助 DeepSeek，ChatGPT，豆包等工具完成写作。

> **Box 5**. G-Computation Recipe

Ingredients: Action $A$, outcome $Y$, and controls $C$.

Important: For an unbiased estimate, the controls $C$ must be sufficient to achieve conditional exchangeability.

- **Step 1**: Model the outcome as $\mathrm{Q}(\mathrm{A}, \mathrm{C})$, a function of the controls. For example, this could be a logistic regression predicting $Y$ from $A$ and $C$, using all individuals in the sample.
- **Step 2**: Duplicate the initial data set in two counterfactual data sets. In one of them, set $A=1$; in the other one, set $A=0$.
All other variables keep their original values.
- **Step 3**: Apply the function $\mathrm{Q}(\mathrm{A}, \mathrm{C})$ to predict each individual's outcome in the two counterfactual data sets; these are the model-implied potential outcomes $Y^1$ and $Y^0$.
- **Step 4**: Aggregate these potential outcomes (e.g., average across all individuals) and contrast them (e.g., by taking their difference) to arrive at an estimate of the estimand of interest.
  
Examples of R packages implementing this estimator: `marginaleffects`, `RISCA`, `stdReg`.
