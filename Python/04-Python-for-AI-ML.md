# Python for AI & Machine Learning — Interview Questions & Answers

## 1. What are the key differences between NumPy arrays and Python lists?

**Answer:**

| Feature | Python List | NumPy Array |
|---------|-------------|-------------|
| Type | Heterogeneous | Homogeneous |
| Memory | High (object pointers) | Low (contiguous block) |
| Speed | Slow (interpreted loop) | Fast (vectorized C ops) |
| Broadcasting | No | Yes |
| Math operations | No native support | Full support |

```python
import numpy as np

# Speed comparison
lst = list(range(1_000_000))
arr = np.arange(1_000_000)

# List: ~100ms
result_list = [x * 2 for x in lst]

# NumPy: ~2ms (50x faster)
result_np = arr * 2
```

---

## 2. Explain NumPy broadcasting rules.

**Answer:**

Broadcasting allows operations on arrays with different shapes.

**Rules:**
1. If arrays have different dimensions, prepend 1s to the smaller shape
2. Arrays with size 1 along a dimension act as if they have the size of the largest array
3. Arrays must match in every dimension (or one of them must be 1)

```python
import numpy as np

a = np.array([[1, 2, 3],
              [4, 5, 6]])       # Shape: (2, 3)
b = np.array([10, 20, 30])     # Shape: (3,) → broadcast to (2, 3)

print(a + b)
# [[11, 22, 33],
#  [14, 25, 36]]

# Column-wise operation
c = np.array([[100], [200]])   # Shape: (2, 1) → broadcast to (2, 3)
print(a + c)
# [[101, 102, 103],
#  [204, 205, 206]]
```

---

## 3. How does Pandas handle missing data?

**Answer:**

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    'A': [1, 2, np.nan, 4],
    'B': [np.nan, 2, 3, 4],
    'C': ['x', None, 'z', 'w']
})

# Detection
df.isnull()            # Boolean mask
df.isnull().sum()      # Count per column

# Removal
df.dropna()            # Drop rows with any NaN
df.dropna(axis=1)      # Drop columns with any NaN
df.dropna(thresh=2)    # Keep rows with at least 2 non-NaN

# Filling
df.fillna(0)                          # Fill with constant
df['A'].fillna(df['A'].mean())        # Fill with mean
df.fillna(method='ffill')             # Forward fill
df.fillna(method='bfill')             # Backward fill
df.interpolate()                       # Linear interpolation

# For ML: SimpleImputer
from sklearn.impute import SimpleImputer
imputer = SimpleImputer(strategy='mean')  # or 'median', 'most_frequent'
df[['A', 'B']] = imputer.fit_transform(df[['A', 'B']])
```

---

## 4. What is the difference between `loc` and `iloc` in Pandas?

**Answer:**

```python
import pandas as pd

df = pd.DataFrame({
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [25, 30, 35],
    'salary': [50000, 60000, 70000]
}, index=['a', 'b', 'c'])

# loc — label-based (inclusive on both ends)
df.loc['a']                    # Row with index 'a'
df.loc['a':'b', 'name']       # Rows a-b, column 'name'
df.loc[df['age'] > 25]        # Boolean indexing

# iloc — position-based (exclusive on end)
df.iloc[0]                     # First row
df.iloc[0:2, 0:1]             # Rows 0-1, column 0
df.iloc[[0, 2]]               # Rows at positions 0 and 2
```

---

## 5. How do you implement train/test split from scratch?

**Answer:**

```python
import numpy as np

def train_test_split(X, y, test_size=0.2, random_state=None):
    if random_state is not None:
        np.random.seed(random_state)
    
    n = len(X)
    indices = np.random.permutation(n)
    test_count = int(n * test_size)
    
    test_idx = indices[:test_count]
    train_idx = indices[test_count:]
    
    return X[train_idx], X[test_idx], y[train_idx], y[test_idx]

# Stratified split (preserves class distribution)
def stratified_split(X, y, test_size=0.2, random_state=None):
    if random_state is not None:
        np.random.seed(random_state)
    
    classes = np.unique(y)
    train_idx, test_idx = [], []
    
    for cls in classes:
        cls_idx = np.where(y == cls)[0]
        np.random.shuffle(cls_idx)
        n_test = int(len(cls_idx) * test_size)
        test_idx.extend(cls_idx[:n_test])
        train_idx.extend(cls_idx[n_test:])
    
    return X[train_idx], X[test_idx], y[train_idx], y[test_idx]

# Using sklearn
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, stratify=y, random_state=42
)
```

---

## 6. Explain the bias-variance tradeoff.

**Answer:**

- **Bias**: Error from overly simplistic model assumptions (underfitting)
- **Variance**: Error from sensitivity to training data fluctuations (overfitting)
- **Total Error** = Bias² + Variance + Irreducible Error

```
High Bias, Low Variance → Underfitting (linear model on non-linear data)
Low Bias, High Variance  → Overfitting  (deep tree on small dataset)
Optimal                   → Balance between both
```

```python
from sklearn.model_selection import cross_val_score
from sklearn.tree import DecisionTreeClassifier

# Bias-variance diagnosis
for depth in [1, 3, 5, 10, None]:
    model = DecisionTreeClassifier(max_depth=depth)
    
    # Training score (low = high bias)
    model.fit(X_train, y_train)
    train_score = model.score(X_train, y_train)
    
    # Cross-val score (gap with train = high variance)
    cv_scores = cross_val_score(model, X_train, y_train, cv=5)
    
    print(f"Depth={depth}: Train={train_score:.3f}, "
          f"CV={cv_scores.mean():.3f} ± {cv_scores.std():.3f}")
```

---

## 7. How do you implement linear regression from scratch?

**Answer:**

```python
import numpy as np

class LinearRegressionScratch:
    def __init__(self, learning_rate=0.01, n_iterations=1000):
        self.lr = learning_rate
        self.n_iter = n_iterations
        self.weights = None
        self.bias = None
    
    def fit(self, X, y):
        n_samples, n_features = X.shape
        self.weights = np.zeros(n_features)
        self.bias = 0
        
        for _ in range(self.n_iter):
            y_pred = X @ self.weights + self.bias
            
            # Gradients
            dw = (1 / n_samples) * (X.T @ (y_pred - y))
            db = (1 / n_samples) * np.sum(y_pred - y)
            
            # Update
            self.weights -= self.lr * dw
            self.bias -= self.lr * db
    
    def predict(self, X):
        return X @ self.weights + self.bias
    
    def score(self, X, y):
        """R² score"""
        y_pred = self.predict(X)
        ss_res = np.sum((y - y_pred) ** 2)
        ss_tot = np.sum((y - np.mean(y)) ** 2)
        return 1 - (ss_res / ss_tot)

# Normal equation (closed-form solution)
def linear_regression_normal(X, y):
    X_b = np.c_[np.ones(len(X)), X]  # Add bias column
    theta = np.linalg.inv(X_b.T @ X_b) @ X_b.T @ y
    return theta
```

---

## 8. Implement logistic regression from scratch.

**Answer:**

```python
import numpy as np

class LogisticRegressionScratch:
    def __init__(self, learning_rate=0.01, n_iterations=1000):
        self.lr = learning_rate
        self.n_iter = n_iterations
    
    def sigmoid(self, z):
        return 1 / (1 + np.exp(-np.clip(z, -500, 500)))
    
    def fit(self, X, y):
        n_samples, n_features = X.shape
        self.weights = np.zeros(n_features)
        self.bias = 0
        
        for _ in range(self.n_iter):
            z = X @ self.weights + self.bias
            y_pred = self.sigmoid(z)
            
            dw = (1 / n_samples) * (X.T @ (y_pred - y))
            db = (1 / n_samples) * np.sum(y_pred - y)
            
            self.weights -= self.lr * dw
            self.bias -= self.lr * db
    
    def predict_proba(self, X):
        return self.sigmoid(X @ self.weights + self.bias)
    
    def predict(self, X, threshold=0.5):
        return (self.predict_proba(X) >= threshold).astype(int)
```

---

## 9. What are the key evaluation metrics for classification?

**Answer:**

```python
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score,
    f1_score, confusion_matrix, classification_report,
    roc_auc_score
)

y_true = [1, 0, 1, 1, 0, 1, 0, 0]
y_pred = [1, 0, 0, 1, 0, 1, 1, 0]

print(f"Accuracy:  {accuracy_score(y_true, y_pred):.3f}")
print(f"Precision: {precision_score(y_true, y_pred):.3f}")
print(f"Recall:    {recall_score(y_true, y_pred):.3f}")
print(f"F1 Score:  {f1_score(y_true, y_pred):.3f}")

print(confusion_matrix(y_true, y_pred))
# [[TN, FP],
#  [FN, TP]]

print(classification_report(y_true, y_pred))
```

| Metric | Formula | When to use |
|--------|---------|-------------|
| Accuracy | (TP+TN)/(Total) | Balanced classes |
| Precision | TP/(TP+FP) | Minimize false positives (spam) |
| Recall | TP/(TP+FN) | Minimize false negatives (cancer) |
| F1 | 2·P·R/(P+R) | Imbalanced classes |
| AUC-ROC | Area under ROC curve | Ranking quality |

---

## 10. Explain cross-validation and its types.

**Answer:**

```python
from sklearn.model_selection import (
    KFold, StratifiedKFold, LeaveOneOut,
    cross_val_score, TimeSeriesSplit
)

# K-Fold Cross Validation
kfold = KFold(n_splits=5, shuffle=True, random_state=42)
scores = cross_val_score(model, X, y, cv=kfold, scoring='accuracy')
print(f"CV Accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Stratified K-Fold (preserves class distribution)
skfold = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

# Time Series Split (respects temporal order)
tscv = TimeSeriesSplit(n_splits=5)
for train_idx, test_idx in tscv.split(X):
    X_train, X_test = X[train_idx], X[test_idx]

# Leave-One-Out (n folds for n samples)
loo = LeaveOneOut()
```

---

## 11. How do you handle imbalanced datasets?

**Answer:**

```python
# 1. Resampling techniques
from sklearn.utils import resample

# Oversampling minority class
X_minority_upsampled = resample(
    X_minority, replace=True,
    n_samples=len(X_majority), random_state=42
)

# 2. SMOTE (Synthetic Minority Over-sampling)
from imblearn.over_sampling import SMOTE
smote = SMOTE(random_state=42)
X_resampled, y_resampled = smote.fit_resample(X, y)

# 3. Class weights
from sklearn.ensemble import RandomForestClassifier
model = RandomForestClassifier(class_weight='balanced')

# 4. Custom class weights
from sklearn.utils.class_weight import compute_class_weight
weights = compute_class_weight('balanced', classes=np.unique(y), y=y)

# 5. Threshold adjustment
y_proba = model.predict_proba(X_test)[:, 1]
y_pred = (y_proba >= 0.3).astype(int)  # Lower threshold for minority
```

---

## 12. What is feature scaling and when do you need it?

**Answer:**

```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler, RobustScaler

# StandardScaler: z = (x - mean) / std — for normal distributions
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)  # Use train stats!

# MinMaxScaler: x_norm = (x - min) / (max - min) — bounded [0, 1]
minmax = MinMaxScaler()
X_normalized = minmax.fit_transform(X_train)

# RobustScaler: uses median and IQR — robust to outliers
robust = RobustScaler()
X_robust = robust.fit_transform(X_train)
```

**Needs scaling:** SVM, KNN, PCA, Neural Networks, Logistic Regression, K-Means

**Doesn't need scaling:** Decision Trees, Random Forest, Gradient Boosting, Naive Bayes

---

## 13. Explain Principal Component Analysis (PCA).

**Answer:**

```python
import numpy as np
from sklearn.decomposition import PCA

# PCA from scratch
def pca_scratch(X, n_components):
    # 1. Center the data
    X_centered = X - X.mean(axis=0)
    
    # 2. Compute covariance matrix
    cov_matrix = np.cov(X_centered.T)
    
    # 3. Eigendecomposition
    eigenvalues, eigenvectors = np.linalg.eigh(cov_matrix)
    
    # 4. Sort by eigenvalue (descending)
    idx = np.argsort(eigenvalues)[::-1]
    eigenvectors = eigenvectors[:, idx]
    
    # 5. Project onto top k eigenvectors
    W = eigenvectors[:, :n_components]
    return X_centered @ W

# Using sklearn
pca = PCA(n_components=0.95)  # Keep 95% variance
X_reduced = pca.fit_transform(X)
print(f"Components: {pca.n_components_}")
print(f"Explained variance: {pca.explained_variance_ratio_}")
```

---

## 14. How do you implement K-Means clustering from scratch?

**Answer:**

```python
import numpy as np

class KMeansScratch:
    def __init__(self, n_clusters=3, max_iters=100, random_state=42):
        self.k = n_clusters
        self.max_iters = max_iters
        self.random_state = random_state
    
    def fit(self, X):
        np.random.seed(self.random_state)
        n_samples = X.shape[0]
        
        # Random initialization
        idx = np.random.choice(n_samples, self.k, replace=False)
        self.centroids = X[idx]
        
        for _ in range(self.max_iters):
            # Assign clusters
            distances = np.linalg.norm(X[:, np.newaxis] - self.centroids, axis=2)
            self.labels = np.argmin(distances, axis=1)
            
            # Update centroids
            new_centroids = np.array([
                X[self.labels == k].mean(axis=0) for k in range(self.k)
            ])
            
            # Check convergence
            if np.allclose(self.centroids, new_centroids):
                break
            self.centroids = new_centroids
        
        return self
    
    def predict(self, X):
        distances = np.linalg.norm(X[:, np.newaxis] - self.centroids, axis=2)
        return np.argmin(distances, axis=1)
    
    def inertia(self, X):
        distances = np.linalg.norm(X - self.centroids[self.labels], axis=1)
        return np.sum(distances ** 2)
```

---

## 15. What is the difference between bagging and boosting?

**Answer:**

| Aspect | Bagging | Boosting |
|--------|---------|----------|
| Training | Parallel (independent) | Sequential (dependent) |
| Sampling | Bootstrap (with replacement) | Weighted samples |
| Focus | Reduce variance | Reduce bias |
| Example | Random Forest | XGBoost, AdaBoost, LightGBM |
| Overfitting | Resistant | Can overfit |

```python
from sklearn.ensemble import (
    RandomForestClassifier,      # Bagging
    GradientBoostingClassifier,  # Boosting
    AdaBoostClassifier           # Boosting
)

# Bagging
rf = RandomForestClassifier(
    n_estimators=100, max_depth=10,
    max_features='sqrt', random_state=42
)

# Boosting
gb = GradientBoostingClassifier(
    n_estimators=100, learning_rate=0.1,
    max_depth=3, random_state=42
)

# XGBoost
import xgboost as xgb
xgb_model = xgb.XGBClassifier(
    n_estimators=100, learning_rate=0.1,
    max_depth=6, random_state=42
)
```

---

## 16. How do you perform hyperparameter tuning?

**Answer:**

```python
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV
from sklearn.ensemble import RandomForestClassifier

# Grid Search (exhaustive)
param_grid = {
    'n_estimators': [100, 200, 300],
    'max_depth': [5, 10, 20, None],
    'min_samples_split': [2, 5, 10]
}
grid_search = GridSearchCV(
    RandomForestClassifier(), param_grid,
    cv=5, scoring='f1', n_jobs=-1, verbose=1
)
grid_search.fit(X_train, y_train)
print(f"Best params: {grid_search.best_params_}")
print(f"Best score: {grid_search.best_score_:.3f}")

# Random Search (faster for large spaces)
from scipy.stats import randint, uniform
param_dist = {
    'n_estimators': randint(50, 500),
    'max_depth': randint(3, 30),
    'learning_rate': uniform(0.001, 0.3)
}
random_search = RandomizedSearchCV(
    model, param_dist, n_iter=100,
    cv=5, scoring='f1', random_state=42
)

# Optuna (Bayesian optimization)
import optuna

def objective(trial):
    params = {
        'n_estimators': trial.suggest_int('n_estimators', 50, 500),
        'max_depth': trial.suggest_int('max_depth', 3, 30),
        'learning_rate': trial.suggest_float('learning_rate', 1e-4, 0.3, log=True)
    }
    model = xgb.XGBClassifier(**params)
    score = cross_val_score(model, X, y, cv=5, scoring='f1').mean()
    return score

study = optuna.create_study(direction='maximize')
study.optimize(objective, n_trials=100)
print(study.best_params)
```

---

## 17. Explain regularization (L1, L2, Elastic Net).

**Answer:**

```python
from sklearn.linear_model import Lasso, Ridge, ElasticNet

# L1 (Lasso) — promotes sparsity (feature selection)
# Loss = MSE + α * Σ|w_i|
lasso = Lasso(alpha=0.1)
lasso.fit(X_train, y_train)
print(f"Non-zero features: {np.sum(lasso.coef_ != 0)}")

# L2 (Ridge) — shrinks coefficients  
# Loss = MSE + α * Σw_i²
ridge = Ridge(alpha=1.0)

# Elastic Net — combines L1 and L2
# Loss = MSE + α * (ρ * Σ|w_i| + (1-ρ) * Σw_i²)
elastic = ElasticNet(alpha=0.1, l1_ratio=0.5)
```

| Regularization | Penalty | Effect | Use case |
|---------------|---------|--------|----------|
| L1 (Lasso) | \|w\| | Sparse weights (feature selection) | High-dimensional, few relevant features |
| L2 (Ridge) | w² | Small weights (no zeros) | Multicollinearity |
| Elastic Net | Both | Balance between L1/L2 | Many correlated features |

---

## 18. How do you build a simple neural network with PyTorch?

**Answer:**

```python
import torch
import torch.nn as nn
import torch.optim as optim

class SimpleNN(nn.Module):
    def __init__(self, input_size, hidden_size, num_classes):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(input_size, hidden_size),
            nn.ReLU(),
            nn.Dropout(0.3),
            nn.Linear(hidden_size, hidden_size // 2),
            nn.ReLU(),
            nn.Linear(hidden_size // 2, num_classes)
        )
    
    def forward(self, x):
        return self.net(x)

# Training loop
model = SimpleNN(input_size=784, hidden_size=128, num_classes=10)
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=0.001)

for epoch in range(10):
    model.train()
    for X_batch, y_batch in train_loader:
        optimizer.zero_grad()
        outputs = model(X_batch)
        loss = criterion(outputs, y_batch)
        loss.backward()
        optimizer.step()
    
    # Evaluation
    model.eval()
    with torch.no_grad():
        correct = total = 0
        for X_batch, y_batch in test_loader:
            outputs = model(X_batch)
            _, predicted = torch.max(outputs, 1)
            total += y_batch.size(0)
            correct += (predicted == y_batch).sum().item()
    print(f"Epoch {epoch+1}, Accuracy: {correct/total:.3f}")
```

---

## 19. What is the attention mechanism?

**Answer:**

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import math

class SelfAttention(nn.Module):
    def __init__(self, d_model, n_heads):
        super().__init__()
        self.d_model = d_model
        self.n_heads = n_heads
        self.d_k = d_model // n_heads
        
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)
    
    def forward(self, x):
        batch_size, seq_len, _ = x.shape
        
        Q = self.W_q(x).view(batch_size, seq_len, self.n_heads, self.d_k).transpose(1, 2)
        K = self.W_k(x).view(batch_size, seq_len, self.n_heads, self.d_k).transpose(1, 2)
        V = self.W_v(x).view(batch_size, seq_len, self.n_heads, self.d_k).transpose(1, 2)
        
        # Scaled dot-product attention
        scores = (Q @ K.transpose(-2, -1)) / math.sqrt(self.d_k)
        attn_weights = F.softmax(scores, dim=-1)
        context = attn_weights @ V
        
        # Concatenate heads
        context = context.transpose(1, 2).contiguous().view(batch_size, seq_len, self.d_model)
        return self.W_o(context)
```

**Key concepts:**
- **Query (Q):** What am I looking for?
- **Key (K):** What do I contain?
- **Value (V):** What do I provide?
- **Score:** Attention(Q, K, V) = softmax(QK^T / √d_k) · V

---

## 20. How do you implement a custom PyTorch Dataset and DataLoader?

**Answer:**

```python
import torch
from torch.utils.data import Dataset, DataLoader

class CustomDataset(Dataset):
    def __init__(self, X, y, transform=None):
        self.X = torch.FloatTensor(X)
        self.y = torch.LongTensor(y)
        self.transform = transform
    
    def __len__(self):
        return len(self.X)
    
    def __getitem__(self, idx):
        sample = self.X[idx]
        label = self.y[idx]
        if self.transform:
            sample = self.transform(sample)
        return sample, label

# Usage
dataset = CustomDataset(X_train, y_train)
loader = DataLoader(
    dataset,
    batch_size=32,
    shuffle=True,
    num_workers=4,
    pin_memory=True  # Faster GPU transfer
)

for batch_X, batch_y in loader:
    outputs = model(batch_X)
    loss = criterion(outputs, batch_y)
```

---

## 21. Explain gradient descent variants.

**Answer:**

```python
# Batch Gradient Descent
# Uses ALL samples per update — slow but stable
for epoch in range(epochs):
    gradient = compute_gradient(X, y, weights)
    weights -= lr * gradient

# Stochastic Gradient Descent (SGD)
# Uses ONE sample per update — fast but noisy
for epoch in range(epochs):
    for xi, yi in zip(X, y):
        gradient = compute_gradient(xi, yi, weights)
        weights -= lr * gradient

# Mini-Batch GD (most common)
# Uses a batch of samples
for epoch in range(epochs):
    for batch in get_batches(X, y, batch_size=32):
        gradient = compute_gradient(batch, weights)
        weights -= lr * gradient
```

| Optimizer | Key Feature |
|-----------|-------------|
| SGD | Basic gradient descent |
| SGD + Momentum | Accelerates in consistent direction |
| RMSprop | Adaptive learning rate per parameter |
| Adam | Momentum + RMSprop (most popular) |
| AdamW | Adam + weight decay (better regularization) |

---

## 22. What is transfer learning and how do you implement it?

**Answer:**

```python
import torch
import torch.nn as nn
from torchvision import models

# Load pretrained model
resnet = models.resnet50(pretrained=True)

# Freeze all layers
for param in resnet.parameters():
    param.requires_grad = False

# Replace the final classification layer
num_classes = 10
resnet.fc = nn.Sequential(
    nn.Linear(resnet.fc.in_features, 256),
    nn.ReLU(),
    nn.Dropout(0.3),
    nn.Linear(256, num_classes)
)

# Only train the new layers
optimizer = torch.optim.Adam(resnet.fc.parameters(), lr=0.001)

# Fine-tuning: unfreeze some layers later
for param in resnet.layer4.parameters():
    param.requires_grad = True
```

---

## 23. How do you handle categorical features?

**Answer:**

```python
import pandas as pd
from sklearn.preprocessing import LabelEncoder, OneHotEncoder, OrdinalEncoder

df = pd.DataFrame({
    'color': ['red', 'blue', 'green', 'red'],
    'size': ['S', 'M', 'L', 'XL'],
    'city': ['NYC', 'LA', 'NYC', 'SF']
})

# Label Encoding (ordinal data)
le = LabelEncoder()
df['size_encoded'] = le.fit_transform(df['size'])  # 0, 1, 2, 3

# One-Hot Encoding (nominal data)
df_encoded = pd.get_dummies(df, columns=['color', 'city'], drop_first=True)

# Ordinal Encoding (with explicit order)
oe = OrdinalEncoder(categories=[['S', 'M', 'L', 'XL']])
df['size_ordinal'] = oe.fit_transform(df[['size']])

# Target Encoding (for high cardinality)
def target_encode(df, col, target, smoothing=10):
    global_mean = df[target].mean()
    stats = df.groupby(col)[target].agg(['mean', 'count'])
    smooth = (stats['count'] * stats['mean'] + smoothing * global_mean) / (stats['count'] + smoothing)
    return df[col].map(smooth)
```

---

## 24. What are word embeddings (Word2Vec, GloVe)?

**Answer:**

```python
# Using Gensim Word2Vec
from gensim.models import Word2Vec

sentences = [
    ["machine", "learning", "is", "fun"],
    ["deep", "learning", "uses", "neural", "networks"],
    ["natural", "language", "processing", "with", "python"]
]

model = Word2Vec(
    sentences, vector_size=100,
    window=5, min_count=1, workers=4,
    sg=1  # 1=Skip-gram, 0=CBOW
)

# Get word vector
vector = model.wv['learning']  # 100-dim vector

# Find similar words
model.wv.most_similar('learning', topn=5)

# Using pretrained embeddings (PyTorch)
import torch.nn as nn

embedding = nn.Embedding(
    num_embeddings=10000,  # Vocabulary size
    embedding_dim=300      # Embedding dimension
)
# Load pretrained weights (e.g., GloVe)
# embedding.weight = nn.Parameter(pretrained_weights)
```

---

## 25. How do you implement a simple recommendation system?

**Answer:**

```python
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

# Content-Based Filtering
def content_based_recommend(item_features, user_profile, top_k=5):
    """
    item_features: (n_items, n_features) matrix
    user_profile: (n_features,) vector
    """
    similarities = cosine_similarity([user_profile], item_features)[0]
    top_indices = np.argsort(similarities)[::-1][:top_k]
    return top_indices, similarities[top_indices]

# Collaborative Filtering (User-User)
def collaborative_recommend(user_item_matrix, user_id, top_k=5):
    """
    user_item_matrix: (n_users, n_items) rating matrix
    """
    user_sim = cosine_similarity(user_item_matrix)
    similar_users = np.argsort(user_sim[user_id])[::-1][1:top_k+1]
    
    # Weighted average of similar users' ratings
    weights = user_sim[user_id][similar_users]
    weighted_ratings = (weights[:, np.newaxis] * user_item_matrix[similar_users]).sum(axis=0)
    weighted_ratings /= weights.sum()
    
    # Recommend items not yet rated
    unrated = user_item_matrix[user_id] == 0
    recommendations = np.where(unrated, weighted_ratings, -np.inf)
    return np.argsort(recommendations)[::-1][:top_k]
```

---

## 26. What is the difference between generative and discriminative models?

**Answer:**

| Aspect | Discriminative | Generative |
|--------|---------------|------------|
| Learns | P(y\|x) — decision boundary | P(x, y) = P(x\|y)·P(y) — data distribution |
| Examples | Logistic Reg, SVM, Neural Nets | Naive Bayes, GMM, GANs, VAE |
| Training data | Needs less | Needs more |
| Outlier detection | Poor | Good |
| New data generation | Cannot | Can |

```python
# Discriminative: Logistic Regression
from sklearn.linear_model import LogisticRegression
disc_model = LogisticRegression()
disc_model.fit(X_train, y_train)
y_pred = disc_model.predict(X_test)

# Generative: Naive Bayes
from sklearn.naive_bayes import GaussianNB
gen_model = GaussianNB()
gen_model.fit(X_train, y_train)
y_pred = gen_model.predict(X_test)
```

---

## 27. How do you implement a pipeline in scikit-learn?

**Answer:**

```python
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.impute import SimpleImputer
from sklearn.ensemble import RandomForestClassifier

# Define preprocessing for different column types
numeric_features = ['age', 'salary', 'experience']
categorical_features = ['department', 'education']

numeric_transformer = Pipeline([
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())
])

categorical_transformer = Pipeline([
    ('imputer', SimpleImputer(strategy='most_frequent')),
    ('encoder', OneHotEncoder(handle_unknown='ignore'))
])

preprocessor = ColumnTransformer([
    ('num', numeric_transformer, numeric_features),
    ('cat', categorical_transformer, categorical_features)
])

# Full pipeline
pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('classifier', RandomForestClassifier(n_estimators=100))
])

# Fit and predict
pipeline.fit(X_train, y_train)
predictions = pipeline.predict(X_test)
score = pipeline.score(X_test, y_test)

# Works with cross-validation and grid search
from sklearn.model_selection import cross_val_score
scores = cross_val_score(pipeline, X, y, cv=5, scoring='accuracy')
```

---

## 28. What is the curse of dimensionality?

**Answer:**

As dimensions increase:
- Data becomes sparse (volume grows exponentially)
- Distance metrics lose meaning (all points become equidistant)
- More data needed to achieve same statistical significance
- Models prone to overfitting

```python
import numpy as np

# Demonstrate: distances converge in high dimensions
def avg_pairwise_distance(n_samples=100, n_dims=2):
    points = np.random.uniform(0, 1, (n_samples, n_dims))
    from sklearn.metrics import pairwise_distances
    dists = pairwise_distances(points)
    return dists[dists > 0].mean(), dists[dists > 0].std()

for d in [2, 10, 100, 1000]:
    mean, std = avg_pairwise_distance(n_dims=d)
    print(f"Dims={d:4d}: mean_dist={mean:.3f}, std={std:.3f}, "
          f"ratio={std/mean:.3f}")
# Ratio (std/mean) decreases → distances become uniform

# Solutions:
# 1. Feature selection
# 2. PCA / dimensionality reduction
# 3. L1 regularization
# 4. Feature engineering
```

---

## 29. How do you save and load ML models?

**Answer:**

```python
# 1. Pickle (basic)
import pickle
with open('model.pkl', 'wb') as f:
    pickle.dump(model, f)
with open('model.pkl', 'rb') as f:
    loaded_model = pickle.load(f)

# 2. Joblib (better for large numpy arrays)
import joblib
joblib.dump(model, 'model.joblib')
loaded_model = joblib.load('model.joblib')

# 3. PyTorch
torch.save(model.state_dict(), 'model.pth')
model = MyModel()
model.load_state_dict(torch.load('model.pth'))

# Save everything (architecture + weights + optimizer)
torch.save({
    'epoch': epoch,
    'model_state_dict': model.state_dict(),
    'optimizer_state_dict': optimizer.state_dict(),
    'loss': loss
}, 'checkpoint.pth')

# 4. ONNX (cross-framework)
import torch.onnx
torch.onnx.export(model, dummy_input, 'model.onnx')

# 5. Hugging Face
from transformers import AutoModel
model.save_pretrained('./my_model')
loaded = AutoModel.from_pretrained('./my_model')
```

---

## 30. What is batch normalization and why is it important?

**Answer:**

```python
import torch.nn as nn

class ConvNet(nn.Module):
    def __init__(self):
        super().__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 32, 3, padding=1),
            nn.BatchNorm2d(32),       # After conv, before activation
            nn.ReLU(),
            nn.Conv2d(32, 64, 3, padding=1),
            nn.BatchNorm2d(64),
            nn.ReLU(),
            nn.AdaptiveAvgPool2d(1)
        )
        self.classifier = nn.Sequential(
            nn.Linear(64, 128),
            nn.BatchNorm1d(128),      # 1D for fully connected
            nn.ReLU(),
            nn.Linear(128, 10)
        )
```

**Benefits:**
- Reduces internal covariate shift
- Allows higher learning rates
- Acts as regularization
- Smoother loss landscape

**Behavior differs in train vs eval mode:**
- Train: Uses batch statistics (mean, variance)
- Eval: Uses running statistics (accumulated during training)

---

## 31. How do you implement early stopping?

**Answer:**

```python
class EarlyStopping:
    def __init__(self, patience=5, min_delta=0.001, mode='min'):
        self.patience = patience
        self.min_delta = min_delta
        self.mode = mode
        self.counter = 0
        self.best_score = None
        self.should_stop = False
    
    def __call__(self, score):
        if self.best_score is None:
            self.best_score = score
        elif self._is_improvement(score):
            self.best_score = score
            self.counter = 0
        else:
            self.counter += 1
            if self.counter >= self.patience:
                self.should_stop = True
    
    def _is_improvement(self, score):
        if self.mode == 'min':
            return score < self.best_score - self.min_delta
        return score > self.best_score + self.min_delta

# Usage
early_stopping = EarlyStopping(patience=5)
for epoch in range(100):
    train_loss = train_one_epoch()
    val_loss = validate()
    
    early_stopping(val_loss)
    if early_stopping.should_stop:
        print(f"Early stopping at epoch {epoch}")
        break
```

---

## 32. What are GANs (Generative Adversarial Networks)?

**Answer:**

```python
import torch
import torch.nn as nn

class Generator(nn.Module):
    def __init__(self, latent_dim, img_dim):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(latent_dim, 256),
            nn.LeakyReLU(0.2),
            nn.Linear(256, 512),
            nn.LeakyReLU(0.2),
            nn.Linear(512, img_dim),
            nn.Tanh()
        )
    
    def forward(self, z):
        return self.net(z)

class Discriminator(nn.Module):
    def __init__(self, img_dim):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(img_dim, 512),
            nn.LeakyReLU(0.2),
            nn.Linear(512, 256),
            nn.LeakyReLU(0.2),
            nn.Linear(256, 1),
            nn.Sigmoid()
        )
    
    def forward(self, x):
        return self.net(x)

# Training
G = Generator(latent_dim=100, img_dim=784)
D = Discriminator(img_dim=784)
criterion = nn.BCELoss()

for epoch in range(epochs):
    for real_images, _ in dataloader:
        # Train Discriminator
        z = torch.randn(batch_size, 100)
        fake_images = G(z)
        
        d_loss_real = criterion(D(real_images), torch.ones(batch_size, 1))
        d_loss_fake = criterion(D(fake_images.detach()), torch.zeros(batch_size, 1))
        d_loss = d_loss_real + d_loss_fake
        
        # Train Generator
        g_loss = criterion(D(G(z)), torch.ones(batch_size, 1))
```

---

## 33. How do you use Hugging Face Transformers?

**Answer:**

```python
from transformers import (
    AutoTokenizer, AutoModel,
    AutoModelForSequenceClassification,
    pipeline
)

# Quick: Pipeline API
classifier = pipeline("sentiment-analysis")
result = classifier("I love machine learning!")
print(result)  # [{'label': 'POSITIVE', 'score': 0.9998}]

# Detailed: Tokenizer + Model
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
model = AutoModelForSequenceClassification.from_pretrained(
    "bert-base-uncased", num_labels=2
)

inputs = tokenizer("Hello, world!", return_tensors="pt",
                    padding=True, truncation=True, max_length=128)
outputs = model(**inputs)
logits = outputs.logits

# Fine-tuning
from transformers import Trainer, TrainingArguments

training_args = TrainingArguments(
    output_dir='./results',
    num_train_epochs=3,
    per_device_train_batch_size=16,
    learning_rate=2e-5,
    weight_decay=0.01,
    evaluation_strategy='epoch'
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset
)
trainer.train()
```

---

## 34. Explain the Transformer architecture.

**Answer:**

```
Encoder-Decoder Architecture:

Input → [Embedding + Positional Encoding]
      → [Multi-Head Self-Attention]
      → [Add & LayerNorm]
      → [Feed-Forward Network]
      → [Add & LayerNorm]
      → (repeat N times)
      → Output

Key Components:
1. Self-Attention: Relate all positions in sequence
2. Multi-Head: Multiple attention patterns in parallel  
3. Positional Encoding: Inject position information
4. Feed-Forward: Two linear layers with activation
5. Layer Normalization: Stabilize training
6. Residual Connections: Enable deep networks
```

```python
class TransformerBlock(nn.Module):
    def __init__(self, d_model, n_heads, d_ff, dropout=0.1):
        super().__init__()
        self.attention = nn.MultiheadAttention(d_model, n_heads, dropout=dropout)
        self.norm1 = nn.LayerNorm(d_model)
        self.norm2 = nn.LayerNorm(d_model)
        self.ff = nn.Sequential(
            nn.Linear(d_model, d_ff),
            nn.GELU(),
            nn.Linear(d_ff, d_model),
            nn.Dropout(dropout)
        )
        self.dropout = nn.Dropout(dropout)
    
    def forward(self, x):
        # Self-attention with residual
        attn_out, _ = self.attention(x, x, x)
        x = self.norm1(x + self.dropout(attn_out))
        # Feed-forward with residual
        ff_out = self.ff(x)
        x = self.norm2(x + ff_out)
        return x
```

---

## 35. What is RAG (Retrieval-Augmented Generation)?

**Answer:**

```python
# Simplified RAG pipeline
from sentence_transformers import SentenceTransformer
import numpy as np

class SimpleRAG:
    def __init__(self, model_name='all-MiniLM-L6-v2'):
        self.encoder = SentenceTransformer(model_name)
        self.documents = []
        self.embeddings = None
    
    def add_documents(self, docs):
        self.documents.extend(docs)
        self.embeddings = self.encoder.encode(self.documents)
    
    def retrieve(self, query, top_k=3):
        query_embedding = self.encoder.encode([query])
        # Cosine similarity
        similarities = np.dot(self.embeddings, query_embedding.T).flatten()
        top_indices = np.argsort(similarities)[::-1][:top_k]
        return [(self.documents[i], similarities[i]) for i in top_indices]
    
    def generate(self, query, llm_client):
        context = self.retrieve(query)
        context_text = "\n".join([doc for doc, _ in context])
        
        prompt = f"""Based on the following context, answer the question.
        
Context:
{context_text}

Question: {query}
Answer:"""
        
        return llm_client.generate(prompt)
```

**RAG Components:**
1. **Indexing:** Chunk documents → embed → store in vector DB
2. **Retrieval:** Query → embed → similarity search → top-k docs
3. **Generation:** Context + query → LLM → answer

---

## 36. How do you implement data augmentation?

**Answer:**

```python
# Image augmentation (torchvision)
from torchvision import transforms

train_transform = transforms.Compose([
    transforms.RandomHorizontalFlip(p=0.5),
    transforms.RandomRotation(15),
    transforms.RandomResizedCrop(224, scale=(0.8, 1.0)),
    transforms.ColorJitter(brightness=0.2, contrast=0.2),
    transforms.RandomAffine(degrees=0, translate=(0.1, 0.1)),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406],
                         std=[0.229, 0.224, 0.225])
])

# Text augmentation
import random

def text_augment(text, p=0.1):
    words = text.split()
    augmented = []
    for word in words:
        r = random.random()
        if r < p:
            continue            # Random deletion
        elif r < 2 * p:
            augmented.extend([word, word])  # Duplication
        else:
            augmented.append(word)
    random.shuffle(augmented)  # Optional: shuffle
    return ' '.join(augmented)

# Tabular augmentation: SMOTE (see Q11)
```

---

## 37. What is the difference between CNN and RNN?

**Answer:**

| Aspect | CNN | RNN |
|--------|-----|-----|
| Input | Fixed-size (images, grids) | Sequential (text, time series) |
| Key operation | Convolution (local patterns) | Recurrence (memory over time) |
| Parameter sharing | Across spatial positions | Across time steps |
| Parallelizable | Yes | No (sequential by nature) |
| Long-range deps | Limited by receptive field | Theoretically unlimited |
| Variants | ResNet, VGG, Inception | LSTM, GRU, Bi-RNN |

```python
# CNN for image classification
class CNN(nn.Module):
    def __init__(self):
        super().__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 32, 3, padding=1), nn.ReLU(), nn.MaxPool2d(2),
            nn.Conv2d(32, 64, 3, padding=1), nn.ReLU(), nn.MaxPool2d(2),
        )
        self.classifier = nn.Linear(64 * 8 * 8, 10)

# RNN for sequence classification
class RNN(nn.Module):
    def __init__(self, vocab_size, embed_dim, hidden_dim, output_dim):
        super().__init__()
        self.embedding = nn.Embedding(vocab_size, embed_dim)
        self.lstm = nn.LSTM(embed_dim, hidden_dim, batch_first=True, bidirectional=True)
        self.fc = nn.Linear(hidden_dim * 2, output_dim)
    
    def forward(self, x):
        embedded = self.embedding(x)
        output, (hidden, cell) = self.lstm(embedded)
        hidden = torch.cat([hidden[-2], hidden[-1]], dim=1)
        return self.fc(hidden)
```

---

## 38. How do you implement tokenization for NLP?

**Answer:**

```python
# Basic tokenization
import re

def word_tokenize(text):
    return re.findall(r'\b\w+\b', text.lower())

# BPE-style (simplified)
from collections import Counter

def build_vocab(texts, vocab_size=1000):
    word_freq = Counter()
    for text in texts:
        word_freq.update(word_tokenize(text))
    return dict(word_freq.most_common(vocab_size))

# Using Hugging Face tokenizers
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
tokens = tokenizer("Hello, how are you?")
print(tokens)
# {'input_ids': [101, 7592, 1010, 2129, 2024, 2017, 1029, 102],
#  'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1]}

# Decode back
text = tokenizer.decode(tokens['input_ids'])
print(text)  # "[CLS] hello, how are you? [SEP]"

# Padding and truncation
batch = tokenizer(
    ["short text", "this is a much longer text that needs truncation"],
    padding=True, truncation=True, max_length=10, return_tensors="pt"
)
```

---

## 39. What are common loss functions and when to use them?

**Answer:**

```python
import torch.nn as nn

# Regression
mse_loss = nn.MSELoss()        # Mean Squared Error — standard
mae_loss = nn.L1Loss()         # Mean Absolute Error — robust to outliers
huber_loss = nn.HuberLoss()    # Smooth L1 — blend of MSE and MAE

# Binary Classification
bce_loss = nn.BCELoss()                    # After sigmoid
bce_logits = nn.BCEWithLogitsLoss()        # More stable (includes sigmoid)

# Multi-class Classification
ce_loss = nn.CrossEntropyLoss()            # Includes softmax
nll_loss = nn.NLLLoss()                    # After log-softmax

# Contrastive / Similarity
cosine_loss = nn.CosineEmbeddingLoss()     # Embedding similarity
triplet_loss = nn.TripletMarginLoss()      # Anchor, positive, negative
```

| Task | Loss Function | When |
|------|--------------|------|
| Regression | MSE | Normal errors |
| Regression | Huber / MAE | Outliers present |
| Binary class | BCE with Logits | Two classes |
| Multi-class | CrossEntropy | Mutually exclusive classes |
| Multi-label | BCE with Logits | Multiple labels per sample |
| Ranking | Triplet / Contrastive | Embedding learning |

---

## 40. How do you implement model interpretability/explainability?

**Answer:**

```python
# 1. Feature Importance (tree-based models)
from sklearn.ensemble import RandomForestClassifier
model = RandomForestClassifier().fit(X_train, y_train)
importances = model.feature_importances_
for name, imp in sorted(zip(feature_names, importances), key=lambda x: -x[1]):
    print(f"{name}: {imp:.4f}")

# 2. SHAP (SHapley Additive exPlanations)
import shap

explainer = shap.Explainer(model, X_train)
shap_values = explainer(X_test)

# Global feature importance
shap.summary_plot(shap_values, X_test, feature_names=feature_names)

# Single prediction explanation
shap.waterfall_plot(shap_values[0])

# 3. LIME (Local Interpretable Model-agnostic Explanations)
from lime.lime_tabular import LimeTabularExplainer

explainer = LimeTabularExplainer(
    X_train, feature_names=feature_names,
    class_names=['negative', 'positive']
)
exp = explainer.explain_instance(X_test[0], model.predict_proba)
exp.show_in_notebook()

# 4. Permutation Importance
from sklearn.inspection import permutation_importance
perm_imp = permutation_importance(model, X_test, y_test, n_repeats=10)
```

---

## 41. What is the vanishing/exploding gradient problem?

**Answer:**

- **Vanishing:** Gradients shrink to ~0 during backprop through many layers → early layers don't learn
- **Exploding:** Gradients grow exponentially → unstable training, NaN values

```python
# Solutions for vanishing gradients:

# 1. ReLU activation (instead of sigmoid/tanh)
nn.ReLU()  # Gradient is 1 for positive values

# 2. Residual connections (skip connections)
class ResidualBlock(nn.Module):
    def __init__(self, dim):
        super().__init__()
        self.layers = nn.Sequential(
            nn.Linear(dim, dim), nn.ReLU(),
            nn.Linear(dim, dim)
        )
    def forward(self, x):
        return x + self.layers(x)  # Skip connection

# 3. Proper weight initialization
nn.init.kaiming_normal_(layer.weight, mode='fan_out', nonlinearity='relu')
nn.init.xavier_uniform_(layer.weight)

# 4. Batch/Layer normalization
nn.BatchNorm1d(dim)
nn.LayerNorm(dim)

# Solutions for exploding gradients:
# Gradient clipping
torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
torch.nn.utils.clip_grad_value_(model.parameters(), clip_value=0.5)
```

---

## 42. How do you deploy an ML model as an API?

**Answer:**

```python
# FastAPI deployment
from fastapi import FastAPI
from pydantic import BaseModel
import joblib
import numpy as np

app = FastAPI()

# Load model at startup
model = joblib.load("model.joblib")
scaler = joblib.load("scaler.joblib")

class PredictionRequest(BaseModel):
    features: list[float]

class PredictionResponse(BaseModel):
    prediction: int
    probability: float

@app.post("/predict", response_model=PredictionResponse)
async def predict(request: PredictionRequest):
    features = np.array(request.features).reshape(1, -1)
    features_scaled = scaler.transform(features)
    prediction = model.predict(features_scaled)[0]
    probability = model.predict_proba(features_scaled).max()
    return PredictionResponse(
        prediction=int(prediction),
        probability=float(probability)
    )

@app.get("/health")
async def health():
    return {"status": "healthy"}

# Run: uvicorn app:app --host 0.0.0.0 --port 8000
```

---

## 43. What is the difference between semantic search and keyword search?

**Answer:**

```python
# Keyword search — TF-IDF
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

corpus = [
    "Machine learning is a subset of AI",
    "Deep learning uses neural networks",
    "Python is great for data science"
]

tfidf = TfidfVectorizer()
tfidf_matrix = tfidf.fit_transform(corpus)

query = tfidf.transform(["artificial intelligence"])
scores = cosine_similarity(query, tfidf_matrix)[0]
# Matches exact words — may miss "AI"

# Semantic search — embeddings
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('all-MiniLM-L6-v2')
corpus_embeddings = model.encode(corpus)
query_embedding = model.encode(["artificial intelligence"])

scores = cosine_similarity(query_embedding, corpus_embeddings)[0]
# Understands meaning — matches "AI" and "machine learning"
```

---

## 44. How does learning rate scheduling work?

**Answer:**

```python
import torch.optim as optim
from torch.optim.lr_scheduler import (
    StepLR, ExponentialLR, CosineAnnealingLR,
    ReduceLROnPlateau, OneCycleLR
)

optimizer = optim.Adam(model.parameters(), lr=0.001)

# Step decay: lr = lr * gamma every step_size epochs
scheduler = StepLR(optimizer, step_size=10, gamma=0.5)

# Exponential decay: lr = lr * gamma^epoch
scheduler = ExponentialLR(optimizer, gamma=0.95)

# Cosine annealing: lr follows cosine curve
scheduler = CosineAnnealingLR(optimizer, T_max=50, eta_min=1e-6)

# Reduce on plateau: reduce when metric stops improving
scheduler = ReduceLROnPlateau(optimizer, mode='min', patience=5, factor=0.5)

# One cycle: ramp up then decay (super-convergence)
scheduler = OneCycleLR(optimizer, max_lr=0.01, total_steps=1000)

# Training loop
for epoch in range(epochs):
    train()
    val_loss = validate()
    
    # For ReduceLROnPlateau
    scheduler.step(val_loss)
    
    # For others
    # scheduler.step()
    
    print(f"LR: {optimizer.param_groups[0]['lr']:.6f}")
```

---

## 45. What are autoencoders?

**Answer:**

```python
class Autoencoder(nn.Module):
    def __init__(self, input_dim, latent_dim):
        super().__init__()
        self.encoder = nn.Sequential(
            nn.Linear(input_dim, 256),
            nn.ReLU(),
            nn.Linear(256, latent_dim)
        )
        self.decoder = nn.Sequential(
            nn.Linear(latent_dim, 256),
            nn.ReLU(),
            nn.Linear(256, input_dim),
            nn.Sigmoid()
        )
    
    def forward(self, x):
        latent = self.encoder(x)
        reconstructed = self.decoder(latent)
        return reconstructed

# Variational Autoencoder (VAE)
class VAE(nn.Module):
    def __init__(self, input_dim, latent_dim):
        super().__init__()
        self.encoder = nn.Linear(input_dim, 256)
        self.fc_mu = nn.Linear(256, latent_dim)
        self.fc_var = nn.Linear(256, latent_dim)
        self.decoder = nn.Sequential(
            nn.Linear(latent_dim, 256),
            nn.ReLU(),
            nn.Linear(256, input_dim),
            nn.Sigmoid()
        )
    
    def reparameterize(self, mu, log_var):
        std = torch.exp(0.5 * log_var)
        eps = torch.randn_like(std)
        return mu + eps * std
    
    def forward(self, x):
        h = torch.relu(self.encoder(x))
        mu, log_var = self.fc_mu(h), self.fc_var(h)
        z = self.reparameterize(mu, log_var)
        return self.decoder(z), mu, log_var
```

**Use cases:** Dimensionality reduction, denoising, anomaly detection, generative models.

---

## 46. How do you handle time series data in Python?

**Answer:**

```python
import pandas as pd
import numpy as np

# Create time series features
def create_time_features(df, date_col):
    df['year'] = df[date_col].dt.year
    df['month'] = df[date_col].dt.month
    df['day_of_week'] = df[date_col].dt.dayofweek
    df['quarter'] = df[date_col].dt.quarter
    df['is_weekend'] = df[date_col].dt.dayofweek.isin([5, 6]).astype(int)
    return df

# Lag features
def create_lag_features(df, target_col, lags=[1, 7, 30]):
    for lag in lags:
        df[f'lag_{lag}'] = df[target_col].shift(lag)
    return df

# Rolling statistics
def create_rolling_features(df, target_col, windows=[7, 30]):
    for w in windows:
        df[f'rolling_mean_{w}'] = df[target_col].rolling(w).mean()
        df[f'rolling_std_{w}'] = df[target_col].rolling(w).std()
    return df

# Train/test split for time series (NO shuffle!)
train_size = int(len(df) * 0.8)
train, test = df[:train_size], df[train_size:]

# Using TimeSeriesSplit for CV
from sklearn.model_selection import TimeSeriesSplit
tscv = TimeSeriesSplit(n_splits=5)
```

---

## 47. What is model distillation?

**Answer:**

Training a smaller "student" model to mimic a larger "teacher" model.

```python
import torch
import torch.nn.functional as F

def distillation_loss(student_logits, teacher_logits, labels,
                      temperature=3.0, alpha=0.5):
    """
    Combines soft targets (from teacher) with hard targets (ground truth).
    """
    # Soft loss: KL divergence between soft predictions
    soft_teacher = F.softmax(teacher_logits / temperature, dim=1)
    soft_student = F.log_softmax(student_logits / temperature, dim=1)
    soft_loss = F.kl_div(soft_student, soft_teacher, reduction='batchmean')
    soft_loss *= temperature ** 2
    
    # Hard loss: standard cross-entropy
    hard_loss = F.cross_entropy(student_logits, labels)
    
    return alpha * soft_loss + (1 - alpha) * hard_loss

# Training loop
teacher.eval()
student.train()
for x, y in dataloader:
    with torch.no_grad():
        teacher_logits = teacher(x)
    student_logits = student(x)
    loss = distillation_loss(student_logits, teacher_logits, y)
    loss.backward()
    optimizer.step()
```

---

## 48. How do you implement vector search with FAISS?

**Answer:**

```python
import faiss
import numpy as np

# Create embeddings
dimension = 768
n_vectors = 100000
embeddings = np.random.random((n_vectors, dimension)).astype('float32')

# Flat index (exact search)
index = faiss.IndexFlatL2(dimension)
index.add(embeddings)

# Search
query = np.random.random((1, dimension)).astype('float32')
distances, indices = index.search(query, k=5)

# IVF index (approximate, faster for large datasets)
nlist = 100  # Number of clusters
quantizer = faiss.IndexFlatL2(dimension)
index_ivf = faiss.IndexIVFFlat(quantizer, dimension, nlist)
index_ivf.train(embeddings)
index_ivf.add(embeddings)
index_ivf.nprobe = 10  # Search 10 nearest clusters
distances, indices = index_ivf.search(query, k=5)

# HNSW index (graph-based, good balance)
index_hnsw = faiss.IndexHNSWFlat(dimension, 32)
index_hnsw.add(embeddings)
```

---

## 49. What are LoRA and QLoRA for fine-tuning LLMs?

**Answer:**

```python
# LoRA: Low-Rank Adaptation
# Instead of fine-tuning all weights W, learn W + BA
# where B (d×r) and A (r×d), r << d

from peft import LoraConfig, get_peft_model
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-7b-hf")

lora_config = LoraConfig(
    r=16,                    # Rank of decomposition
    lora_alpha=32,           # Scaling factor
    target_modules=["q_proj", "v_proj"],  # Which layers
    lora_dropout=0.1,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)
model.print_trainable_parameters()
# trainable params: 4,194,304 || all params: 6,742,609,920 || trainable%: 0.06%

# QLoRA: Quantized LoRA (4-bit quantization + LoRA)
from transformers import BitsAndBytesConfig

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.float16,
    bnb_4bit_use_double_quant=True
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    quantization_config=bnb_config,
    device_map="auto"
)
model = get_peft_model(model, lora_config)
```

---

## 50. How do you build an ML experiment tracking system?

**Answer:**

```python
# Using MLflow
import mlflow
import mlflow.sklearn

mlflow.set_experiment("my_experiment")

with mlflow.start_run(run_name="random_forest_v1"):
    # Log parameters
    mlflow.log_param("n_estimators", 100)
    mlflow.log_param("max_depth", 10)
    
    # Train model
    model = RandomForestClassifier(n_estimators=100, max_depth=10)
    model.fit(X_train, y_train)
    
    # Log metrics
    train_acc = model.score(X_train, y_train)
    test_acc = model.score(X_test, y_test)
    mlflow.log_metric("train_accuracy", train_acc)
    mlflow.log_metric("test_accuracy", test_acc)
    
    # Log model
    mlflow.sklearn.log_model(model, "model")
    
    # Log artifacts (plots, data files)
    mlflow.log_artifact("confusion_matrix.png")

# Using Weights & Biases
import wandb

wandb.init(project="my_project", config={
    "n_estimators": 100,
    "max_depth": 10
})

for epoch in range(epochs):
    train_loss, val_loss = train_epoch()
    wandb.log({
        "train_loss": train_loss,
        "val_loss": val_loss,
        "epoch": epoch
    })

wandb.finish()
```
