# AI/ML Interview Guide
## Complete Question Bank with Timeline and Structure

---

## Table of Contents
1. [Interview Structure & Timeline](#interview-structure--timeline)
2. [Basic Technical Questions (Every Tech Person Should Know)](#basic-technical-questions-every-tech-person-should-know)
3. [AI/ML Basic Questions (30)](#aiml-basic-questions-30)
4. [AI/ML Medium Questions (30)](#aiml-medium-questions-30)
5. [AI/ML Hard Questions (30)](#aiml-hard-questions-30)
6. [Company-Specific Questions (RAG, Fine-tuning, Prompt-based Models)](#company-specific-questions)

---

## Interview Structure & Timeline

### **Total Duration: 90-120 minutes**

#### **Phase 1: Warm-up & Basic Technical Assessment (15-20 minutes)**
- Introduction and rapport building (5 min)
- Basic technical questions applicable to all tech roles (10-15 min)
- Assess: Communication skills, basic problem-solving, cultural fit

#### **Phase 2: AI/ML Fundamentals (20-25 minutes)**
- Basic AI/ML questions (15-20 questions from the basic pool)
- Focus on theoretical understanding and core concepts
- Assess: Foundational knowledge, ability to explain concepts clearly

#### **Phase 3: Practical AI/ML Knowledge (25-30 minutes)**
- Medium difficulty questions (10-15 questions)
- Include scenario-based questions
- Assess: Practical application, problem-solving approach

#### **Phase 4: Advanced Topics & Company-Specific (25-30 minutes)**
- Hard questions (5-8 questions)
- RAG, Fine-tuning, and Prompt Engineering specific questions
- Discuss past projects and implementation details
- Assess: Deep technical expertise, alignment with company needs

#### **Phase 5: Coding/Practical Exercise (Optional, 20-30 minutes)**
- Live coding or take-home assignment
- Focus on ML pipeline implementation or model evaluation

#### **Phase 6: Q&A and Closing (5-10 minutes)**
- Candidate questions
- Next steps discussion

---

## Basic Technical Questions (Every Tech Person Should Know)

### 1. **What is the difference between a process and a thread?**
**Answer:** A process is an independent program in execution with its own memory space, while a thread is a lightweight unit of execution within a process that shares memory with other threads in the same process. Threads enable concurrent execution within a single process.

### 2. **Explain the concept of REST API.**
**Answer:** REST (Representational State Transfer) is an architectural style for building web services. It uses HTTP methods (GET, POST, PUT, DELETE) to perform operations on resources identified by URLs. RESTful APIs are stateless, cacheable, and follow a client-server architecture.

### 3. **What is version control and why is it important?**
**Answer:** Version control (like Git) is a system that tracks changes to files over time, allowing multiple developers to collaborate, revert to previous versions, branch for experimentation, and maintain a complete history of code changes. It's essential for team collaboration and code management.

### 4. **What is the difference between SQL and NoSQL databases?**
**Answer:** SQL databases are relational, use structured schemas, support ACID transactions, and use SQL for querying (e.g., PostgreSQL, MySQL). NoSQL databases are non-relational, offer flexible schemas, scale horizontally, and are optimized for specific use cases like document storage (MongoDB), key-value pairs (Redis), or graph data (Neo4j).

### 5. **Explain the concept of time complexity (Big O notation).**
**Answer:** Time complexity describes how the runtime of an algorithm grows with input size. Common complexities: O(1) constant, O(log n) logarithmic, O(n) linear, O(n log n) linearithmic, O(n²) quadratic. It helps evaluate algorithm efficiency.

### 6. **What is a design pattern? Give an example.**
**Answer:** Design patterns are reusable solutions to common software design problems. Example: Singleton pattern ensures a class has only one instance and provides a global access point. Factory pattern creates objects without specifying their exact classes.

### 7. **What is the difference between authentication and authorization?**
**Answer:** Authentication verifies "who you are" (login with username/password), while authorization determines "what you can do" (permissions and access rights). Authentication comes first, then authorization.

### 8. **Explain Docker and its benefits.**
**Answer:** Docker is a containerization platform that packages applications with their dependencies into isolated containers. Benefits include consistency across environments, easy deployment, resource efficiency, scalability, and simplified DevOps workflows.

### 9. **What is CI/CD?**
**Answer:** Continuous Integration/Continuous Deployment automates the software delivery process. CI automatically tests and integrates code changes. CD automatically deploys tested code to production. This accelerates releases and reduces manual errors.

### 10. **What are microservices?**
**Answer:** Microservices architecture breaks applications into small, independent services that communicate via APIs. Each service handles a specific business function, can be developed and deployed independently, and uses its own technology stack and database.

---

## AI/ML Basic Questions (30)

### 1. **What is Machine Learning?**
**Answer:** Machine Learning is a subset of AI that enables systems to learn and improve from experience without being explicitly programmed. It uses algorithms to find patterns in data and make predictions or decisions based on those patterns.

### 2. **What are the main types of Machine Learning?**
**Answer:** 
- **Supervised Learning**: Learning from labeled data (classification, regression)
- **Unsupervised Learning**: Finding patterns in unlabeled data (clustering, dimensionality reduction)
- **Reinforcement Learning**: Learning through interaction with an environment using rewards and penalties
- **Semi-supervised Learning**: Combination of labeled and unlabeled data

### 3. **What is the difference between classification and regression?**
**Answer:** Classification predicts discrete categories/classes (e.g., spam/not spam, cat/dog), while regression predicts continuous numerical values (e.g., house prices, temperature).

### 4. **What is overfitting and how can you prevent it?**
**Answer:** Overfitting occurs when a model learns training data too well, including noise, and performs poorly on new data. Prevention methods:
- Cross-validation
- Regularization (L1, L2)
- Early stopping
- Dropout (for neural networks)
- More training data
- Simpler model architecture

### 5. **Explain the bias-variance tradeoff.**
**Answer:** Bias is error from incorrect assumptions (underfitting), variance is error from sensitivity to training data fluctuations (overfitting). High bias = oversimplified model, high variance = overly complex model. The goal is to balance both for optimal generalization.

### 6. **What is a confusion matrix?**
**Answer:** A confusion matrix is a table showing the performance of a classification model:
```
                Predicted
              Pos    Neg
Actual  Pos   TP     FN
        Neg   FP     TN
```
TP=True Positive, FP=False Positive, TN=True Negative, FN=False Negative

### 7. **What are precision and recall?**
**Answer:** 
- **Precision** = TP/(TP+FP) - Of all predicted positives, how many are actually positive?
- **Recall** = TP/(TP+FN) - Of all actual positives, how many did we correctly identify?
- F1-score is the harmonic mean of precision and recall.

### 8. **What is cross-validation?**
**Answer:** Cross-validation is a technique to assess model performance by splitting data into multiple folds. K-fold CV divides data into k subsets, trains on k-1 folds, validates on the remaining fold, and repeats k times. This provides a more robust performance estimate.

### 9. **What is the purpose of a train-test split?**
**Answer:** Splitting data into training and test sets allows us to train the model on one portion and evaluate its performance on unseen data. This helps detect overfitting and provides an honest assessment of generalization ability. Common splits: 80-20 or 70-30.

### 10. **What is gradient descent?**
**Answer:** Gradient descent is an optimization algorithm that iteratively adjusts model parameters to minimize a loss function. It calculates the gradient (slope) of the loss and moves in the opposite direction to find the minimum. Variants include batch, stochastic, and mini-batch gradient descent.

### 11. **What is a learning rate?**
**Answer:** Learning rate is a hyperparameter that controls the step size during gradient descent. Too high causes overshooting the minimum, too low results in slow convergence. Typical values: 0.001-0.1. Adaptive learning rates (Adam, RMSprop) adjust automatically.

### 12. **What is regularization?**
**Answer:** Regularization prevents overfitting by adding a penalty term to the loss function:
- **L1 (Lasso)**: Adds sum of absolute weights, encourages sparsity
- **L2 (Ridge)**: Adds sum of squared weights, shrinks all weights
- **Elastic Net**: Combines L1 and L2

### 13. **What is a neural network?**
**Answer:** A neural network is a computational model inspired by biological neurons, consisting of layers of interconnected nodes. Each connection has a weight, and nodes apply activation functions to weighted inputs. Networks learn by adjusting weights through backpropagation.

### 14. **What are activation functions and why are they important?**
**Answer:** Activation functions introduce non-linearity, enabling neural networks to learn complex patterns. Common ones:
- **ReLU**: f(x) = max(0, x) - most common
- **Sigmoid**: f(x) = 1/(1+e^(-x)) - outputs 0-1
- **Tanh**: f(x) = (e^x - e^(-x))/(e^x + e^(-x)) - outputs -1 to 1
- **Softmax**: For multi-class classification output

### 15. **What is backpropagation?**
**Answer:** Backpropagation is the algorithm for training neural networks. It calculates gradients of the loss function with respect to each weight by applying the chain rule backward through the network. These gradients are used to update weights via gradient descent.

### 16. **What is the difference between a parameter and a hyperparameter?**
**Answer:** 
- **Parameters**: Learned from data during training (e.g., neural network weights, linear regression coefficients)
- **Hyperparameters**: Set before training and control the learning process (e.g., learning rate, number of layers, batch size, regularization strength)

### 17. **What is batch size and epoch?**
**Answer:** 
- **Batch size**: Number of training examples processed before updating weights
- **Epoch**: One complete pass through the entire training dataset
- Example: 1000 samples, batch size 100 = 10 batches per epoch

### 18. **What is data normalization and why is it important?**
**Answer:** Normalization scales features to a similar range (e.g., 0-1 or mean=0, std=1). It's important because:
- Speeds up gradient descent convergence
- Prevents features with larger scales from dominating
- Required for distance-based algorithms (KNN, SVM)
Common methods: Min-Max scaling, Z-score standardization

### 19. **What is feature engineering?**
**Answer:** Feature engineering is creating new features or transforming existing ones to improve model performance. Techniques include:
- Creating interaction terms
- Polynomial features
- Binning continuous variables
- Encoding categorical variables
- Domain-specific transformations

### 20. **What is the curse of dimensionality?**
**Answer:** As the number of features increases, data becomes sparse in high-dimensional space, making it harder for models to find patterns. Problems include increased computational cost, overfitting, and need for exponentially more data. Solutions: feature selection, dimensionality reduction (PCA).

### 21. **What is Principal Component Analysis (PCA)?**
**Answer:** PCA is a dimensionality reduction technique that transforms features into a smaller set of uncorrelated components that capture most of the variance in the data. It identifies the directions (principal components) along which data varies most.

### 22. **What is the difference between L1 and L2 regularization?**
**Answer:** 
- **L1 (Lasso)**: Penalty = λ×|weights|, can shrink weights to exactly zero, performs feature selection
- **L2 (Ridge)**: Penalty = λ×weights², shrinks weights but rarely to zero, handles multicollinearity
- L1 is better for sparse models, L2 for dealing with correlated features

### 23. **What is a ROC curve and AUC?**
**Answer:** ROC (Receiver Operating Characteristic) curve plots True Positive Rate vs False Positive Rate at different classification thresholds. AUC (Area Under Curve) measures the area under the ROC curve (0-1). Higher AUC = better model. AUC=0.5 means random guessing, AUC=1.0 means perfect classifier.

### 24. **What is the difference between bagging and boosting?**
**Answer:** 
- **Bagging**: Trains multiple models independently on random subsets (with replacement) and averages predictions (e.g., Random Forest). Reduces variance.
- **Boosting**: Trains models sequentially, each correcting errors of previous ones (e.g., XGBoost, AdaBoost). Reduces bias.

### 25. **What is a Random Forest?**
**Answer:** Random Forest is an ensemble method that builds multiple decision trees on random subsets of data and features, then aggregates their predictions (voting for classification, averaging for regression). It's robust to overfitting and handles non-linear relationships well.

### 26. **What is K-Means clustering?**
**Answer:** K-Means is an unsupervised algorithm that partitions data into K clusters. Process:
1. Initialize K centroids randomly
2. Assign each point to nearest centroid
3. Recalculate centroids as cluster means
4. Repeat until convergence
Limitation: Requires predefined K, sensitive to initialization

### 27. **What is the elbow method?**
**Answer:** The elbow method helps determine optimal K in clustering by plotting the within-cluster sum of squares (WCSS) vs. number of clusters. The "elbow point" where WCSS starts decreasing slowly indicates a good K value.

### 28. **What is transfer learning?**
**Answer:** Transfer learning uses knowledge from a pre-trained model (trained on large datasets) and adapts it to a new task with less data. Common in deep learning (e.g., using ImageNet-trained models for custom image classification). Saves time and computational resources.

### 29. **What is the difference between batch gradient descent, stochastic gradient descent, and mini-batch gradient descent?**
**Answer:** 
- **Batch GD**: Uses entire dataset per update - stable but slow
- **Stochastic GD**: Uses one sample per update - fast but noisy
- **Mini-batch GD**: Uses small batches (e.g., 32-256 samples) - balance of speed and stability, most commonly used

### 30. **What is one-hot encoding?**
**Answer:** One-hot encoding converts categorical variables into binary vectors. Each category becomes a separate binary column. Example: Color [Red, Blue, Green] → Red[1,0,0], Blue[0,1,0], Green[0,0,1]. Prevents models from interpreting ordinal relationships in nominal data.

---

## AI/ML Medium Questions (30)

### 1. **Explain the architecture of a Convolutional Neural Network (CNN).**
**Answer:** CNNs are designed for grid-like data (images). Architecture:
- **Convolutional layers**: Apply filters to detect features (edges, textures)
- **Activation functions**: Usually ReLU after convolutions
- **Pooling layers**: Downsample feature maps (max pooling, average pooling)
- **Fully connected layers**: Final classification
- **Dropout**: Regularization
Key concepts: Local connectivity, parameter sharing, translation invariance

### 2. **What is batch normalization and why is it useful?**
**Answer:** Batch normalization normalizes layer inputs by adjusting and scaling activations. Benefits:
- Reduces internal covariate shift
- Allows higher learning rates
- Reduces sensitivity to initialization
- Acts as regularization
- Speeds up training
Applied between layers: normalize → scale (γ) → shift (β)

### 3. **Explain the vanishing gradient problem and how to address it.**
**Answer:** In deep networks, gradients become extremely small during backpropagation, preventing early layers from learning. Causes: sigmoid/tanh activations squashing gradients, many multiplicative operations.
Solutions:
- Use ReLU/Leaky ReLU activations
- Batch normalization
- Residual connections (ResNet)
- LSTM/GRU for RNNs
- Proper weight initialization (Xavier, He)

### 4. **What is dropout and how does it work?**
**Answer:** Dropout randomly deactivates neurons during training (typically 20-50% dropout rate). This prevents co-adaptation of neurons, forcing the network to learn robust features. At test time, all neurons are active but outputs are scaled. It's an effective regularization technique.

### 5. **Explain the concept of word embeddings (Word2Vec, GloVe).**
**Answer:** Word embeddings map words to dense vector representations that capture semantic relationships. 
- **Word2Vec**: Two approaches - CBOW (predicts word from context) and Skip-gram (predicts context from word)
- **GloVe**: Global Vectors, uses co-occurrence statistics
Similar words have similar vectors (king - man + woman ≈ queen)

### 6. **What is attention mechanism in neural networks?**
**Answer:** Attention allows models to focus on relevant parts of input when producing output. It computes weighted sums where weights represent importance. Used in:
- Seq2seq models (machine translation)
- Transformers
- Image captioning
Query-Key-Value framework: Attention(Q,K,V) = softmax(QK^T/√d_k)V

### 7. **Explain the Transformer architecture.**
**Answer:** Transformers use self-attention instead of recurrence:
- **Encoder**: Multi-head self-attention + feed-forward layers
- **Decoder**: Masked self-attention + encoder-decoder attention + feed-forward
- **Positional encoding**: Adds position information
- **Multi-head attention**: Multiple attention mechanisms in parallel
Advantages: Parallelization, long-range dependencies, state-of-the-art results (BERT, GPT)

### 8. **What is the difference between BERT and GPT?**
**Answer:** 
- **BERT**: Bidirectional, encoder-only, masked language modeling, good for understanding tasks (classification, Q&A)
- **GPT**: Unidirectional (left-to-right), decoder-only, autoregressive, good for generation tasks
- BERT sees both directions, GPT only sees previous tokens

### 9. **What is few-shot learning?**
**Answer:** Few-shot learning enables models to learn from very few examples (1-shot, 5-shot, etc.). Approaches:
- Meta-learning (learning to learn)
- Transfer learning with fine-tuning
- Prompt engineering (for LLMs)
- Siamese networks
- Prototypical networks
Important for scenarios with limited labeled data

### 10. **Explain the concept of learning rate scheduling.**
**Answer:** Learning rate scheduling adjusts the learning rate during training:
- **Step decay**: Reduce by factor every N epochs
- **Exponential decay**: Continuously decrease
- **Cosine annealing**: Follow cosine curve
- **Warm-up**: Start with low LR, gradually increase
- **Cyclic LR**: Oscillate between bounds
Benefits: Better convergence, escape local minima

### 11. **What is a Generative Adversarial Network (GAN)?**
**Answer:** GANs consist of two neural networks competing:
- **Generator**: Creates fake data from noise
- **Discriminator**: Distinguishes real from fake data
Training: Adversarial process where generator tries to fool discriminator. Applications: image generation, style transfer, data augmentation. Challenges: Mode collapse, training instability.

### 12. **What is the difference between accuracy, precision, recall, and F1-score?**
**Answer:** 
- **Accuracy**: (TP+TN)/(TP+TN+FP+FN) - overall correctness, misleading with imbalanced data
- **Precision**: TP/(TP+FP) - of predicted positives, how many are correct
- **Recall**: TP/(TP+FN) - of actual positives, how many were found
- **F1**: 2×(Precision×Recall)/(Precision+Recall) - harmonic mean
Use F1 when you need balance, precision when FP is costly, recall when FN is costly

### 13. **Explain dimensionality reduction techniques beyond PCA.**
**Answer:** 
- **t-SNE**: Non-linear, preserves local structure, good for visualization
- **UMAP**: Similar to t-SNE but faster, preserves global structure better
- **Autoencoders**: Neural networks that learn compressed representations
- **LDA**: Linear Discriminant Analysis, supervised dimensionality reduction
- **Feature selection**: Select subset of relevant features

### 14. **What is model ensembling and what are the common techniques?**
**Answer:** Ensembling combines multiple models for better performance:
- **Voting**: Majority vote (classification) or average (regression)
- **Bagging**: Bootstrap aggregating (Random Forest)
- **Boosting**: Sequential correction (XGBoost, AdaBoost, CatBoost)
- **Stacking**: Train meta-model on base models' predictions
- **Blending**: Similar to stacking but simpler
Reduces variance and bias, improves robustness

### 15. **What is XGBoost and why is it popular?**
**Answer:** XGBoost (Extreme Gradient Boosting) is an optimized gradient boosting implementation. Features:
- Regularization (L1/L2) to prevent overfitting
- Tree pruning using max_depth
- Built-in cross-validation
- Parallel processing
- Handling missing values
- Sparsity awareness
Popular for tabular data competitions, fast and accurate

### 16. **Explain the concept of feature importance.**
**Answer:** Feature importance measures how much each feature contributes to model predictions:
- **Tree-based**: Gain/split count from decision trees
- **Permutation**: Measure performance drop when feature is shuffled
- **SHAP**: Game-theoretic approach, additive feature attribution
- **Coefficients**: For linear models
Helps with feature selection and model interpretation

### 17. **What is SHAP (SHapley Additive exPlanations)?**
**Answer:** SHAP is a model-agnostic explainability method based on Shapley values from game theory. It assigns each feature an importance value for a particular prediction. Properties:
- Local interpretability (individual predictions)
- Global interpretability (aggregate SHAP values)
- Consistent and fair attribution
- Works with any model type

### 18. **What is the difference between online learning and batch learning?**
**Answer:** 
- **Batch learning**: Train on entire dataset at once, model is static after training, requires retraining for updates
- **Online learning**: Train incrementally on new data as it arrives, model continuously updates, suitable for streaming data
Online learning is useful for: large datasets that don't fit in memory, changing patterns, real-time systems

### 19. **Explain class imbalance and how to handle it.**
**Answer:** Class imbalance occurs when classes have very different frequencies. Problems: Model biased toward majority class.
Solutions:
- **Resampling**: Oversample minority (SMOTE) or undersample majority
- **Class weights**: Penalize misclassifying minority class more
- **Different metrics**: Use F1, precision-recall, not just accuracy
- **Ensemble methods**: Focus on difficult cases
- **Anomaly detection**: Treat minority as outliers

### 20. **What is SMOTE?**
**Answer:** SMOTE (Synthetic Minority Over-sampling Technique) generates synthetic samples for minority class:
1. Select a minority sample
2. Find k nearest minority neighbors
3. Create synthetic sample along line between them
4. Repeat until balanced
Advantage: Avoids exact duplication, creates diverse samples. Variations: ADASYN, BorderlineSMOTE

### 21. **Explain the concept of early stopping.**
**Answer:** Early stopping monitors validation performance during training and stops when it stops improving:
- Prevents overfitting
- Saves computational resources
- Typical: Stop if no improvement for N epochs (patience)
- Save best model based on validation metric
Common in neural networks and gradient boosting

### 22. **What is A/B testing in the context of ML models?**
**Answer:** A/B testing compares two model versions in production:
- Randomly split users/traffic between models
- Measure key metrics (accuracy, latency, business KPIs)
- Statistical significance testing
- Choose better performing model
Important for validating model improvements in real-world settings

### 23. **Explain the bias in AI/ML models (ethical bias).**
**Answer:** ML models can perpetuate or amplify biases present in training data:
- **Historical bias**: Past discriminatory practices in data
- **Representation bias**: Underrepresented groups
- **Measurement bias**: How labels are assigned
- **Aggregation bias**: One-size-fits-all models
Mitigation: Diverse datasets, fairness metrics, bias audits, diverse teams, regular monitoring

### 24. **What is federated learning?**
**Answer:** Federated learning trains models across decentralized devices without centralizing data:
- Devices train local models on local data
- Send model updates (not data) to central server
- Server aggregates updates
- Send improved model back to devices
Benefits: Privacy preservation, reduced communication costs. Used in: Mobile keyboards, healthcare

### 25. **Explain the concept of model drift.**
**Answer:** Model drift occurs when model performance degrades over time due to changing data patterns:
- **Concept drift**: Relationship between features and target changes
- **Data drift**: Feature distributions change
- **Upstream drift**: Changes in data pipeline
Detection: Monitor performance metrics, statistical tests. Solutions: Retraining, online learning, ensemble of models

### 26. **What is AutoML?**
**Answer:** AutoML automates ML pipeline tasks:
- Feature engineering
- Model selection
- Hyperparameter tuning
- Neural architecture search
- Ensemble creation
Tools: Google AutoML, H2O.ai, Auto-sklearn, TPOT. Benefits: Democratizes ML, saves time. Limitations: Black box, may not beat expert-designed solutions

### 27. **Explain the difference between L1 and L2 loss functions.**
**Answer:** 
- **L1 Loss (MAE)**: Sum of absolute differences, robust to outliers, non-differentiable at zero
- **L2 Loss (MSE)**: Sum of squared differences, penalizes large errors more, differentiable everywhere
- L1 converges slower, L2 provides unique solutions
Choice depends on outlier sensitivity and problem requirements

### 28. **What is knowledge distillation?**
**Answer:** Knowledge distillation transfers knowledge from a large teacher model to a smaller student model:
- Train large model (teacher) on data
- Use teacher's soft predictions (probabilities) to train student
- Student learns from teacher's knowledge, not just hard labels
- Results in smaller, faster model with similar performance
Used in model compression for deployment

### 29. **Explain multi-task learning.**
**Answer:** Multi-task learning trains a single model on multiple related tasks simultaneously:
- Shared representations for common features
- Task-specific layers for each task
- Benefits: Better generalization, faster learning, less overfitting
- Examples: Predict age and gender from face, multiple NLP tasks
Requires related tasks that benefit from shared knowledge

### 30. **What is the difference between Type I and Type II errors?**
**Answer:** 
- **Type I Error (False Positive)**: Rejecting true null hypothesis, detecting effect when none exists (α)
- **Type II Error (False Negative)**: Failing to reject false null hypothesis, missing real effect (β)
- Trade-off controlled by significance level
- In ML: FP vs FN, depends on domain which is costlier (medical diagnosis vs spam filter)

---

## AI/ML Hard Questions (30)

### 1. **Explain the mathematical foundation of backpropagation using chain rule.**
**Answer:** Backpropagation applies chain rule to compute gradients:
For loss L and parameter w in layer l:
∂L/∂w^(l) = ∂L/∂a^(l) × ∂a^(l)/∂z^(l) × ∂z^(l)/∂w^(l)

Where:
- a^(l) = activation output
- z^(l) = weighted sum (before activation)
- Chain rule propagates gradients backward: δ^(l) = (W^(l+1))^T δ^(l+1) ⊙ σ'(z^(l))

This enables efficient gradient computation for all parameters in one backward pass.

### 2. **Derive the gradient descent update rule for logistic regression.**
**Answer:** 
Loss: L = -[y log(p) + (1-y)log(1-p)] where p = σ(w^T x)
Gradient: ∂L/∂w = (p - y)x

Derivation:
∂L/∂w = ∂L/∂p × ∂p/∂w
∂L/∂p = (p-y)/(p(1-p))
∂p/∂w = p(1-p)x  [sigmoid derivative]
∂L/∂w = (p-y)x

Update: w ← w - α(p-y)x

### 3. **Explain the mathematics behind Support Vector Machines (SVM).**
**Answer:** SVM finds optimal hyperplane maximizing margin:

Primal problem:
minimize: (1/2)||w||² 
subject to: y_i(w^T x_i + b) ≥ 1

Using Lagrangian and KKT conditions → Dual problem:
maximize: Σα_i - (1/2)ΣΣα_i α_j y_i y_j x_i^T x_j
subject to: Σα_i y_i = 0, α_i ≥ 0

Kernel trick: Replace x_i^T x_j with K(x_i, x_j) for non-linear separation
Decision: sign(Σα_i y_i K(x_i, x) + b)

### 4. **Explain the EM (Expectation-Maximization) algorithm.**
**Answer:** EM algorithm for maximum likelihood estimation with latent variables:

**E-step**: Compute expected value of log-likelihood w.r.t. posterior of latent variables:
Q(θ|θ^(t)) = E_z[log P(X,Z|θ) | X, θ^(t)]

**M-step**: Maximize Q to find new parameters:
θ^(t+1) = argmax_θ Q(θ|θ^(t))

Repeat until convergence. Guarantees monotonic increase in likelihood.

Example: Gaussian Mixture Models
- E-step: Compute cluster responsibilities
- M-step: Update means, covariances, mixing coefficients

### 5. **What is the difference between variational inference and MCMC?**
**Answer:** Both approximate intractable posterior distributions:

**Variational Inference**:
- Optimization problem: Find q(z) closest to p(z|x)
- Minimize KL divergence: KL(q||p)
- Deterministic, fast, scalable
- May underestimate uncertainty
- Example: Variational Autoencoders

**MCMC** (Markov Chain Monte Carlo):
- Sampling-based: Generate samples from p(z|x)
- Asymptotically exact
- Stochastic, slower, harder to scale
- Better uncertainty quantification
- Examples: Gibbs sampling, Metropolis-Hastings

### 6. **Explain Variational Autoencoders (VAE) and their loss function.**
**Answer:** VAEs are generative models with:
- **Encoder**: q_φ(z|x) approximates posterior
- **Decoder**: p_θ(x|z) generates data
- Latent space has prior p(z) = N(0,I)

Loss (ELBO - Evidence Lower Bound):
L = E_q[log p_θ(x|z)] - KL(q_φ(z|x) || p(z))
    reconstruction loss    regularization

- First term: Reconstruction quality
- Second term: Keep posterior close to prior
- Reparameterization trick: z = μ + σε, ε~N(0,I) for backpropagation

### 7. **Explain the attention mechanism mathematically.**
**Answer:** 
Scaled Dot-Product Attention:
Attention(Q, K, V) = softmax(QK^T / √d_k) V

Where:
- Q (query), K (key), V (value) are linear projections
- d_k: Key dimension
- Scaling prevents large dot products pushing softmax into saturation

Multi-Head Attention:
MultiHead(Q,K,V) = Concat(head_1,...,head_h)W^O
where head_i = Attention(QW_i^Q, KW_i^K, VW_i^V)

Allows attending to different representation subspaces.

### 8. **Explain the difference between maximum likelihood estimation (MLE) and maximum a posteriori (MAP) estimation.**
**Answer:** 
**MLE**: θ_MLE = argmax_θ P(D|θ)
- Maximizes likelihood of observed data
- Point estimate
- No prior beliefs

**MAP**: θ_MAP = argmax_θ P(θ|D) = argmax_θ P(D|θ)P(θ)
- Maximizes posterior probability
- Incorporates prior P(θ)
- Reduces to MLE with uniform prior
- Example: L2 regularization ≡ Gaussian prior

Full Bayesian: Compute entire posterior P(θ|D), not just mode

### 9. **Explain the mathematics of Principal Component Analysis (PCA).**
**Answer:** 
PCA finds orthogonal directions of maximum variance:

1. Center data: X_centered = X - mean(X)
2. Covariance matrix: C = (1/n)X^T X
3. Eigendecomposition: C = VΛV^T
   - V: eigenvectors (principal components)
   - Λ: eigenvalues (variance explained)
4. Project: Z = XV_k (keep top k eigenvectors)

Alternatively: SVD of X = UΣV^T
- V columns are principal components
- Σ² / n are eigenvalues

Dimensionality reduction: Keep components explaining desired variance.

### 10. **What is the kernel trick in SVM and how does it work?**
**Answer:** 
Kernel trick maps data to higher dimensional space without explicit computation:

Linear SVM decision: sign(w^T x + b) = sign(Σα_i y_i x_i^T x + b)
Kernel SVM: sign(Σα_i y_i K(x_i, x) + b)

Common kernels:
- RBF: K(x,x') = exp(-γ||x-x'||²)
- Polynomial: K(x,x') = (x^T x' + c)^d
- Sigmoid: K(x,x') = tanh(αx^T x' + c)

Mercer's theorem: Kernel must be positive semi-definite
Implicitly computes: K(x,x') = φ(x)^T φ(x') without computing φ(x)

### 11. **Explain the Bellman equation in reinforcement learning.**
**Answer:** 
Bellman equation expresses recursive relationship for value functions:

**State-value**: V(s) = E[R_t | S_t=s]
Bellman equation: V(s) = Σ_a π(a|s) Σ_s',r p(s',r|s,a)[r + γV(s')]

**Action-value**: Q(s,a) = E[R_t | S_t=s, A_t=a]
Q(s,a) = Σ_s',r p(s',r|s,a)[r + γ Σ_a' π(a'|s')Q(s',a')]

**Bellman optimality**:
V*(s) = max_a Σ_s',r p(s',r|s,a)[r + γV*(s')]
Q*(s,a) = Σ_s',r p(s',r|s,a)[r + γ max_a' Q*(s',a')]

Forms basis for Q-learning, value iteration, policy iteration.

### 12. **Explain the actor-critic algorithm in reinforcement learning.**
**Answer:** 
Actor-Critic combines value-based and policy-based RL:

**Actor** (policy): π_θ(a|s) - selects actions
**Critic** (value): V_φ(s) or Q_φ(s,a) - evaluates actions

Algorithm:
1. Actor takes action a ~ π_θ(s)
2. Observe reward r and next state s'
3. Critic computes TD error: δ = r + γV_φ(s') - V_φ(s)
4. Update critic: φ ← φ + α_c δ ∇_φ V_φ(s)
5. Update actor: θ ← θ + α_a δ ∇_θ log π_θ(a|s)

Advantages:
- Lower variance than REINFORCE
- More stable than pure policy gradient
- Examples: A3C, PPO, SAC

### 13. **What is the difference between Q-learning and SARSA?**
**Answer:** 
Both are TD (Temporal Difference) learning algorithms:

**Q-learning** (off-policy):
Q(s,a) ← Q(s,a) + α[r + γ max_a' Q(s',a') - Q(s,a)]
- Learns optimal policy regardless of behavior policy
- Explores but learns greedy policy
- Can overestimate values

**SARSA** (on-policy):
Q(s,a) ← Q(s,a) + α[r + γ Q(s',a') - Q(s,a)]
- Learns value of current policy (including exploration)
- More conservative
- Better for risky environments

Q-learning: "What if I always act optimally?"
SARSA: "What if I continue exploring like I do?"

### 14. **Explain the policy gradient theorem.**
**Answer:** 
Policy Gradient finds policy parameters maximizing expected return:

Objective: J(θ) = E_τ~π_θ[Σ_t r_t]

Policy Gradient Theorem:
∇_θ J(θ) = E_τ~π_θ[Σ_t ∇_θ log π_θ(a_t|s_t) G_t]

where G_t = Σ_t' γ^(t'-t) r_t' (return from time t)

REINFORCE algorithm: θ ← θ + α ∇_θ log π_θ(a|s) G_t

Advantage Actor-Critic uses A(s,a) instead of G_t:
∇_θ J(θ) = E[∇_θ log π_θ(a|s) A(s,a)]
where A(s,a) = Q(s,a) - V(s)

Reduces variance while keeping unbiased gradient.

### 15. **Explain the difference between proximal policy optimization (PPO) and trust region policy optimization (TRPO).**
**Answer:** 
Both constrain policy updates for stability:

**TRPO**:
- Constrains KL divergence: KL(π_old || π_new) ≤ δ
- Guarantees monotonic improvement
- Complex second-order optimization
- Computationally expensive

**PPO**:
- Clip objective: L^CLIP = E[min(r_t(θ)A_t, clip(r_t(θ), 1-ε, 1+ε)A_t)]
  where r_t(θ) = π_θ(a|s)/π_θ_old(a|s)
- Simpler first-order optimization
- Nearly TRPO performance
- More practical, widely used

PPO is the de facto standard for policy gradient methods.

### 16. **Explain the reparameterization trick in VAEs.**
**Answer:** 
Problem: Sampling z ~ q_φ(z|x) is non-differentiable.

Reparameterization:
- Original: z ~ N(μ(x), σ²(x))
- Reparameterized: z = μ(x) + σ(x)⊙ε, where ε ~ N(0,I)

Now z is deterministic function of ε and parameters (μ, σ).
Gradient flows through μ and σ:
∇_φ E_q[f(z)] = E_ε[∇_φ f(μ + σ⊙ε)]

Enables end-to-end training with backpropagation.

Generalizes to other distributions with location-scale families.

### 17. **What is batch normalization's effect on gradient flow and how does it work mathematically?**
**Answer:** 
Batch Normalization normalizes layer inputs:

Forward pass:
1. Normalize: x̂ = (x - μ_B) / √(σ_B² + ε)
2. Scale and shift: y = γx̂ + β

where μ_B, σ_B are batch statistics, γ, β are learned.

Effects on gradients:
- Reduces internal covariate shift
- Gradients don't explode/vanish due to normalization
- Allows higher learning rates
- Smooths loss landscape

Backward pass: Gradients flow through normalization operation.
During inference: Use running statistics instead of batch statistics.

Subtlety: Different behavior train vs test can cause issues.

### 18. **Explain the mathematics behind LSTM cells.**
**Answer:** 
LSTM uses gates to control information flow:

**Gates**:
- Forget: f_t = σ(W_f[h_(t-1), x_t] + b_f)
- Input: i_t = σ(W_i[h_(t-1), x_t] + b_i)
- Output: o_t = σ(W_o[h_(t-1), x_t] + b_o)

**Cell state update**:
- Candidate: c̃_t = tanh(W_c[h_(t-1), x_t] + b_c)
- Cell state: c_t = f_t ⊙ c_(t-1) + i_t ⊙ c̃_t
- Hidden state: h_t = o_t ⊙ tanh(c_t)

Key: Cell state c_t forms memory highway
- Forget gate removes information
- Input gate adds information
- Output gate reads information

Solves vanishing gradient via additive cell state updates.

### 19. **Explain the difference between self-attention and cross-attention.**
**Answer:** 
**Self-Attention**:
- Q, K, V all from same sequence
- Each position attends to all positions in same sequence
- Captures intra-sequence dependencies
- Used in: BERT, GPT, encoder layers

**Cross-Attention**:
- Q from one sequence, K and V from another
- Target sequence attends to source sequence
- Captures inter-sequence dependencies
- Used in: Transformer decoder, seq2seq models

Example: Machine translation
- Encoder: Self-attention on source
- Decoder: Self-attention on target + Cross-attention to source

### 20. **Explain the concept of neural architecture search (NAS).**
**Answer:** 
NAS automates neural network design:

**Search space**: Defines possible architectures
- Macro: Number of layers, connections
- Micro: Operations within cells
- Examples: ResNet-like, dense connections

**Search strategy**:
- Random search
- Reinforcement learning (controller generates architectures)
- Evolutionary algorithms
- Gradient-based (DARTS)
- Bayesian optimization

**Evaluation**: Train candidate architectures (expensive)
- Early stopping
- Weight sharing
- One-shot methods

Results: Discovered architectures (NASNet, EfficientNet) competitive with hand-designed.

Challenges: Computational cost (1000s of GPU days)

### 21. **Explain mixture of experts (MoE) architecture.**
**Answer:** 
MoE uses conditional computation with multiple expert networks:

**Architecture**:
- Multiple expert networks: E_1, ..., E_n
- Gating network: G(x) outputs weights
- Output: y = Σ_i G(x)_i E_i(x)

**Gating mechanisms**:
- Soft gating: All experts contribute (weighted)
- Sparse gating: Top-k experts (efficiency)
- Noisy top-k: Add noise for exploration

**Benefits**:
- Model capacity without proportional computation
- Specialization (different experts for different inputs)
- Scalability

**Challenges**:
- Load balancing (ensure all experts used)
- Training stability

Used in: GPT-4, Switch Transformer (sparse MoE)

### 22. **What is contrastive learning and how does it work?**
**Answer:** 
Contrastive learning learns representations by comparing similar vs dissimilar samples:

**Principle**: 
- Positive pairs: Similar samples (augmented versions)
- Negative pairs: Dissimilar samples
- Pull positives together, push negatives apart

**Loss functions**:
- Contrastive loss: L = y d² + (1-y)max(margin-d, 0)²
- Triplet loss: L = max(d(a,p) - d(a,n) + margin, 0)
- InfoNCE: L = -log[exp(sim(x,x⁺)/τ) / Σ_i exp(sim(x,x_i)/τ)]

**Methods**:
- SimCLR: Two augmentations, large batch
- MoCo: Momentum encoder, queue
- CLIP: Image-text pairs

Self-supervised learning without labels.

### 23. **Explain the concept of neural ordinary differential equations (Neural ODEs).**
**Answer:** 
Neural ODEs model hidden states as continuous-time dynamics:

**Standard ResNet**: h_(t+1) = h_t + f(h_t, θ_t)
**Neural ODE**: dh/dt = f(h(t), t, θ)

**Solution**: h(t_1) = h(t_0) + ∫_{t_0}^{t_1} f(h(t), t, θ) dt
Computed using ODE solvers (adaptive step size)

**Adjoint method** for gradients:
- Avoids backprop through solver operations
- Constant memory cost
- Compute gradients via backward ODE solve

**Advantages**:
- Continuous depth
- Adaptive computation
- Memory efficient
- Normalizing flows

**Applications**: Time series, density estimation, generative models

### 24. **What is the difference between instance normalization and layer normalization?**
**Answer:** 
Normalization methods differ in which dimensions they normalize over:

**Batch Norm**: Normalize across batch dimension
- Shape: [N, C, H, W] → normalize over N for each (C,H,W)
- Requires large batch size
- Different train/test behavior

**Layer Norm**: Normalize across feature dimension
- Shape: [N, C, H, W] → normalize over (C,H,W) for each N
- Works with batch size 1
- Same train/test behavior
- Used in: Transformers, RNNs

**Instance Norm**: Normalize per sample, per channel
- Shape: [N, C, H, W] → normalize over (H,W) for each (N,C)
- Used in: Style transfer, GANs

**Group Norm**: Normalize within channel groups
- Compromise between layer and instance norm

### 25. **Explain the mathematical foundation of diffusion models.**
**Answer:** 
Diffusion models learn to reverse a gradual noising process:

**Forward process** (fixed, adds noise):
q(x_t|x_{t-1}) = N(x_t; √(1-β_t) x_{t-1}, β_t I)
x_t = √(1-β_t) x_{t-1} + √β_t ε, ε ~ N(0,I)

Closed form: q(x_t|x_0) = N(x_t; √ᾱ_t x_0, (1-ᾱ_t)I)
where ᾱ_t = ∏_{s=1}^t (1-β_s)

**Reverse process** (learned):
p_θ(x_{t-1}|x_t) = N(x_{t-1}; μ_θ(x_t,t), Σ_θ(x_t,t))

**Training**: Learn to predict noise ε
L = E_{t,x_0,ε}[||ε - ε_θ(x_t, t)||²]

**Sampling**: Start from x_T ~ N(0,I), iteratively denoise

**Score-based view**: Learn ∇_x log p(x)
Connection to score matching and Langevin dynamics.

### 26. **What is curriculum learning and why is it effective?**
**Answer:** 
Curriculum learning trains models on gradually increasing difficulty:

**Concept**: Start with easy examples, progress to harder ones
(Inspired by human learning)

**Strategies**:
- **Predefined**: Hand-crafted difficulty measure
- **Self-paced**: Model automatically selects easier examples
- **Transfer**: Train on simple task, transfer to complex
- **Multi-stage**: Coarse-to-fine, shallow-to-deep

**Why effective**:
- Faster convergence
- Better local minima
- Improved generalization
- Easier training of deep networks

**Examples**:
- NMT: Short sentences → long sentences
- Vision: Low resolution → high resolution
- RL: Simple environments → complex

Not always beneficial; depends on task and curriculum design.

### 27. **Explain the concept of meta-learning (learning to learn).**
**Answer:** 
Meta-learning trains models to quickly adapt to new tasks:

**Framework**:
- **Meta-training**: Learn from multiple tasks
- **Meta-testing**: Quickly adapt to new task with few examples

**Approaches**:

**1. Model-Agnostic Meta-Learning (MAML)**:
- Find initialization θ that adapts quickly
- θ* = θ - α∇L_τ(θ) for each task τ
- Meta-update: θ ← θ - β∇_θ Σ_τ L_τ(θ*)
- Second-order optimization

**2. Metric Learning**:
- Learn embedding space where similar samples are close
- Prototypical networks, matching networks
- Few-shot classification via nearest neighbors

**3. Memory-Augmented**:
- External memory for rapid learning
- Neural Turing Machines, MANN

**Applications**: Few-shot learning, hyperparameter optimization, neural architecture search

### 28. **Explain the lottery ticket hypothesis.**
**Answer:** 
Lottery Ticket Hypothesis: Dense neural networks contain sparse subnetworks ("winning tickets") that can train in isolation to reach comparable accuracy.

**Finding lottery tickets**:
1. Randomly initialize network
2. Train to convergence
3. Prune smallest-magnitude weights
4. Reset remaining weights to initial values
5. Retrain pruned network

**Key insight**: Sparse network with right initialization (not random) matches full network performance.

**Implications**:
- Overparameterization helps find good subnetworks
- Explains why neural networks train well
- Enables efficient architectures

**Extensions**:
- Rewinding (reset to epoch k, not 0)
- Supermasks (binary masks, no training)
- Universal lottery tickets

### 29. **What is neural tangent kernel (NTK) and its significance?**
**Answer:** 
NTK describes infinite-width neural network behavior:

**Definition**: Kernel matrix K(x,x') = ⟨∇_θ f(x,θ), ∇_θ f(x,x',θ)⟩

**Key result**: Infinitely wide networks with gradient descent behave like kernel methods with fixed NTK.

**Properties**:
- Training dynamics become linear in function space
- Kernel remains nearly constant during training
- Convergence guarantees via kernel theory

**Significance**:
- Theoretical understanding of neural networks
- Explains why overparameterized networks don't overfit
- Connection to Gaussian processes
- Limitations: Real networks aren't infinite width, feature learning

**Extensions**: Neural path kernel, feature learning beyond NTK

### 30. **Explain double descent phenomenon in deep learning.**
**Answer:** 
Double descent: Test error initially decreases, increases (classical overfitting), then decreases again with more parameters.

**Three regimes**:
1. **Underparameterized**: Classical bias-variance tradeoff
2. **Interpolation threshold**: Maximum test error (exactly fits training data)
3. **Overparameterized**: Decreasing test error with more parameters

**Why it happens**:
- Multiple models fit training data perfectly
- Gradient descent finds smooth interpolating function
- Implicit regularization from optimization
- "Benign overfitting"

**Variants**:
- Model-wise: Vary model size
- Sample-wise: Vary training data size
- Epoch-wise: Vary training time

**Implications**: Overparameterization can help generalization, contrary to classical theory.

---

## Company-Specific Questions

### RAG (Retrieval-Augmented Generation) Questions

#### 1. **What is RAG and why is it useful?**
**Answer:** RAG combines retrieval systems with language models:
- **Retrieval**: Find relevant documents from knowledge base
- **Generation**: LLM generates answer conditioned on retrieved docs

**Benefits**:
- Access to up-to-date information
- Reduces hallucinations
- Cites sources
- No need to retrain for new information
- Cost-effective vs fine-tuning

**Architecture**: Query → Retriever (vector DB) → Top-k docs → LLM + docs → Answer

#### 2. **How do you implement a RAG system?**
**Answer:** 
**Components**:
1. **Document processing**: Chunking, preprocessing
2. **Embedding model**: Convert text to vectors (e.g., sentence-transformers)
3. **Vector database**: Store embeddings (Pinecone, Weaviate, ChromaDB)
4. **Retrieval**: Similarity search (cosine, dot product)
5. **Ranking**: Rerank retrieved documents
6. **Generation**: LLM with retrieved context

**Pipeline**:
```python
# Index documents
embeddings = embed_model.encode(documents)
vector_db.add(embeddings, documents)

# Query
query_emb = embed_model.encode(query)
top_docs = vector_db.search(query_emb, k=5)
prompt = f"Context: {top_docs}\nQuestion: {query}"
answer = llm.generate(prompt)
```

#### 3. **What are common challenges in RAG systems?**
**Answer:** 
1. **Retrieval quality**: Irrelevant documents retrieved
   - Solution: Better embeddings, hybrid search, reranking
2. **Chunk size**: Too small loses context, too large loses relevance
   - Solution: Overlapping chunks, hierarchical chunking
3. **Context window**: Limited LLM context
   - Solution: Better retrieval, summarization, long-context models
4. **Latency**: Multiple steps add delay
   - Solution: Caching, async retrieval, optimized vector DBs
5. **Hallucination**: LLM ignores retrieved docs
   - Solution: Instruction tuning, citations, fact-checking
6. **Scalability**: Large document collections
   - Solution: Distributed vector DBs, efficient indexing

#### 4. **How do you evaluate a RAG system?**
**Answer:** 
**Retrieval metrics**:
- Precision@k, Recall@k
- MRR (Mean Reciprocal Rank)
- NDCG (Normalized Discounted Cumulative Gain)

**Generation metrics**:
- BLEU, ROUGE (reference-based)
- BERTScore (semantic similarity)
- Faithfulness (answer aligns with retrieved docs)
- Answer relevance

**End-to-end**:
- Human evaluation
- LLM-as-judge (GPT-4 scoring)
- Task-specific metrics (accuracy for QA)

**Component analysis**: Retrieval recall vs generation quality

#### 5. **What is the difference between dense retrieval and sparse retrieval?**
**Answer:** 
**Sparse retrieval** (BM25, TF-IDF):
- Based on exact keyword matching
- Fast, interpretable
- Misses semantic similarity

**Dense retrieval** (bi-encoder):
- Neural embeddings, semantic search
- Captures meaning beyond keywords
- Requires training or large models

**Hybrid approach**:
- Combine both: (α × sparse_score + (1-α) × dense_score)
- Best of both worlds
- Used in production systems

#### 6. **How do you handle multi-hop reasoning in RAG?**
**Answer:** 
Multi-hop: Answer requires information from multiple documents.

**Approaches**:
1. **Iterative retrieval**: Retrieve → Generate → Retrieve again based on intermediate answer
2. **Graph-based**: Build knowledge graph, traverse for multi-hop paths
3. **Decomposition**: Break complex query into sub-questions
4. **Self-ask**: Model generates and answers intermediate questions
5. **Chain-of-thought**: Encourage reasoning steps in generation

**Example**: "Who is the spouse of the director of Movie X?"
- Hop 1: Retrieve director of Movie X
- Hop 2: Retrieve spouse of that director

### Fine-Tuning Questions

#### 7. **What is the difference between fine-tuning and prompt engineering?**
**Answer:** 
**Prompt engineering**:
- Craft input prompts for zero/few-shot learning
- No model updates
- Fast, flexible, low cost
- Limited by context window
- Best for: General tasks, quick iteration

**Fine-tuning**:
- Update model weights on task-specific data
- Requires labeled data and compute
- Better task performance
- Model specialization
- Best for: Domain-specific, high-volume tasks

**When to use**:
- Start with prompting, fine-tune if needed
- Fine-tune for: Custom tone, proprietary data, consistent behavior

#### 8. **What are the different types of fine-tuning?**
**Answer:** 
1. **Full fine-tuning**: Update all model parameters
   - Best performance
   - Requires most resources
   - Risk of catastrophic forgetting

2. **Parameter-efficient fine-tuning (PEFT)**:
   - **LoRA**: Add low-rank matrices, train only those
   - **Adapter layers**: Insert small layers, freeze base model
   - **Prefix tuning**: Add trainable prefixes to each layer
   - **Prompt tuning**: Learn soft prompts (continuous vectors)

3. **Task-specific head**: Freeze encoder, train classification head
4. **Gradual unfreezing**: Progressively unfreeze layers
5. **Discriminative fine-tuning**: Different LR for different layers

#### 9. **Explain LoRA (Low-Rank Adaptation) in detail.**
**Answer:** 
LoRA adds trainable low-rank matrices to frozen pre-trained weights:

**Method**:
- Original: W ∈ R^(d×k)
- LoRA: W' = W + BA, where B ∈ R^(d×r), A ∈ R^(r×k), r << min(d,k)
- Only train B and A, freeze W

**Forward pass**: h = (W + BA)x = Wx + BAx

**Benefits**:
- Few trainable parameters (typically <1% of model)
- No inference latency (can merge W + BA)
- Multiple LoRA modules for different tasks
- Lower memory, faster training

**Hyperparameters**:
- Rank r (typically 4-64)
- Alpha (scaling factor)
- Which layers to apply (usually attention)

**When to use**: Large models, limited compute, multiple task adaptation

#### 10. **What is catastrophic forgetting and how do you prevent it?**
**Answer:** 
Catastrophic forgetting: Fine-tuned model forgets original capabilities.

**Prevention strategies**:
1. **Regularization**:
   - Elastic Weight Consolidation (EWC): Constrain important weights
   - L2 regularization toward original weights

2. **Replay methods**:
   - Mix original training data with new data
   - Store subset of original examples

3. **Parameter-efficient tuning**:
   - LoRA, adapters: Preserve base model

4. **Progressive networks**: Add new parameters, keep old frozen

5. **Knowledge distillation**: Regularize toward original model outputs

6. **Multi-task learning**: Train on old and new tasks simultaneously

**Best practice**: Start with small LR, short training, monitor validation on original tasks

#### 11. **How do you prepare data for fine-tuning?**
**Answer:** 
**Data requirements**:
- Quality > Quantity (high-quality examples more important)
- Diversity (cover edge cases)
- Balance (for classification)
- Minimum: 50-100 examples (depends on task)
- Ideal: 1000+ examples

**Data format**:
```json
{"messages": [
  {"role": "system", "content": "You are a helpful assistant."},
  {"role": "user", "content": "Question here"},
  {"role": "assistant", "content": "Answer here"}
]}
```

**Preparation steps**:
1. Clean and validate data
2. Remove duplicates
3. Balance classes
4. Split train/validation (80-20)
5. Format consistently
6. Add system prompts
7. Validate with model tokenizer

**Common mistakes**: Too much data, low quality, biased data, wrong format

#### 12. **What metrics do you use to evaluate fine-tuned models?**
**Answer:** 
**Task-specific**:
- Classification: Accuracy, F1, precision, recall, confusion matrix
- Generation: BLEU, ROUGE, perplexity
- Q&A: Exact match, F1

**General quality**:
- Validation loss
- Human evaluation (quality, helpfulness, safety)
- A/B testing vs baseline

**Safety**:
- Toxicity scores
- Bias evaluation
- Hallucination rate

**Business metrics**:
- User satisfaction
- Task completion rate
- Cost per query

**Monitoring**:
- Track metrics over time
- Test on held-out evaluation set
- Regular human review

### Prompt Engineering Questions

#### 13. **What are best practices for prompt engineering?**
**Answer:** 
**Structure**:
1. Clear instructions
2. Provide context
3. Specify output format
4. Include examples (few-shot)
5. Define constraints

**Techniques**:
- **Zero-shot**: Just instructions
- **Few-shot**: Include examples
- **Chain-of-thought**: "Let's think step by step"
- **Role-playing**: "You are an expert in..."
- **Constraints**: "Answer in 50 words", "Use bullet points"

**Example**:
```
Role: You are a medical expert.
Task: Summarize this patient report.
Format: Bullet points, max 5 items.
Constraints: Use medical terminology.

Report: [text]

Summary:
```

**Iteration**: Test, analyze failures, refine prompts

#### 14. **What is chain-of-thought (CoT) prompting?**
**Answer:** 
CoT encourages models to show reasoning steps:

**Basic CoT**: "Let's think step by step"

**Few-shot CoT**: Provide examples with reasoning:
```
Q: Roger has 5 balls. He buys 2 more. How many does he have?
A: Roger starts with 5 balls. He buys 2 more balls. 5 + 2 = 7. 
Answer: 7

Q: [Your question]
A: Let's think step by step.
```

**Zero-shot CoT**: Just add "Let's think step by step" or "Explain your reasoning"

**Benefits**:
- Better accuracy on reasoning tasks
- Explainability
- Catches errors in reasoning

**When to use**: Math, logic, multi-step reasoning, complex problems

**Variants**: Self-consistency (sample multiple reasoning paths), tree-of-thoughts

#### 15. **How do you handle hallucinations in prompt-based models?**
**Answer:** 
**Prevention**:
1. **Explicit instructions**: "Only use provided information", "Say 'I don't know' if unsure"
2. **RAG**: Ground responses in retrieved facts
3. **Few-shot examples**: Show desired behavior (citations, admitting uncertainty)
4. **Constraints**: "Quote from text", "Provide source"
5. **Temperature**: Lower temperature (0.1-0.3) for factual tasks

**Detection**:
1. Self-consistency check: Generate multiple outputs, check consistency
2. Self-verification: Ask model to fact-check its own output
3. External validation: Check against knowledge base

**Mitigation**:
1. Iterative refinement: "Are you sure? Check again"
2. Confidence scoring: Request confidence levels
3. Human-in-the-loop: Flag uncertain responses for review

**Prompt template**:
```
Answer based only on the context below.
If you cannot answer, say "I don't know."
Cite specific parts of the context.

Context: [text]
Question: [question]
```

---

## Interview Scoring Framework

### Scoring Rubric (for each phase)

**Score 1-5**:
- **1**: Cannot answer basic questions, fundamental gaps
- **2**: Limited understanding, struggles with concepts
- **3**: Adequate knowledge, answers basic questions correctly
- **4**: Strong knowledge, handles medium questions well, some depth
- **5**: Excellent expertise, handles hard questions, deep understanding

### Red Flags
- Cannot explain basic ML concepts (overfitting, bias-variance)
- No hands-on experience with any ML framework
- Cannot discuss any real project in detail
- Unable to explain trade-offs or limitations
- Only theoretical knowledge, no practical application

### Green Flags
- Explains concepts clearly with examples
- Discusses trade-offs and best practices
- Relevant project experience
- Asks clarifying questions
- Mentions recent developments/papers
- Understands company's tech stack (RAG, fine-tuning, prompts)
- Pragmatic approach to problem-solving

---

## Additional Resources

### Key Topics to Deep Dive Based on Role
- **ML Engineer**: Model deployment, MLOps, scalability, monitoring
- **Research Scientist**: Latest papers, theoretical foundations, novel approaches
- **Applied Scientist**: Business impact, A/B testing, product integration
- **Data Scientist**: EDA, feature engineering, business metrics

### Recommended Follow-up Questions
- "Can you explain that in simpler terms?"
- "How would you implement this in production?"
- "What are the trade-offs of this approach?"
- "Have you used this in a project? Tell me about it."
- "What would you do differently if you had to do it again?"

---

*End of AI/ML Interview Guide*
