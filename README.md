<p align="center">
  <img src="https://github.com/iryna-kondr/scikit-llm/blob/main/logo.png?raw=true" height="200"/>
</p>

# Scikit-LLM: Sklearn Meets Large Language Models

Seamlessly integrate powerful language models like ChatGPT into scikit-learn for enhanced text analysis tasks.

## Installation 💾 

```bash 
pip install scikit-llm
```

## Documentation 📚

### Configuring OpenAI API Key
At the moment Scikit-LLM is only compatible with some of the OpenAI models. Hence, a user-provided OpenAI API key is required.

```python
from skllm.config import SKLLMConfig
SKLLMConfig.set_openai_key("<YOUR_KEY>")
SKLLMConfig.set_openai_org("<YOUR_ORGANISATION>")
```

### Zero-Shot Text Classification

One of the powerful ChatGPT features is the ability to perform text classification without being re-trained. For that, the only requirement is that the labels must be descriptive.

We provide a class `ZeroShotGPTClassifier` that allows to create such a model as a regular scikit-learn classifier.

Example 1: Training as a regular classifier
```python
from skllm import ZeroShotGPTClassifier
from skllm.datasets import get_classification_dataset

# demo sentiment analysis dataset
# labels: positive, negative, neutral
X, y = get_classification_dataset() 

clf = ZeroShotGPTClassifier(openai_model = "gpt-3.5-turbo")
clf.fit(X, y)
labels = clf.predict(X)
```
Scikit-LLM will automatically query the OpenAI API and transform the response into a regular list of labels.

Additionally, Scikit-LLM will ensure that the obtained response contains a valid label. If this is not the case, a label will be selected randomly (label probabilities are proportional to label occurrences in the training set).

Example 2: Training without labeled data

Since the training data is not strictly required, it can be fully ommited. The only thing that has to be provided is the list of candidate labels.

```python
from skllm import ZeroShotGPTClassifier
from skllm.datasets import get_classification_dataset

X, _ = get_classification_dataset()

clf = ZeroShotGPTClassifier()
clf.fit(None, ['positive', 'negative', 'neutral'])
labels = clf.predict(X)

```

### Multi-Label Zero-Shot Text Classification

With a class `MultiLabelZeroShotGPTClassifier` it is possible to perform the classification in multi-label setting, which means that each sample might be assigned to one or several distinct classes.

Example: 

```python
from skllm import MultiLabelZeroShotGPTClassifier
from skllm.datasets import get_multilabel_classification_dataset

X, y = get_multilabel_classification_dataset()

clf = MultiLabelZeroShotGPTClassifier(max_labels=3)
clf.fit(X, y)
labels = clf.predict(X)
```

Similarly to the `ZeroShotGPTClassifier` it is sufficient if only candidate labels are provided. However, this time the classifier expects `y` of a type `List[List[str]]`.

```
from skllm import MultiLabelZeroShotGPTClassifier
from skllm.datasets import get_multilabel_classification_dataset

X, _ = get_multilabel_classification_dataset()
candidate_labels = [
    "Quality", 
    "Price", 
    "Delivery", 
    "Service", 
    "Product Variety", 
    "Customer Support", 
    "Packaging", 
    "User Experience", 
    "Return Policy", 
    "Product Information"
]
clf = MultiLabelZeroShotGPTClassifier(max_labels=3)
clf.fit(None, [candidate_labels])
labels = clf.predict(X)
```

## Roadmap 🧭

- [x] Zero-Shot Classification with OpenAI GPT 3/4
    - [x] Multiclass classification
    - [x] Multi-label classification
    - [x] ChatGPT models
    - [ ] InstructGPT models
- [ ] Few shot classifier
- [ ] GPT Vectorizer
- [ ] GPT Fine-tuning (optional)
- [ ] Integration of other LLMs