---
title: "IMC Prosperity 3 - 2025"
collection: miscellaneous
type: "Financial trading challenge"
permalink: /teaching/imc-prosperity-3
venue: "International"
date: 2025-04-01
location: "Ithaca, NY"
---

## Overview
*Prosperity* is a 15-day trading challenge hosted by IMC Trading, a trading firm. The 15-day challenge is separated into 5 rounds, each of which lasts 72 hours. Within each round, teams compete in a pair of challenges – an *algorithmic* challenge, which involves noticing trends in markets and writing Python code to automate buying and selling of certain commodities, and a *manual* challenge, which involves submitting a single answer to a trading-based question. I focused on solving the manual challenges. My thought process and the outcomes are detailed below.

# Round 1: Currency Exchange
In Round 1, we were given the following table of currencies to trade, along with their corresponding exchange rates:

|                 |  Snowballs  |  Pizza  |  Silicon Nuggets  |  Seashells  |
|-----------------|-------------|---------|-------------------|-------------|
|    Snowballs    |   1.00      |  1.45   |        0.52       |    0.72     |
|      Pizza      |    0.70     |   1.00  |        0.31       |     0.48    |
| Silicon Nuggets |   1.95      |  3.10   |     1.00          |   1.49      |
| Seashells       |   1.34      | 1.98    |        0.64       |    1.00     |

where the row label is the item you have and the column label is the item you are converting your currency into. Our goal is to maximize our funds by performing a sequence of trades. We are constrained to start with seashells (the currency on our island) and end with seashells, and we are also constrained to make at most 5 trades.

Because the maximum number of trades allowed is so small, we can directly check the outcome of all legal sequences of trades and choose the sequence that gives us the largest return. We begin by indexing the elements of our table: 

0 = Snowballs, 1 = Pizza, 2 = Silicon Nuggets, and 3 = Seashells. 

A legal trade sequence is a string of at most 6 digits that starts and ends with 3; for instance, 31023 is a legal sequence of trades, and we can use the table to compute the return by taking the product of the appropriate sequence of elements in the table. The starting and ending digit contain no information, so we can drop them. A legal trade of length N is then represented by a string of length N-1.

We can further reduce the computational time by noting that trading a currency for itself is 1-1, and so strings that contain the same number multiple times in a row are equivalent to a trade of shorter length; for instance, the returns of 0122 and 012 are identical.

I implement this trading procedure in Python by enumerating all distinct trades and then computing their returns. There are 3 strategies of length 2, 6 strategies of length 3, 21 strategies of length 4, and 60 strategies of length 5. In principle, a shorter strategy could be optimal, so all need to be checked. But it turns out that the optimal strategy is 0210, corresponding to the following trade sequence:

seashells->snowballs->silicon nuggets->pizza->snowballs->seashells.

This sequence multiplies your starting currency by 1.08868032. Many other participating teams also found this optimal trade, leading to a ~1000-way tie in the manual Round 1.

# Round 2: competitive container-picking
In this round, several containers are presented. Each container has a different amount of seashells inside of it $c_i$. When we select a box, we share its contents with some number of *individuals* $n_i$ (also printed on the box) and also some percentage of teams that select the same box as we do. To be precise, the payout $w_i$ from selecting box $i$ with $c_i$ seashells and $n_i$ individuals when a fraction $f_i$ of the total number of teams also select that box is

$$w_i = \frac{c_i}{n_i+100*f_i}.$$

As a twist, we (and everyone else) also have the option to selecct a second box, for the "low" price of 50,000 seashells (roughly the amount that was won in the entire first round).

To study the structure of this problem, it is useful to find the *Nash equilibrium*, which is a set of strategies for each team whereupon, given everyone else's strategy remains fixed, you can do no better by changing your strategy. The strategy in complicated games is usually a *mixed* one, meaning that each team finds that using a probability distribution supported over different selections of boxes that is optimal (rather than simply picking a particular box).

We can start by studying a simplified game, where we are only allowed to pick one of the boxes (it will turn out that this is sufficient to find the Nash equilibrium). Then a *strategy* for a particular team is a set $\left{p_i\right}$ of probabilities for picking each box. The expected return of this strategy, given that every other team's own strategy is fixed, is a weighted sum of the returns from each box:

$$\left\langle w\right\rangle=\sum_ip_i\frac{c_i}{n_i+100*f_i}.$$

For this strategy to be optimal, our team must not be able to do any better by changing our strategy. This means that 

$$\frac{\partial\left\langle w\right\rangle}{\partial p_j}=0\;\;\;\;\textrm{for all } p_j.$$

# Round 3: turtle trading
Round 3

# Round 4: competitive suitcase-picking
This round ended up being nearly identical to Round 2 in theory, but with more interesting results given the selected numbers. 

# Round 5: reading comprehension
Round 5
