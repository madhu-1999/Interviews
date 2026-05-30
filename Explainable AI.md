#ml #dl 
# Taxonomy
![](https://christophm.github.io/interpretable-ml-book/images/taxonomy.jpg)
## Interpretability by Design (Intrinsic)
These are models that are **inherently interpretable** due to their simple structure
**Examples:** [Linear Regression](https://christophm.github.io/interpretable-ml-book/limo.html), [Logistic Regression](https://christophm.github.io/interpretable-ml-book/logistic.html), and short [Decision Trees](https://www.google.com/search?q=https://christophm.github.io/interpretable-ml-book/trees.html).
## Post-hoc Interpretability
These methods are applied **after training** to explain complex "black box" models.
### **Model-Specific** 
These only work for a single type of model because they analyze internal structures (like weights or gradients)
### **Model-Agnostic** 
These treat the model as a black box, only looking at inputs and outputs. 
They are highly flexible because you can switch the underlying machine learning model without changing the explanation method.
#### **Global Methods** 
Describe the **average behavior** of a model across an entire dataset.
#### **Local Methods**
Explain **individual predictions** (why did the model reject _this_ specific loan application?)
# References
https://christophm.github.io/interpretable-ml-book/