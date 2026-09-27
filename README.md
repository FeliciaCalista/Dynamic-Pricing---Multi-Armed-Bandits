# Dynamic Pricing with Multi-Armed Bandits

This project simulates **dynamic pricing** for a single product using several **Multi-Armed Bandit (MAB)** algorithms implemented from scratch.

The main focus of the project is to understand **how agents learn**, **how environmental assumptions affect performance**, and **why certain algorithms perform better under specific conditions**.

---

## Environment Setup

The simulated market consists of:

* A single product
* Multiple pricing options (arms)
* Customers with probabilistic purchasing behavior

### Stored Data

* List of available prices that can be selected by the agent
* Customer purchase probabilities associated with each price

### `pull` Mechanism

Each time the agent selects a price:

* The customer either makes a purchase
* Or does not make a purchase

The outcome is stochastic (random).

---

## Types of Environments

### 1. Simple Dynamic Pricing Environment

**Characteristics:**

* Each arm has a **constant purchase probability**
* The environment is **stationary**
* No price sensitivity
* Reward = price
* No market noise

This environment is suitable for understanding the basic concept of the **exploration-exploitation trade-off**.

---

### 2. Realistic Dynamic Pricing Environment

**Characteristics:**

* Purchase probabilities are **dynamic**
* Determined by the difference between price and **willingness to pay**
* Uses a sigmoid function
* The environment is **non-stationary** due to seasonal effects and trends
* Includes price sensitivity
* Reward = profit (price − cost)
* Includes market noise

This environment more closely represents **real-world market conditions** and requires the agent to continuously adapt.

---

## Agent Framework

All agents inherit the same interface:

* `select_arm()` → selects a price
* `update(arm, reward)` → learns from the outcome

This design ensures that each agent:

* Faces the same environment
* Is evaluated under the same conditions
* Differs only in its learning strategy

---

## Agents

### Greedy Agent

* Does not perform exploration
* Always selects the arm with the highest estimated reward
* Maintains:

  * The number of times each arm has been selected
  * The estimated average reward

Performs well in stationary environments but poorly in dynamic environments.

---

### ε-Greedy Agent

* Explores with probability ε
* Exploits with probability 1 − ε
* More robust than the Greedy approach
* However, its exploration is random and not always efficient

---

### UCB (Upper Confidence Bound)

* Based on the principle of **Optimism Under Uncertainty**
* Arms that have been selected less frequently are considered potentially promising
* Adds an exploration bonus:

  * Rarely selected arm → larger bonus
  * Frequently selected arm → smaller bonus
* Ensures that every arm is explored at least once

UCB does not necessarily select the arm that is currently estimated to be the best, but rather the arm that **could potentially be the best**.

---

### Gradient Bandit (Policy-Based)

* Learns a **policy** rather than directly estimating action values
* Evaluates whether the current reward is:

  * Better than the average reward
  * Or worse than the average reward
* Updates the preference for each arm based on this comparison
* Uses **softmax** to convert preferences into selection probabilities

The agent learns to directly shape its decision-making policy.

---

## Simulation

At each simulation step:

1. The agent selects an arm (`select_arm`)
2. The environment provides a reward (`pull`)
3. The agent updates its strategy (`update`)
4. The resulting data is recorded

### Recorded Data

* `rewards` → reward obtained at each step
* `actions` → arm selected at each step
* `cumulative_rewards` → cumulative reward over time

Cumulative reward is used as the primary metric for evaluating agent performance.

---

## Experimental Results

### Dynamic Pricing Environment (Stationary)

**Performance ranking:**

Greedy ≈ UCB > Gradient Bandit > ε-Greedy

**Discussion:**

* The Greedy agent happens to identify the best arm early and continuously exploits it.
* UCB eventually converges toward the best arm as uncertainty decreases.
* Gradient Bandit remains relatively exploratory, resulting in lower performance during the early stages.
* ε-Greedy intentionally continues exploring even after identifying the best arm.

---

### Realistic Dynamic Pricing Environment (Non-Stationary)

**Performance ranking:**

Gradient Bandit > ε-Greedy > UCB > Greedy

**Discussion:**

* Gradient Bandit can adapt its price-selection distribution as market conditions change.
* ε-Greedy remains adaptive, although its exploration is random.
* UCB adapts more slowly because it assumes a stationary environment.
* Greedy performs poorly because it does not explore alternative prices.

---

## Key Insights

> **The effectiveness of an algorithm strongly depends on the characteristics of the environment.**

* Stationary environment → aggressive exploitation tends to perform well
* Dynamic environment → adaptive exploration becomes more important
* Policy-based methods can perform well in realistic dynamic pricing environments

---

## Future Development

* Contextual Bandits (time, customer segments)
* Regret analysis
* Sliding-Window UCB
* Thompson Sampling
* Full Reinforcement Learning (state-based pricing)
