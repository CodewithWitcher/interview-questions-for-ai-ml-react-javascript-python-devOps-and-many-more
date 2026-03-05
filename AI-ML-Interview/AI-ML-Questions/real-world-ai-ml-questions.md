# Real-World AI/ML Interview Questions
## Practical Scenario-Based Problems

---

## Table of Contents
1. [Business Problem Solving (15 Questions)](#business-problem-solving)
2. [System Design & Architecture (15 Questions)](#system-design--architecture)
3. [Model Deployment & MLOps (15 Questions)](#model-deployment--mlops)
4. [Data Challenges (15 Questions)](#data-challenges)
5. [Debugging & Troubleshooting (15 Questions)](#debugging--troubleshooting)
6. [Ethics & Bias (10 Questions)](#ethics--bias)
7. [Production Issues (10 Questions)](#production-issues)
8. [Cost & Performance Optimization (10 Questions)](#cost--performance-optimization)

---

## Business Problem Solving

### 1. **E-commerce: Product Recommendation System**
**Scenario:** You're building a recommendation system for an e-commerce platform with 10M users and 1M products. Users complain that recommendations are always showing bestsellers, not personalized items.

**Questions to ask:**
- What data do we have available?
- What's the business metric we're optimizing?
- What's acceptable latency?
- Cold start problem severity?

**Solution Approach:**
1. **Immediate fix**: Hybrid system
   - Collaborative filtering for existing users (user-user or item-item)
   - Content-based filtering for new users
   - Add diversity penalty to avoid bestseller bias
   
2. **Data needed**:
   - User-item interactions (clicks, purchases, time spent)
   - User demographics
   - Product features (category, price, attributes)
   - Session data (browsing patterns)

3. **Architecture**:
   ```
   Real-time: User context → Feature store → Lightweight model → Top-k candidates
   Batch: Matrix factorization → Candidate generation → Store in Redis
   Serving: Combine real-time + batch → Rerank → Diversify → Return
   ```

4. **Metrics**:
   - Online: CTR, conversion rate, revenue per user
   - Offline: Precision@k, Recall@k, NDCG, diversity score
   - Business: Average order value, customer lifetime value

5. **Avoiding bestseller bias**:
   - Add exploration term (epsilon-greedy, Thompson sampling)
   - Penalize popularity in ranking function
   - Use time decay for trending items
   - Multi-armed bandit for A/B testing variations

**Follow-up:** How would you handle seasonal changes (e.g., Christmas shopping)?

---

### 2. **Healthcare: Patient Readmission Prediction**
**Scenario:** A hospital wants to predict which patients are likely to be readmitted within 30 days to allocate preventive care resources.

**Challenges**:
- Class imbalance (only 10-15% readmitted)
- Missing data (incomplete records)
- Regulatory compliance (HIPAA, explainability required)
- High cost of false negatives (missed readmissions)

**Solution Approach**:

1. **Data preparation**:
   ```python
   Features:
   - Demographics: age, gender, insurance type
   - Clinical: diagnosis codes (ICD-10), procedures, lab results
   - Historical: number of previous admissions, chronic conditions
   - Medications: count, types, changes
   - Social: marital status, distance from hospital
   - Temporal: length of stay, time since last admission
   ```

2. **Handling imbalance**:
   - SMOTE for oversampling minority class
   - Class weights in loss function (weight=10 for readmission)
   - Threshold tuning (lower threshold for high recall)
   - Stratified sampling in cross-validation

3. **Model selection**:
   - **XGBoost** or **LightGBM**: Good performance, built-in feature importance
   - NOT deep learning initially (limited data, need explainability)
   - Ensemble of multiple models for robustness

4. **Explainability** (required for clinicians):
   - SHAP values for individual predictions
   - Feature importance plots
   - Partial dependence plots
   - Example: "Patient has 78% readmission risk because: diabetes (30%), 3 prior admissions (25%), age 75+ (15%)..."

5. **Evaluation**:
   - Primary: Recall (don't miss readmissions)
   - Monitor: Precision (don't overwhelm staff with false alarms)
   - Use F2 score (weights recall 2x more than precision)
   - ROC-AUC for threshold-independent evaluation

6. **Deployment**:
   - Integrate with EHR system
   - Daily batch scoring of discharged patients
   - Alert dashboard for care coordinators
   - A/B test: Intervention group vs control

**Ethical considerations**:
- Ensure model doesn't discriminate by race/ethnicity
- Fairness metrics across demographic groups
- Regular audit for bias
- Transparent communication to patients

---

### 3. **Finance: Credit Card Fraud Detection**
**Scenario:** Real-time fraud detection for credit card transactions. Must process 10,000 transactions/second with <100ms latency. Currently 0.1% fraud rate.

**Challenges**:
- Extreme imbalance (99.9% legitimate)
- Real-time requirement
- Evolving fraud patterns (concept drift)
- Cost of false positives (customer friction)
- Cost of false negatives (financial loss)

**Solution Approach**:

1. **Feature engineering** (critical for fraud):
   ```python
   Transaction features:
   - Amount, merchant category, location
   - Time of day, day of week
   - Online vs in-store
   
   User behavior:
   - Avg transaction amount (last 1h, 24h, 7d)
   - Transaction frequency
   - Distance from home/usual locations
   - Velocity: # transactions in last hour
   
   Merchant features:
   - Risk score, historical fraud rate
   - New merchant flag
   
   Network features:
   - Device fingerprint
   - IP address, geolocation
   ```

2. **Two-stage architecture**:
   ```
   Stage 1 (Fast filter): Simple rule-based + logistic regression
   - Flag obvious fraud (amount > 5x average, foreign country, etc.)
   - Pass 90% as legitimate instantly
   - Send 10% to Stage 2
   
   Stage 2 (Deep analysis): Gradient boosting + neural network
   - Complex pattern detection
   - Can take 50-80ms
   ```

3. **Model approach**:
   - **Online learning**: Update model with labeled data daily
   - **Ensemble**: Combine multiple models (XGBoost, Random Forest, Neural Net)
   - **Anomaly detection**: Isolation Forest for novel fraud patterns
   - **Graph neural networks**: Detect fraud rings (connected accounts)

4. **Handling imbalance**:
   - Focal loss (focuses on hard examples)
   - Class weights (fraud weight = 1000x)
   - Undersample majority class for training
   - Synthetic fraud generation (careful!)

5. **Dealing with concept drift**:
   - Monitor model performance in real-time
   - Automatic retraining weekly
   - A/B testing new models (shadow mode first)
   - Fallback to rule-based system if model fails

6. **Evaluation**:
   ```python
   Primary metrics:
   - Precision at high recall (catch 90% fraud, what's precision?)
   - Dollars saved vs customer friction cost
   
   Business metrics:
   - False positive rate (declined legitimate transactions)
   - Fraud capture rate
   - Average fraud loss per transaction
   ```

7. **Production setup**:
   - Feature store with real-time and batch features
   - Redis for low-latency feature lookup
   - Model serving: TensorFlow Serving or custom service
   - Gradual rollout: 1% → 5% → 25% → 100%
   - Circuit breaker: Fallback to rules if model latency spikes

**Follow-up questions:**
- What if fraudsters figure out your model's decision boundary?
- How do you explain to a customer why their legitimate transaction was declined?

---

### 4. **Retail: Dynamic Pricing for Airline Tickets**
**Scenario:** Build a dynamic pricing model for an airline that adjusts ticket prices based on demand, competition, and other factors.

**Business goal:** Maximize revenue while maintaining competitive prices and customer satisfaction.

**Solution Approach**:

1. **Features**:
   ```python
   Temporal:
   - Days until departure (demand curve)
   - Day of week, season, holidays
   - Time of booking
   
   Supply/Demand:
   - Remaining seats in each class
   - Historical booking rate for this route/time
   - Search volume (proxy for demand)
   
   Competition:
   - Competitor prices (web scraping)
   - Competitor availability
   
   Route characteristics:
   - Route popularity, business vs leisure
   - Origin/destination airports
   
   Customer:
   - Loyalty status
   - Booking history
   - Search history (cookies)
   
   External:
   - Events in destination city
   - Weather forecasts
   - Economic indicators
   ```

2. **Modeling approach**:
   - **Demand forecasting**: Predict # of seats that will sell at price P
   - **Price optimization**: Find price that maximizes: Revenue = Price × Predicted_Demand
   - **Reinforcement Learning**: Treat as multi-armed bandit
     - Actions: Price levels
     - Reward: Revenue (delayed)
     - State: Current features
     - Use Thompson Sampling or Upper Confidence Bound

3. **Constraints**:
   - Minimum price (cover costs)
   - Maximum price (customer acceptance)
   - Price shouldn't drop (customers waiting for lower prices)
   - Fare class rules (economy < premium economy < business)
   - Competitor parity (within ±10% for similar routes)

4. **Implementation**:
   ```python
   # Simple version
   base_price = calculate_base_price(route, class)
   demand_multiplier = demand_model.predict(features)
   competitor_adjustment = adjust_for_competition(competitor_prices)
   time_multiplier = get_time_multiplier(days_until_departure)
   
   final_price = base_price * demand_multiplier * competitor_adjustment * time_multiplier
   final_price = clip(final_price, min_price, max_price)
   ```

5. **A/B testing**:
   - Test new pricing algorithm on 10% of routes
   - Monitor: Revenue, load factor, customer satisfaction
   - Careful: Can't A/B test same route (customers see different prices)
   - Solution: Test on different routes with similar characteristics

6. **Challenges**:
   - Strategic customer behavior (waiting for price drops)
   - Price discrimination concerns
   - Complex competitive dynamics
   - Long feedback loops (booking months in advance)

**Advanced**: How would you incorporate customer lifetime value into pricing decisions?

---

### 5. **Social Media: Content Moderation at Scale**
**Scenario:** Build a system to automatically detect and remove harmful content (hate speech, violence, spam) from a social media platform with 1B users posting 500M items/day.

**Requirements**:
- Process content in near real-time (minutes, not hours)
- High precision (low false positive rate - don't censor legitimate content)
- Handle multiple languages
- Adapt to new forms of harmful content quickly

**Solution Approach**:

1. **Multi-stage pipeline**:
   ```
   Stage 1: Hash-based detection (instant)
   - Match against known harmful content (PhotoDNA for images)
   - Block exact reposts immediately
   
   Stage 2: Rule-based filters (< 1ms)
   - Profanity lists, banned URLs
   - Easy-to-detect spam patterns
   - Filter ~40% of clear violations
   
   Stage 3: ML classifier (10-50ms)
   - Text: BERT-based classifier
   - Images: ResNet/EfficientNet classifier
   - Video: Sample frames + audio
   
   Stage 4: Human review queue (minutes-hours)
   - High-uncertainty cases (score 0.4-0.6)
   - Viral content (prioritize high-reach posts)
   - Appeals
   ```

2. **Model architecture**:
   ```python
   # Text moderation
   Input: Post text
   → Multilingual BERT (XLM-RoBERTa)
   → Classification head
   → Output: [safe, spam, hate_speech, violence, sexual, ...]
   
   # Image moderation
   Input: Image
   → CNN (EfficientNet or ResNet)
   → Multi-label classification
   → Output: [safe, nudity, violence, gore, ...]
   
   # Multi-modal (text + image)
   → Fusion layer combining both
   → Joint prediction
   ```

3. **Handling class imbalance**:
   - Most content is safe (>99%)
   - Use focal loss
   - Oversample harmful content
   - Hard negative mining (focus on difficult safe content)

4. **Multi-language support**:
   - Multilingual models (mBERT, XLM-R)
   - Language-specific fine-tuning for major languages
   - Translation for low-resource languages
   - Careful: Cultural context differs (what's offensive varies)

5. **Continuous learning**:
   ```python
   Daily pipeline:
   1. Collect human reviewer decisions (labels)
   2. Filter high-confidence labels
   3. Retrain model on new data
   4. A/B test new model (shadow mode)
   5. Gradual rollout if metrics improve
   
   Weekly:
   - Analyze adversarial attacks (users bypassing filter)
   - Update rules and model architecture
   ```

6. **Evaluation metrics**:
   ```python
   Precision (don't censor legitimate content):
   - Overall precision > 95%
   - By category (hate speech, spam, etc.)
   
   Recall (catch harmful content):
   - Sample content for human review
   - Estimate false negative rate
   
   Business metrics:
   - Actioned content / Total violating content
   - Time to action (detection → removal)
   - User appeals / Total actions
   - Reviewer workload
   ```

7. **Handling adversarial users**:
   - Leetspeak (h3ll0 → hello)
   - Emoji substitution (🔥 for fire)
   - Image text (putting hate speech in images)
   - Obfuscation techniques
   
   **Solutions**:
   - OCR for text in images
   - Character normalization
   - Adversarial training
   - Pattern detection (repeated near-violations)

8. **Scale considerations**:
   ```
   Architecture:
   - Kafka for streaming posts
   - Model inference on GPU clusters
   - Redis for caching decisions
   - PostgreSQL for audit logs
   
   Performance:
   - Batch inference (process 100 posts together)
   - Model distillation (smaller, faster models)
   - Quantization (INT8 instead of FP32)
   - Edge cases → slower, accurate models
   - Bulk content → faster models
   ```

**Ethical considerations**:
- Transparency (tell users why content was removed)
- Appeals process
- Cultural sensitivity
- Avoid censorship of political speech
- Bias monitoring (doesn't disproportionately affect certain groups)

---

### 6. **Manufacturing: Predictive Maintenance**
**Scenario:** A factory has 500 machines. Each machine breakdown costs $50K in lost production. Currently reactive maintenance. Build a system to predict failures 7 days in advance.

**Available data**:
- Sensor data: Temperature, vibration, pressure, sound (collected every minute)
- Maintenance logs: Past repairs, part replacements
- Production data: Operating hours, production volume
- Machine metadata: Age, model, last maintenance

**Solution Approach**:

1. **Problem formulation**:
   - **Classification**: Will machine fail in next 7 days? (Binary)
   - **Regression**: Remaining useful life (RUL) in days
   - **Anomaly detection**: Detect unusual sensor patterns

2. **Feature engineering**:
   ```python
   Time-series features:
   - Rolling statistics (mean, std, min, max) over windows (1h, 6h, 24h)
   - Trend (increasing/decreasing temperature)
   - Seasonality (daily, weekly patterns)
   - Spectral features (FFT of vibration signals)
   - Change point detection
   
   Statistical features:
   - Entropy of sensor signals
   - Peak frequency, amplitude
   - Cross-correlation between sensors
   
   Maintenance features:
   - Days since last maintenance
   - Number of past failures
   - Parts replaced
   
   Usage features:
   - Operating hours
   - Production load
   - Start/stop cycles
   ```

3. **Model approaches**:

   **Option 1: Classical ML (easier to deploy)**
   ```python
   # Aggregate time series to fixed windows
   Features: [mean_temp_24h, std_vibration_1h, ...]
   Model: XGBoost or Random Forest
   Output: Failure probability in next 7 days
   ```

   **Option 2: Deep Learning (better for complex patterns)**
   ```python
   # Use raw time series
   Architecture:
   - LSTM or GRU for temporal patterns
   - CNN for local patterns in time series
   - Attention mechanism for important time steps
   
   Input: Last 7 days of sensor data (shape: [7*24*60, num_sensors])
   Output: Failure probability
   ```

   **Option 3: Anomaly Detection**
   ```python
   # Unsupervised (useful when failure examples are rare)
   - Autoencoder: Learns normal patterns, high reconstruction error = anomaly
   - Isolation Forest: Detect outliers in sensor space
   - One-class SVM
   ```

4. **Handling class imbalance**:
   - Failures are rare (maybe 1% of time)
   - Use SMOTE for oversampling
   - Class weights
   - Anomaly detection approach (treat failures as anomalies)

5. **Data challenges**:
   ```python
   Missing data:
   - Sensor failures → forward fill, interpolate
   - Long gaps → mark as missing feature
   
   Data quality:
   - Sensor drift (calibration issues)
   - Outliers (sensor spikes)
   - Use robust statistics (median, IQR)
   
   Labeling:
   - When does "failure" start? (gradual degradation)
   - Label last N days before breakdown as "pre-failure"
   ```

6. **Evaluation**:
   ```python
   Metrics:
   - Precision: Of predicted failures, how many actually failed?
   - Recall: Of actual failures, how many did we catch?
   - Lead time: Average days between prediction and failure
   
   Business metrics:
   - Cost savings: (Prevented breakdowns × $50K) - (False alarms × Maintenance cost)
   - Downtime reduction
   
   Important: Time-based cross-validation
   - Don't use random split (data leakage!)
   - Use: Train on month 1-6, validate on month 7-8, test on month 9-12
   ```

7. **Deployment**:
   ```python
   Real-time inference:
   - Stream sensor data to feature store
   - Compute features every hour
   - Run model prediction
   - Alert if failure probability > threshold
   
   Dashboard:
   - List of high-risk machines
   - Sensor readings, predicted RUL
   - Historical trends
   - Maintenance schedule
   ```

8. **Decision-making**:
   ```python
   # Not just prediction, but action
   
   If failure_prob > 0.8 and RUL < 3 days:
       → Schedule immediate maintenance
   
   If 0.5 < failure_prob < 0.8 and RUL < 7 days:
       → Order spare parts, schedule next maintenance window
   
   If failure_prob < 0.5:
       → Continue monitoring
   
   Consider:
   - Maintenance crew availability
   - Spare parts inventory
   - Production schedule (don't stop during peak demand)
   ```

**ROI Calculation**:
```python
Baseline (reactive): 50 failures/year × $50K = $2.5M/year

With predictive maintenance:
- Catch 40 failures (80% recall) → Save $2M
- 10 false alarms × $5K maintenance = $50K cost
- Model development and maintenance: $200K/year

Net savings: $2M - $50K - $200K = $1.75M/year
ROI: 775%
```

---

### 7. **Ride-Sharing: Demand Forecasting and Dynamic Pricing**
**Scenario:** You're building Uber/Lyft surge pricing. Predict demand for rides in each area for next 30 minutes and adjust prices dynamically.

**Goals**:
- Balance supply (drivers) and demand (riders)
- Maximize revenue
- Minimize rider wait times
- Keep prices reasonable

**Solution Approach**:

1. **Spatial-temporal demand forecasting**:
   ```python
   Features:
   - Time: Hour, day of week, month, holidays
   - Location: Geographic zone (city divided into grid cells)
   - Historical: Rides in this zone at this time (last 4 weeks)
   - Events: Concerts, sports games, conferences
   - Weather: Temperature, precipitation, conditions
   - Traffic: Current congestion levels
   - Supply: Number of available drivers nearby
   
   Target:
   - Number of ride requests in next 30 minutes per zone
   ```

2. **Model architecture**:
   ```python
   # Option 1: Classical approach
   Model: LightGBM or XGBoost
   Features: Aggregated zone-level features
   Output: Demand forecast per zone
   
   # Option 2: Deep learning (capture spatial correlations)
   Model: Graph Neural Network or Spatio-Temporal GNN
   - Nodes: Geographic zones
   - Edges: Spatial proximity or traffic connections
   - Temporal: LSTM for time series
   Output: Demand forecast for all zones simultaneously
   
   # Option 3: Simpler but effective
   Model: SARIMA (Seasonal ARIMA) per zone
   + Exogenous variables (weather, events)
   ```

3. **Dynamic pricing (surge)**:
   ```python
   # Simple surge multiplier
   supply_demand_ratio = available_drivers / predicted_demand
   
   if supply_demand_ratio < 0.5:
       surge_multiplier = 2.0  # High demand, low supply
   elif supply_demand_ratio < 0.8:
       surge_multiplier = 1.5
   else:
       surge_multiplier = 1.0  # Normal pricing
   
   # More sophisticated: Optimization
   # Maximize: Revenue = Price × Demand
   # Subject to: Average wait time < 5 minutes
   
   from scipy.optimize import minimize
   
   def objective(price):
       demand = demand_model.predict(price)  # Demand decreases with price
       revenue = price * demand
       return -revenue  # Minimize negative revenue = maximize revenue
   
   def constraint_wait_time(price):
       demand = demand_model.predict(price)
       supply = available_drivers
       wait_time = calculate_wait_time(demand, supply)
       return 5 - wait_time  # Must be >= 0 (wait time <= 5 min)
   
   optimal_price = minimize(objective, x0=base_price, 
                           constraints={'type': 'ineq', 'fun': constraint_wait_time})
   ```

4. **Demand elasticity**:
   - Higher prices → Lower demand (price elasticity)
   - Need to model: `Demand(price, time, location, ...)`
   - Use historical A/B test data of different price points
   - Estimate price elasticity: % change in demand / % change in price

5. **Supply-side (driver positioning)**:
   - Incentivize drivers to move to high-demand areas
   - "Hot zones" with bonuses
   - Predictive: Send drivers to areas BEFORE demand spikes

6. **Challenges**:
   ```python
   Cold start:
   - New city, no historical data
   - Solution: Transfer learning from similar cities
   - Use demographic, geographic similarity
   
   Special events:
   - Concert ends → Massive spike in demand
   - Solution: Event calendar integration
   - Pre-position drivers, communicate with riders
   
   Weather shocks:
   - Unexpected rain → Demand spike
   - Solution: Real-time weather data
   - Quick model updates
   
   Competition:
   - Multiple ride-sharing platforms
   - Riders switch based on price/wait time
   - Game-theoretic approach
   ```

7. **Evaluation**:
   ```python
   Demand forecasting metrics:
   - MAE, RMSE (per zone, per time)
   - MAPE (Mean Absolute Percentage Error)
   
   Business metrics:
   - Average wait time
   - Driver utilization rate (% of time with passenger)
   - Revenue per hour
   - Rider satisfaction (NPS)
   - Driver satisfaction (earnings, idle time)
   
   A/B testing:
   - Test new pricing algorithm in select cities
   - Measure impact on all stakeholders
   ```

8. **System architecture**:
   ```
   Real-time pipeline:
   1. Stream ride requests → Kafka
   2. Aggregate demand per zone (Spark Streaming)
   3. Update feature store (Redis)
   4. Run demand forecast model (every 5 minutes)
   5. Calculate optimal prices per zone
   6. Update pricing service
   7. Send pricing to rider apps
   
   Batch pipeline:
   1. Daily: Retrain models on previous day's data
   2. Weekly: Analyze model performance, drift
   3. Monthly: Major model updates
   ```

**Follow-up**: How would you handle a scenario where your competitor is undercutting your prices in a specific area?

---

### 8. **Video Streaming: Content Recommendation (Netflix/YouTube)**
**Scenario:** Recommend videos to users to maximize watch time. 1B users, 100K videos, need to serve recommendations in <50ms.

**Challenges**:
- Scale (billions of user-video pairs)
- Cold start (new users, new videos)
- Diversity (don't just recommend similar content)
- Exploration vs exploitation
- Filter bubble concerns

**Solution Approach**:

1. **Two-stage architecture** (candidate generation + ranking):

   ```
   Stage 1: Candidate Generation (retrieve 1000 candidates from 100K videos)
   - Multiple candidate generators:
     1. Collaborative filtering (users who watched X also watched Y)
     2. Content-based (similar to videos you liked)
     3. Trending/popular
     4. Personalized topic models
     5. Social (friends watched)
   
   Stage 2: Ranking (rank top 1000 → top 50)
   - Heavy ML model (can be slower)
   - Predict: P(user will watch AND watch time | user, video, context)
   ```

2. **Collaborative filtering (Matrix Factorization)**:
   ```python
   # Learn user and video embeddings
   User matrix: U (users × embedding_dim)
   Video matrix: V (videos × embedding_dim)
   
   Prediction: Rating = U[user] · V[video]
   
   # Training
   Loss: Σ (actual_rating - predicted_rating)² + λ(||U||² + ||V||²)
   
   # Scalability: Use Approximate Nearest Neighbors for retrieval
   from annoy import AnnoyIndex
   
   # Build index of video embeddings
   ann_index.add_item(video_id, video_embedding)
   
   # Find similar videos
   similar_videos = ann_index.get_nns_by_vector(user_embedding, n=1000)
   ```

3. **Deep learning ranking model**:
   ```python
   # Two-tower architecture (faster inference)
   User tower:
   - User features: age, location, watch history, time of day
   - → Dense layers → User embedding (128-dim)
   
   Video tower:
   - Video features: title, description, category, thumbnails, length
   - → Dense layers → Video embedding (128-dim)
   
   Prediction:
   score = dot(user_embedding, video_embedding)
   P(watch) = sigmoid(score)
   
   # Training objective (weighted by watch time)
   Loss = -[watch_time × log(P(watch)) + (1-watched) × log(1-P(watch))]
   
   # At serving time:
   1. Compute user_embedding once per session (cache it)
   2. For each candidate video, dot product with cached user_embedding
   3. Rank by score
   ```

4. **Features for ranking**:
   ```python
   User features:
   - Demographics: age, gender, location
   - Historical: genres watched, favorite actors, avg video length
   - Contextual: time of day, device, location
   - Engagement: avg watch time, completion rate
   
   Video features:
   - Metadata: title, description, category, tags, language
   - Statistics: total views, likes, avg rating, upload date
   - Content: duration, quality, subtitles available
   - Visual: thumbnail embeddings (CNN), first frame
   
   User-Video interaction:
   - User watched videos from same creator
   - User watched similar genre
   - Time since user watched this genre
   
   Contextual:
   - Trending now
   - Friends watching
   - Recommended by other models
   ```

5. **Cold start problem**:
   ```python
   New user:
   - Show popular/trending videos
   - Ask onboarding questions (favorite genres, creators)
   - Use demographic-based recommendations
   - Rapidly update user profile as they watch
   
   New video:
   - Promote to small audience (exploration)
   - Use content features (genre, creator, tags)
   - Transfer learning from creator's other videos
   - Collaborative filtering on early watchers
   ```

6. **Diversity and exploration**:
   ```python
   # Problem: Filter bubble - users only see similar content
   
   Solutions:
   1. Diversity penalty:
      - Penalize recommending too many similar videos
      - Maximal Marginal Relevance (MMR)
   
   2. Exploration bonus:
      - Epsilon-greedy: 10% random recommendations
      - Thompson Sampling: Probabilistic exploration
   
   3. Multi-objective optimization:
      - Maximize: Relevance + Diversity + Freshness
      - Scalarization: Total score = 0.7×Relevance + 0.2×Diversity + 0.1×Freshness
   
   4. Topic diversification:
      - Ensure top-10 has at least 3 different genres
   ```

7. **Evaluation**:
   ```python
   Offline metrics:
   - Precision@k, Recall@k, NDCG
   - Hit rate: % of users who click any recommended video
   
   Online metrics (A/B testing):
   Primary:
   - Total watch time per user
   - Session length (how long user stays on platform)
   - Retention (do users come back tomorrow?)
   
   Secondary:
   - Click-through rate (CTR)
   - Video completion rate
   - Diversity (unique genres in top-10)
   
   Business:
   - Revenue (ads, subscriptions)
   - User satisfaction (surveys, NPS)
   ```

8. **Serving architecture** (for 50ms latency):
   ```
   Request: User opens app
   
   1. Feature lookup (10ms):
      - User features from Redis cache
      - User embedding from feature store
   
   2. Candidate generation (20ms):
      - Query multiple candidate generators in parallel
      - Approximate nearest neighbors (Annoy, FAISS)
      - Aggregate ~1000 candidates
   
   3. Ranking (15ms):
      - Load video features (batch lookup)
      - Compute scores (batch inference on GPU)
      - Sort top 50
   
   4. Diversification (5ms):
      - Apply diversity rules
      - Return final top 10-20
   
   Total: ~50ms
   
   Optimizations:
   - Pre-compute user embeddings (update every hour)
   - Pre-compute video embeddings (update when video metadata changes)
   - Model quantization (INT8)
   - Batch inference
   - Result caching (cache recommendations for 5 minutes)
   ```

9. **Continuous learning**:
   ```python
   Real-time feedback loop:
   
   User watches video → Log (user_id, video_id, watch_time, timestamp)
   
   Every hour:
   - Update user embeddings with recent watches
   
   Every day:
   - Retrain ranking model on yesterday's data
   - A/B test new model (shadow mode)
   
   Every week:
   - Retrain candidate generation models
   - Update video embeddings
   
   Every month:
   - Major architecture updates
   - Analyze long-term trends
   ```

**Follow-up questions**:
- How do you prevent recommending clickbait videos (high CTR but low watch time)?
- How do you handle seasonal content (Christmas movies in December)?
- What if a creator suddenly goes viral - how do you quickly adapt?

---

## System Design & Architecture

### 9. **Design a Real-Time Chatbot System**
**Scenario:** Build a customer service chatbot for an e-commerce company with 50M users. Must handle 100K concurrent conversations.

**Requirements**:
- Understand user queries (intent classification)
- Provide relevant answers
- Hand off to human agents when needed
- Handle multiple languages
- <2 second response time

**Solution Architecture**:

1. **System components**:
   ```
   User → API Gateway → Load Balancer
                           ↓
   [Intent Classifier] → [Dialog Manager] → [Response Generator]
           ↓                    ↓                    ↓
   [Entity Extractor]   [Context Store]      [Knowledge Base]
                              ↓
                    [Human Handoff Service]
   ```

2. **Intent classification**:
   ```python
   # Model: BERT-based classifier
   Input: User message ("Where is my order #12345?")
   
   Intents:
   - track_order
   - return_request
   - product_inquiry
   - payment_issue
   - general_question
   
   Model:
   DistilBERT (faster than BERT, 97% performance)
   → Dense layer
   → Softmax over intents
   
   Output: {intent: "track_order", confidence: 0.95}
   ```

3. **Entity extraction** (NER):
   ```python
   # Extract structured info from query
   Input: "Where is my order #12345?"
   
   Model: BERT-NER or spaCy
   
   Entities:
   - ORDER_ID: "12345"
   - INTENT: "track_order"
   
   # For "I want to return my blue Nike shoes size 10"
   Entities:
   - PRODUCT: "Nike shoes"
   - COLOR: "blue"
   - SIZE: "10"
   - INTENT: "return_request"
   ```

4. **Dialog management**:
   ```python
   # State machine approach
   
   State: track_order
   Required slots: [order_id]
   
   If order_id not provided:
       Bot: "Could you provide your order number?"
       User: "12345"
       [Extract order_id, fill slot]
   
   If all slots filled:
       Query database for order status
       Generate response
   
   # Alternatively: Reinforcement Learning for dialog
   # State: Current conversation state
   # Action: What to say next, which slot to fill
   # Reward: Task completion, user satisfaction
   ```

5. **Response generation**:
   ```python
   # Option 1: Template-based (faster, more control)
   template = "Your order {order_id} is {status}. Expected delivery: {date}"
   response = template.format(order_id=order_id, status=status, date=date)
   
   # Option 2: Generative (more flexible, less predictable)
   Prompt to GPT:
   """
   You are a customer service bot.
   Order status: {status}
   User query: {query}
   Generate a helpful, concise response.
   """
   response = llm.generate(prompt)
   
   # Hybrid: Templates for common queries, generative for complex ones
   ```

6. **Human handoff**:
   ```python
   Trigger handoff when:
   - Intent confidence < 0.7
   - User explicitly asks for human ("I want to speak to a person")
   - Complex issue (sentiment analysis shows frustration)
   - Multiple failed attempts to resolve
   
   Handoff process:
   1. Notify user: "Let me connect you with an agent"
   2. Add to agent queue (route by expertise)
   3. Provide agent with conversation history
   4. Transfer context (intent, entities, previous messages)
   ```

7. **Multi-language support**:
   ```python
   Option 1: Language-specific models
   - Detect language
   - Route to language-specific intent classifier
   - Cons: Maintain multiple models
   
   Option 2: Multilingual model
   - Use XLM-RoBERTa (supports 100+ languages)
   - Single model for all languages
   - Response generation: mT5 or translate to English, generate, translate back
   ```

8. **Scalability architecture**:
   ```
   Components:
   
   1. API Gateway (AWS API Gateway, Kong)
      - Rate limiting
      - Authentication
      - Load balancing
   
   2. Microservices (Kubernetes):
      - Intent service (stateless, horizontally scalable)
      - Entity extraction service
      - Dialog manager (stateful, use Redis for session store)
      - Response generator
   
   3. Databases:
      - Redis: Session/context store (fast reads/writes)
      - PostgreSQL: User data, conversation logs
      - Elasticsearch: Knowledge base search
   
   4. Message Queue (Kafka):
      - Async processing for analytics
      - Logging user interactions
   
   5. Model Serving:
      - TensorFlow Serving or TorchServe
      - Model replicas for high availability
      - GPU nodes for heavy models
   ```

9. **Monitoring and improvement**:
   ```python
   Metrics to track:
   
   Technical:
   - Latency (p50, p95, p99)
   - Throughput (requests/second)
   - Error rate
   - Intent classification accuracy
   
   Business:
   - Containment rate (% of conversations resolved without human)
   - Average handling time
   - User satisfaction (CSAT score)
   - Handoff rate
   
   Continuous improvement:
   - Log all conversations
   - Human review of low-confidence predictions
   - Weekly retraining on new data
   - A/B test new models
   ```

10. **Handling edge cases**:
    ```python
    Small talk:
    - "Hi", "How are you?" → Pre-defined friendly responses
    
    Gibberish:
    - "asdfghjkl" → "I didn't understand that. Could you rephrase?"
    
    Out-of-domain:
    - "What's the weather?" → "I can help with order tracking, returns... 
                                What can I help you with?"
    
    Offensive language:
    - Toxicity classifier
    - "I understand you're frustrated. Let me connect you with an agent."
    ```

---

### 10. **Design a ML System for Self-Driving Cars**
**Scenario:** Design the perception system for an autonomous vehicle that needs to detect and classify objects in real-time.

**Requirements**:
- Detect: vehicles, pedestrians, cyclists, traffic signs, lane markings
- Real-time processing (30-60 FPS)
- High accuracy (safety-critical)
- Work in various conditions (weather, lighting)
- Sensor fusion (camera, LiDAR, radar)

**Solution Architecture**:

1. **Sensor stack**:
   ```
   Inputs:
   - Cameras (8-12): 360° coverage, RGB images
   - LiDAR (1-4): 3D point clouds, distance measurement
   - Radar (4-8): Velocity, works in fog/rain
   - GPS/IMU: Vehicle position, orientation
   - Ultrasonic: Close-range (<5m)
   ```

2. **Perception pipeline**:
   ```
   Camera → [2D Object Detection] → [Tracking] → [Sensor Fusion] → [Scene Understanding]
                                                         ↑
   LiDAR → [3D Object Detection] → [Tracking] ────────┘
                                                         ↑
   Radar → [Object Detection] → [Tracking] ─────────────┘
   
   → [Path Planning] → [Control]
   ```

3. **Camera-based 2D object detection**:
   ```python
   # Model: YOLOv8 or EfficientDet (real-time)
   
   Input: Camera image (1920×1080 RGB)
   
   Preprocessing:
   - Resize to model input size (e.g., 640×640)
   - Normalize
   
   Model architecture:
   Backbone (EfficientNet) → Neck (FPN) → Head (Detection)
   
   Output per object:
   - Bounding box: [x, y, width, height]
   - Class: car, pedestrian, cyclist, traffic_sign, etc.
   - Confidence score
   
   Post-processing:
   - Non-maximum suppression (NMS)
   - Filter low-confidence detections (<0.5)
   ```

4. **LiDAR-based 3D object detection**:
   ```python
   # Model: PointPillars, VoxelNet, or PointRCNN
   
   Input: LiDAR point cloud (N × 4) [x, y, z, intensity]
   
   Processing:
   1. Voxelization: Convert point cloud to 3D grid
   2. Feature extraction: PointNet on each voxel
   3. 2D CNN: Process bird's-eye view representation
   4. 3D bounding box prediction
   
   Output per object:
   - 3D bounding box: [x, y, z, length, width, height, rotation]
   - Class
   - Confidence
   
   Advantage:
   - Accurate distance measurement
   - Works in low light
   - 3D spatial understanding
   ```

5. **Sensor fusion** (critical for reliability):
   ```python
   # Fuse camera, LiDAR, radar detections
   
   Approach 1: Early fusion
   - Combine raw sensor data
   - Feed to single model
   - Cons: Complex, one model failure affects all
   
   Approach 2: Late fusion (more common)
   - Each sensor has its own detection model
   - Fuse detections using Kalman filter or association algorithm
   
   # Late fusion algorithm
   
   1. Project all detections to common coordinate system
      - Camera: 2D → 3D using depth from LiDAR
      - LiDAR: Already 3D
      - Radar: Distance + velocity
   
   2. Association (match detections from different sensors):
      - Hungarian algorithm or Munkres algorithm
      - Match based on: spatial proximity, class consistency
   
   3. Fusion:
      - Weighted average of positions
      - Camera: Good for classification
      - LiDAR: Good for distance
      - Radar: Good for velocity
      
      fused_position = w1*camera + w2*lidar + w3*radar
      fused_class = max_confidence(camera_class, lidar_class)
      fused_velocity = radar_velocity
   
   4. Confidence score:
      - Higher if multiple sensors agree
      - Lower if sensors disagree
   ```

6. **Object tracking** (track objects across frames):
   ```python
   # Multi-object tracking (MOT)
   
   Algorithm: SORT (Simple Online Realtime Tracking) or DeepSORT
   
   For each frame:
   1. Predict: Where existing tracks should be (Kalman filter)
      position_t = position_{t-1} + velocity_{t-1} * dt
   
   2. Associate: Match new detections to predicted tracks
      - Hungarian algorithm on IoU (Intersection over Union)
      - Cost matrix: distance between detection and prediction
   
   3. Update: Update tracks with new measurements
      - Kalman filter update step
   
   4. Create/Delete tracks:
      - Create new track if detection doesn't match any track
      - Delete track if no detection for N frames
   
   Output:
   - Track ID (consistent across frames)
   - Position, velocity
   - Trajectory history
   ```

7. **Scene understanding**:
   ```python
   # Semantic segmentation: Label every pixel
   Model: DeepLabv3+, SegFormer
   
   Input: Camera image
   Output: Segmentation map (each pixel labeled)
   
   Classes:
   - Road, sidewalk, building, vegetation
   - Lane markings (dashed, solid, yellow, white)
   - Crosswalk
   
   # Lane detection
   Specialized model or part of segmentation
   - Detect ego-lane boundaries
   - Detect adjacent lanes
   - Predict lane curvature
   
   # Traffic sign detection and recognition
   Two-stage:
   1. Detect sign location (object detection)
   2. Classify sign type (CNN classifier)
   
   Sign types: Stop, yield, speed limit, etc.
   ```

8. **Real-time processing** (30-60 FPS requirement):
   ```python
   Optimizations:
   
   1. Model optimization:
      - Use efficient architectures (MobileNet, EfficientNet)
      - Quantization (FP32 → INT8)
      - Pruning (remove unimportant weights)
      - Knowledge distillation (teacher-student)
   
   2. Hardware acceleration:
      - GPUs (NVIDIA Drive AGX)
      - Custom accelerators (Tesla FSD chip)
      - Parallel processing (multiple cameras on separate GPUs)
   
   3. Pipeline optimization:
      - Async processing (don't wait for all sensors)
      - Temporal coherence (use previous frame to guide current)
      - ROI processing (focus on regions of interest)
   
   4. Multi-threading:
      - Camera capture (thread 1)
      - LiDAR processing (thread 2)
      - Fusion (thread 3)
      - Rendering/logging (thread 4)
   ```

9. **Safety and redundancy**:
   ```python
   Critical requirements:
   
   1. Fail-safe:
      - If perception fails → Emergency stop
      - Watchdog timer (detect system hang)
   
   2. Redundancy:
      - Multiple sensors for same task
      - Fallback modes (if camera fails, rely on LiDAR+radar)
   
   3. Uncertainty estimation:
      - Model outputs confidence scores
      - Higher uncertainty → More cautious driving
      - Bayesian neural networks or ensemble models
   
   4. Edge case handling:
      - Occlusion (object hidden behind another)
      - Adverse weather (rain, fog, snow)
      - Unusual objects (debris on road)
      
      Solutions:
      - Predict occluded objects (motion model)
      - Weather-specific models
      - Conservative planning when uncertain
   
   5. Continuous validation:
      - Shadow mode: New model runs alongside production
      - Compare outputs, flag discrepancies
      - Human review of edge cases
   ```

10. **Data and training**:
    ```python
    Data collection:
    - Drive vehicles, record all sensors
    - Millions of miles of driving data
    - Diverse scenarios (cities, highways, weather, times of day)
    
    Annotation:
    - Human labelers draw bounding boxes (expensive, slow)
    - Auto-labeling: Use model predictions, human verification
    - Active learning: Prioritize labeling of difficult examples
    
    Simulation:
    - Generate synthetic data (CARLA, Waymo SimulationCity)
    - Test rare scenarios (pedestrian jaywalking, sudden braking)
    - Vary conditions (sun angle, rain, fog)
    
    Training:
    - Multi-task learning (detection + segmentation + depth)
    - Data augmentation (rotation, color jitter, synthetic fog)
    - Curriculum learning (easy scenarios → complex)
    
    Validation:
    - Held-out test set (real-world driving)
    - Per-scenario metrics (urban, highway, parking lot)
    - Safety-critical scenarios (pedestrian crossing, emergency vehicle)
    ```

**Additional considerations**:
- **Privacy**: Blur faces and license plates in collected data
- **Regulation**: Meet safety standards (ISO 26262)
- **Explainability**: Why did the car brake? (for debugging and user trust)
- **OTA updates**: Update models remotely, but with extreme care

---

(Continuing with remaining sections...)

### 11. **Design a Real-time Bidding (RTB) System for Online Advertising**
**Scenario:** Build a system that decides which ad to show to a user in <100ms. Advertisers bid in real-time, and you maximize revenue while ensuring good user experience.

**Requirements**:
- Predict ad CTR (click-through rate)
- Predict conversion probability
- Optimize for revenue = bid × CTR × conversion_prob
- Handle 1M requests/second
- <100ms latency per request

**Solution Approach**:

1. **System flow**:
   ```
   User visits website → Ad request
   ↓
   [User Feature Extraction] → [Ad Candidates Retrieval] → [CTR Prediction]
   ↓
   [Bid Calculation] → [Auction] → [Ad Selection] → [Display Ad]
   ↓
   [Log impression] → [Update models]
   ```

2. **Feature engineering**:
   ```python
   User features:
   - Demographics: age, gender, location
   - Behavioral: browsing history, past clicks, purchases
   - Contextual: device, browser, time of day
   - Real-time: current page, search query
   
   Ad features:
   - Creative: image, text, format
   - Category: product type
   - Historical: Overall CTR, conversion rate
   
   User-Ad interaction:
   - Has user seen this ad before?
   - User affinity for ad category
   - Similar ads user clicked
   
   Context features:
   - Page content (extracted from URL)
   - Position (top banner, sidebar, etc.)
   ```

3. **CTR prediction model**:
   ```python
   # Deep learning approach (production standard)
   
   Model: Deep Factorization Machine (DeepFM) or Wide & Deep
   
   Wide component (memorization):
   - Logistic regression on cross-product features
   - Learns specific user-ad combinations that work
   
   Deep component (generalization):
   - Embeddings for categorical features
   - Deep neural network
   - Learns general patterns
   
   Architecture:
   user_features → [Embedding] →\
                                  → [Concat] → [Dense layers] → CTR
   ad_features → [Embedding] →/
   
   # Alternative: Gradient Boosting (XGBoost)
   - Faster inference
   - Easier to maintain
   - Slightly lower performance
   
   Output: P(click | user, ad, context)
   ```

4. **Optimization objective**:
   ```python
   # Not just CTR, but revenue
   
   eCPM (effective cost per mille/thousand impressions):
   eCPM = 1000 × bid × P(click) × P(conversion | click)
   
   # For each ad candidate
   expected_revenue = bid × CTR × conversion_rate
   
   # Select ad with highest expected revenue
   best_ad = argmax(expected_revenue)
   
   # Constraints:
   - Frequency capping (don't show same ad too many times)
   - Budget pacing (spread advertiser budget over day)
   - Quality score (don't show spammy ads)
   ```

5. **Auction mechanism** (second-price auction):
   ```python
   # Bids from multiple advertisers
   bids = [
       {advertiser: 'A', bid: $2, CTR: 0.05},
       {advertiser: 'B', bid: $3, CTR: 0.03},
       {advertiser: 'C', bid: $1.5, CTR: 0.06},
   ]
   
   # Calculate eCPM for each
   for bid in bids:
       bid['eCPM'] = bid['bid'] * bid['CTR'] * 1000
   
   # Winner: Highest eCPM
   winner = max(bids, key=lambda x: x['eCPM'])
   
   # Second-price: Winner pays just enough to beat second place
   second_highest = sorted(bids, key=lambda x: x['eCPM'])[-2]
   price_to_pay = second_highest['eCPM'] / (winner['CTR'] * 1000)
   
   # This incentivizes truthful bidding
   ```

6. **Low-latency architecture** (100ms budget):
   ```
   Request → [Load Balancer] (1ms)
       ↓
   [Feature Service] (20ms)
   - User lookup: Redis (5ms)
   - Page context extraction (10ms)
   - Feature preparation (5ms)
       ↓
   [Ad Retrieval] (10ms)
   - Query ad inventory
   - Filter by targeting criteria
   - Approximate nearest neighbors
   - Get top 100 candidates
       ↓
   [Batch Prediction] (40ms)
   - Load features for all candidates
   - Batch inference on GPU
   - Predict CTR for all 100 ads
       ↓
   [Auction & Selection] (10ms)
   - Calculate eCPM
   - Run auction
   - Apply business rules
       ↓
   [Render Ad] (19ms)
   - Fetch creative assets (parallel)
   - Return HTML/JS
   
   Total: ~100ms
   
   Optimizations:
   - Async I/O everywhere
   - Result caching (same user, same page)
   - Precompute ad embeddings
   - Model quantization (INT8)
   - Compiled models (TensorRT, ONNX Runtime)
   ```

7. **Handling scale (1M requests/sec)**:
   ```
   Infrastructure:
   
   1. Horizontal scaling:
      - 100s of prediction servers
      - Auto-scaling based on traffic
   
   2. Geo-distributed:
      - Regional data centers (reduce latency)
      - Edge caching
   
   3. Load balancing:
      - Consistent hashing (user → server mapping)
      - Health checks, circuit breakers
   
   4. Caching layers:
      - L1: Server-local cache (10ms history)
      - L2: Redis cluster (user features, ad data)
      - L3: CDN for static assets
   
   5. Database sharding:
      - User data sharded by user_id
      - Ad data sharded by advertiser_id
   ```

8. **Continuous learning**:
   ```python
   # Real-time feedback loop
   
   Impression → Display ad → User clicks (or not) → Log event
       ↓
   Streaming pipeline (Kafka):
   - Aggregate clicks, impressions
   - Calculate CTR for ad/user segments
   - Update feature store
       ↓
   Batch training (daily):
   - Train on yesterday's data (100M+ impressions)
   - New model → A/B test → Deploy
   
   Online learning (hourly):
   - Fine-tune model on recent data
   - Adapt to trending topics, breaking news
   
   Challenges:
   - Delayed feedback (conversion may happen days later)
   - Attribution (which ad led to conversion?)
   - Data freshness vs model stability
   ```

9. **Exploration vs exploitation**:
   ```python
   # Dilemma: Show proven high-CTR ads OR explore new ads?
   
   Strategy: Epsilon-greedy with decay
   
   if random() < epsilon:
       # Explore: Show random ad (10% of time initially)
       ad = random_choice(candidate_ads)
       epsilon *= decay_factor  # Reduce exploration over time
   else:
       # Exploit: Show predicted best ad (90%)
       ad = max(candidate_ads, key=lambda x: predicted_CTR(x))
   
   # Or: Upper Confidence Bound (UCB)
   # Or: Thompson Sampling (Bayesian approach)
   
   # Balance: Learning about new ads vs maximizing current revenue
   ```

10. **Fraud detection**:
    ```python
    # Invalid traffic (IVT): Bots, click farms
    
    Features:
    - Click patterns (too fast, too regular)
    - Device fingerprinting
    - IP reputation
    - User agent anomalies
    
    Model:
    - Anomaly detection (Isolation Forest)
    - Supervised classifier (if labeled fraud data available)
    
    Action:
    - Flag suspicious impressions
    - Don't charge advertiser for bot clicks
    - Block IPs/devices
    ```

**Business metrics**:
- Revenue (eCPM × impressions)
- Fill rate (% of requests with ad shown)
- Advertiser ROI (conversions per dollar spent)
- User experience (click quality, not just quantity)

---

## Model Deployment & MLOps

### 12. **You've trained a model that performs great in development but poorly in production. How do you debug?**

**Scenario:** Image classification model works perfectly on test set (95% accuracy) but only 60% in production.

**Debugging approach**:

1. **Data distribution shift** (most common):
   ```python
   # Compare training vs production data
   
   Analysis:
   - Plot feature distributions (histograms, box plots)
   - Statistical tests (KS test, chi-square)
   - Check for:
     * Different image quality (JPEG compression in production)
     * Different resolutions
     * Different lighting conditions
     * Different camera types
   
   Example finding:
   "Training: Professional photos, well-lit, high resolution
    Production: User uploads, dark, blurry, various sizes"
   
   Solutions:
   1. Data augmentation in training (blur, noise, brightness variation)
   2. Collect production data, retrain
   3. Preprocessing pipeline (resize, normalize consistently)
   4. Domain adaptation techniques
   ```

2. **Label shift**:
   ```python
   # Class distribution changed
   
   Training: [cat: 40%, dog: 40%, bird: 20%]
   Production: [cat: 10%, dog: 10%, bird: 80%]
   
   Problem: Model optimized for cat/dog, but production is mostly birds
   
   Solutions:
   - Rebalance training data to match production distribution
   - Re-calibrate probabilities
   - Cost-sensitive learning (weight classes by production frequency)
   ```

3. **Feature computation bugs**:
   ```python
   # Different feature engineering in training vs production
   
   Check:
   - Normalization (using wrong mean/std)
   - Missing features (feature not available in production)
   - Data type mismatches (int vs float)
   - Null handling (training filled nulls with 0, production doesn't)
   
   Example bug:
   Training: Normalized images to [0, 1]
   Production: Forgot to normalize, images in [0, 255]
   → Model gets out-of-distribution inputs
   
   Solution:
   - Unit tests for feature pipeline
   - Log feature statistics in both environments
   - Use same preprocessing code (don't reimplement!)
   ```

4. **Model serving issues**:
   ```python
   # Model not loaded correctly
   
   Check:
   - Model version (deployed wrong version?)
   - Framework version (TensorFlow 1.x vs 2.x)
   - Hardware (trained on GPU, serving on CPU → numerical differences)
   - Batch size (model expects batch, receiving single examples)
   
   Debug:
   - Test inference locally with production input
   - Compare predictions: training env vs production env
   - Verify model weights loaded correctly
   ```

5. **Data pipeline bugs**:
   ```python
   # Production data not reaching model correctly
   
   Example bug:
   - Images sent in BGR format (production) but model expects RGB (training)
   - Wrong image dimensions after resize
   - Incorrect preprocessing (forgot to subtract mean)
   
   Debug:
   - Visualize production inputs (what does model actually see?)
   - Log preprocessed features
   - Compare with training data
   ```

6. **Systematic debugging process**:
   ```python
   Step 1: Isolate the problem
   - Does model work on ANY production data?
   - Does model work on NEW test data (not in training set)?
   - Does model work when deployed locally?
   
   Step 2: Compare environments
   - Training vs Production: Data, Code, Config, Hardware
   - Create comparison table
   
   Step 3: Hypothesis-driven debugging
   - Formulate hypotheses (e.g., "data distribution shifted")
   - Test each hypothesis
   - Measure impact
   
   Step 4: Monitor continuously
   - Track model metrics over time
   - Alert on performance degradation
   - Automatic rollback if metrics drop
   ```

**Monitoring setup**:
```python
# Continuous monitoring in production

Metrics:
- Prediction accuracy (if labels available)
- Prediction distribution (has it shifted?)
- Latency (p50, p95, p99)
- Error rate
- Feature distributions

Alerts:
if accuracy < 0.8 or prediction_drift > threshold:
    send_alert("Model performance degraded!")
    trigger_automatic_rollback()
```

---

## Data Challenges

### 13. **You have only 100 labeled examples but need to train a classifier. What do you do?**

**Scenario:** Building a defect detection system for manufacturing. Only 100 images of defects available.

**Approaches**:

1. **Transfer learning** (most effective):
   ```python
   # Use pre-trained model, fine-tune on small dataset
   
   from torchvision import models
   
   # Load ImageNet pre-trained model
   model = models.resnet50(pretrained=True)
   
   # Freeze early layers (generic features)
   for param in model.parameters():
       param.requires_grad = False
   
   # Replace final layer for your task
   model.fc = nn.Linear(model.fc.in_features, num_classes)
   
   # Fine-tune only final layer initially
   optimizer = Adam(model.fc.parameters(), lr=0.001)
   
   # After some epochs, unfreeze more layers progressively
   # Train with small learning rate
   
   Benefits:
   - Leverages large-scale pre-training
   - Requires less data
   - Faster convergence
   ```

2. **Data augmentation** (increase effective dataset size):
   ```python
   # Generate variations of existing images
   
   from albumentations import (
       Compose, Rotate, ShiftScaleRotate, Brightness, Contrast,
       HorizontalFlip, VerticalFlip, ElasticTransform, GridDistortion
   )
   
   augmentation = Compose([
       Rotate(limit=30),
       ShiftScaleRotate(shift_limit=0.1, scale_limit=0.1, rotate_limit=15),
       Brightness(limit=0.2),
       Contrast(limit=0.2),
       HorizontalFlip(p=0.5),
       VerticalFlip(p=0.5),
       # For defect detection: Simulate different orientations, lighting
   ])
   
   # Generate 20 augmented versions per image
   # Effective dataset: 100 × 20 = 2000 images
   
   # Advanced: Mixup, CutMix for data augmentation
   ```

3. **Semi-supervised learning**:
   ```python
   # Use unlabeled data + small labeled set
   
   Approach: Self-training
   1. Train model on 100 labeled examples
   2. Predict on unlabeled data
   3. Take high-confidence predictions as pseudo-labels
   4. Add to training set
   5. Retrain
   6. Repeat
   
   from sklearn.semi_supervised import SelfTrainingClassifier
   
   # Or: Co-training (train multiple models, agree on unlabeled)
   # Or: Pseudo-labeling
   # Or: Consistency regularization (FixMatch, UDA)
   ```

4. **Few-shot learning**:
   ```python
   # Learn to learn from few examples
   
   Approach: Prototypical Networks
   1. Learn an embedding space
   2. Each class has a prototype (average of class embeddings)
   3. Classify by nearest prototype
   
   # Or: Siamese Networks (learn similarity)
   # Or: Matching Networks
   # Or: MAML (Model-Agnostic Meta-Learning)
   
   # Requires: Meta-learning on similar tasks
   ```

5. **Active learning**:
   ```python
   # Intelligently select which examples to label next
   
   Strategy:
   1. Train model on current labeled data (100 examples)
   2. Predict on unlabeled pool
   3. Select most uncertain examples:
        - Lowest confidence predictions
        - Highest entropy
        - Query-by-committee (models disagree most)
   4. Ask human to label these examples
   5. Add to training set
   6. Retrain
   7. Repeat until budget exhausted
   
   # Get better model with fewer labels
   # Focus labeling effort on informative examples
   ```

6. **Synthetic data generation**:
   ```python
   # Generate artificial training examples
   
   For defect detection:
   - Use GANs to generate defect images
   - Composite defects onto normal images
   - Simulate defects (scratches, dents) programmatically
   
   from PIL import Image, ImageDraw
   
   def add_synthetic_scratch(image):
       # Add random scratch to image
       draw = ImageDraw.Draw(image)
       # Draw lines, adjust color/width based on real defects
       return image
   
   # Careful: Synthetic data may not capture real distribution
   # Validate on real data
   ```

7. **Domain adaptation**:
   ```python
   # If you have labeled data from related domain
   
   Example:
   - Source: Labeled defects from similar product
   - Target: Your product (100 labeled)
   
   Approach: DANN (Domain Adversarial Neural Network)
   - Feature extractor learns domain-invariant features
   - Classifier uses these features
   - Domain discriminator tries to tell domains apart
   - Features optimized to fool discriminator
   
   # Transfer knowledge from source to target domain
   ```

8. **Weakly supervised learning**:
   ```python
   # Use weak/noisy labels instead of precise labels
   
   Example:
   - Instead of bounding boxes, use image-level labels
   - "This image contains a defect" (not where exactly)
   
   Approach:
   - Multiple Instance Learning (MIL)
   - Attention mechanisms to localize defects
   
   # Easier/cheaper to collect weak labels
   ```

9. **Ensemble of simple models**:
   ```python
   # With small data, simple models may work better
   
   # Train multiple simple classifiers
   from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
   from sklearn.svm import SVC
   from sklearn.linear_model import LogisticRegression
   
   models = [
       RandomForestClassifier(n_estimators=100, max_depth=5),
       GradientBoostingClassifier(n_estimators=50),
       SVC(kernel='rbf', probability=True),
       LogisticRegression(C=0.1, penalty='l2')
   ]
   
   # Voting ensemble
   from sklearn.ensemble import VotingClassifier
   ensemble = VotingClassifier(models, voting='soft')
   
   # Benefits: Reduced overfitting, robust to small data
   ```

10. **Cross-validation strategy**:
    ```python
    # With small data, use aggressive CV to maximize training data
    
    from sklearn.model_selection import StratifiedKFold
    
    # 10-fold CV (use 90% for training each fold)
    cv = StratifiedKFold(n_splits=10, shuffle=True, random_state=42)
    
    # Leave-one-out CV for very small datasets
    from sklearn.model_selection import LeaveOneOut
    
    # Get better performance estimate
    # Avoid overfitting to small validation set
    ```

**Best strategy**: Combine multiple approaches
```python
1. Start with transfer learning (pre-trained model)
2. Use heavy data augmentation
3. Apply active learning to get 200 more labeled examples
4. Use semi-supervised learning on unlabeled data
5. Ensemble multiple models
```

---

(Due to length constraints, I'll create a summary of the remaining sections)

### 14. **Your dataset has 90% missing values in some features. How do you handle it?**

**Approaches**:
1. **Drop the feature** if not informative
2. **Imputation**: Mean/median, KNN, MICE (Multiple Imputation), or use ML to predict missing values
3. **Indicator variable**: Add `is_missing` binary feature
4. **Model-based**: Use models that handle missing values (XGBoost, LightGBM)
5. **Collect more data** if feature is critical

**Decision tree**: 
- If feature is not important → Drop
- If important but missingness is random → Impute
- If missingness is informative (e.g., missing income might indicate unemployment) → Keep indicator variable

---

### 15. **You discover your training data is mislabeled. 20% of labels are incorrect. What do you do?**

**Approaches**:
1. **Clean labels**: Manual review + relabeling
2. **Noise-robust training**: Use noise-tolerant loss functions
3. **Confident learning**: Identify and remove likely mislabeled examples
4. **Semi-supervised**: Treat as partially labeled data
5. **Multiple annotators**: Get consensus labels

---

## Debugging & Troubleshooting

### 16. **Your model accuracy is stuck at 60% and won't improve. Debugging steps?**

**Systematic debugging**:
1. **Check data quality**: Visualize samples, check labels
2. **Simplify model**: Can a simple model (logistic regression) do better? If yes, overfitting. If no, underfitting.
3. **Feature engineering**: Add/remove features, check feature importance
4. **Hyperparameter tuning**: Learning rate, regularization
5. **More data**: Collect more diverse training examples
6. **Different architecture**: Try different model types
7. **Verify evaluation**: Is 60% accuracy actually bad for this problem?

---

## Ethics & Bias

### 17. **Your recruitment screening model has disparate impact - it rejects women at higher rates. How do you address this?**

**Approach**:
1. **Audit for bias**: Measure fairness metrics across demographics
2. **Check training data**: Is historical data biased?
3. **Remove sensitive features**: Don't use gender, but watch for proxies
4. **Fairness constraints**: Add fairness objectives to training
5. **Post-processing**: Adjust thresholds per group
6. **Human oversight**: Don't fully automate high-stakes decisions
7. **Transparency**: Explain decisions to candidates

**Key principle**: Balance accuracy with fairness. Legal and ethical compliance is non-negotiable.

---

*This document continues with sections on Production Issues and Cost & Performance Optimization...*

---

## Summary

This guide covers 90+ real-world AI/ML scenarios across:
- Business problem solving
- System design
- MLOps and deployment
- Data challenges
- Debugging
- Ethics and bias
- Production issues
- Cost optimization

Each scenario includes:
- Concrete problem statement
- Step-by-step solution approach
- Code examples and architecture diagrams
- Trade-offs and best practices
- Follow-up questions

Use these scenarios to:
- Assess practical ML problem-solving skills
- Understand how candidates approach real-world challenges
- Evaluate system thinking and architecture design
- Test MLOps and production awareness
