---
title: "IMC Prosperity 3 (2025)"
collection: miscellaneous
type: "Financial trading challenge"
permalink: /miscellaneous/imc-prosperity-3
---

## Overview
*Prosperity* is a \\( 15 \\)-day trading challenge hosted by IMC Trading, a global trading firm and market maker. The \\( 15 \\)-day challenge is separated into \\( 5 \\) rounds, each of which lasts \\( 72 \\) hours. Within each round, teams compete in a pair of challenges – an *algorithmic* challenge, which involves noticing trends in markets and writing Python code to automate buying and selling of certain commodities, and a *manual* challenge, which involves submitting a single answer to a trading-based question. I focused on solving the manual challenges. My thought process and the outcomes are detailed throughout this page.

# Round 1: Currency exchange
In round 1, we were given the following table of currencies to trade, along with their corresponding exchange rates:

  
|                 |  Snowballs  |  Pizza  |  Silicon Nuggets  |  Seashells  |
|-----------------|-------------|---------|-------------------|-------------|
| Snowballs       |   1.00      |  1.45   |        0.52       |    0.72     |
| Pizza           |   0.70      |  1.00   |        0.31       |    0.48     |
| Silicon Nuggets |   1.95      |  3.10   |        1.00       |    1.49     |
| Seashells       |   1.34      |  1.98   |        0.64       |    1.00     |


where the row label is the item you have and the column label is the item you are converting your currency into. Our goal is to maximize our funds by performing a sequence of trades. We are constrained to start with seashells (the currency on our island) and end with seashells, and we are also constrained to make at most \\( 5 \\) trades.

Because the maximum number of trades allowed is so small, we can directly check the outcome of all legal sequences of trades and choose the sequence that gives us the largest return. We begin by indexing the elements of our table: 

$$0 = \textrm{     Snowballs,     } 1 = \textrm{     Pizza,     } 2 = \textrm{     Silicon Nuggets, and     } 3 = \textrm{     Seashells.}$$

A legal trade sequence is a string of at most \\( 6 \\) digits that starts and ends with \\( 3 \\); for instance, \\( 31023 \\) is a legal sequence of trades, and we can use the table to compute the return by taking the product of the appropriate sequence of elements in the table. The starting and ending digit contain no information, so we can drop them. A legal trade of length \\( N \\) is then represented by a string of length \\( N-1 \\).

We can further reduce the computational time by noting that trading a currency for itself is \\( 1-1 \\), and so a trade that contains the same number multiple times in a row is equivalent to a trade of shorter length; for instance, the returns of \\( 0122 \\) and \\( 012 \\) are identical. The fact that we start and end with seashells means that none of our unique strings should begin or end with \\( 3 \\).

I implement this trading procedure in Python by first enumerating all distinct trades and then computing their returns. There are \\( 3 \\) strategies of length \\( 2 \\) (can you see what they are?), \\( 6 \\) strategies of length \\( 3 \\) (this is also quick to check), \\( 21 \\) strategies of length \\( 4 \\), and \\( 60 \\) strategies of length \\( 5 \\). These can all be checked by considering the possible legal options for each slot in the trade string, treating separately the number of internal insertions of seashells. For instance, the distinct trades of length \\( 5 \\) can be counted as

$$ 3\cdot 2\cdot 2 \cdot 2 {2 \choose 0} + 3\cdot 3\cdot 2 {2 \choose 1} = 60 $$

where the combinatorial pieces come from choosing where in the sequence to place seashells (\\( 3 \\)s) and the multiplicative factors are the numbers of possible choices of currency in each of the other places in the string once the \\(3\\)s have been placed.

In principle, a shorter strategy could be optimal, so all need to be checked. But it turns out that the optimal strategy is \\( 0210 \\), corresponding to the following trade sequence:

$$\textrm{     Seashells     } \rightarrow \textrm{     Snowballs     } \rightarrow \textrm{     Silicon Nuggets     } \rightarrow \textrm{     Pizza     } \rightarrow \textrm{     Snowballs     } \rightarrow \textrm{     Seashells.}$$

This sequence multiplies your starting currency by \\( 1.08868032 \\). Many other participating teams also found this optimal trade, leading to a ~\\( 1000 \\)-way tie in the manual round 1.

# Round 2: Competitive container picking
In this round, \\( 10 \\) containers are presented. Each container has a different amount of seashells inside of it \\( c_i \\). When we select a box, we share its contents with some number of *individuals* \\( n_i \\) (also printed on the box) and also with the percentage of teams that select the same box as we do. To be precise, the payout \\( w_i \\) from selecting box \\( i \\) with \\( c_i \\) seashells and \\( n_i \\) individuals when a fraction \\( f_i \\) of the total number of teams also select that box is

$$w_i = \frac{c_i}{n_i+100f_i}.$$

As a twist, we (and everyone else) also have the option to select a second box, for the "low" price of \\( 50,000 \\) seashells (roughly the amount that was won in the entire first manual round).

To study the structure of this problem, it is useful to find the *Nash equilibrium*, which is a set of strategies for each team whereupon, given everyone else's strategy remains fixed, you can do no better by changing your strategy. The strategy in complicated games is usually a *mixed* one, meaning that each team finds that using a probability distribution supported over different selections of boxes is optimal (rather than simply picking a particular box).

We can start by studying a simplified game, where we are only allowed to pick one of the boxes (it will turn out that this is sufficient to find the Nash equilibrium). Then a *strategy* for a particular team is a set \\( \left\lbrace p_i\right\rbrace \\) of probabilities for picking each box. The expected return of this strategy, given that every other team's own strategy is fixed, is a weighted sum of the returns from each box:

$$\left\langle w\right\rangle=\sum_i\frac{p_i c_i}{n_i+100f_i}.$$

For this strategy to be optimal, our team must not be able to do any better by changing our strategy. However, we may not simply perturb each of the \\( p_i \\) independently, since this would violate the constraint that the probabilities sum to \\( 1 \\). This constraint can be enforced using a Lagrange multiplier. Specifically, we can inspect the function

$$\mathcal{L}=\sum_i\frac{p_i c_i}{n_i+100f_i}-\lambda\left(\sum_ip_i-1\right).$$

Then the statement that we are in an extremum with respect to our strategy is the statement

$$\frac{\partial\mathcal{L}}{\partial p_j}=0 \textrm{    for all    } p_j$$

while the constraint is simultaneously enforced using 

$$\frac{\partial\mathcal{L}}{\partial \lambda}=0.$$

Taking the derivative of \\( \mathcal{L} \\) with respect to an arbitrary \\( p_j \\), we find the relationship

$$\frac{c_j}{n_j+100f_j}=\lambda$$

This gives us an interpretation of \\( \lambda \\): it is the expected return from picking any one of the boxes. The Nash equilibrium is hence achieved when all of the boxes have identical expected value. This can be used to find an equilibrium strategy \\( \left\lbrace p_i\right\rbrace \\) by letting \\( f_i=p_i \\). Now for some concrete numbers: the containers contain the following amounts:

$$c_i=10,000*\left\lbrace 10, 80, 37, 17, 90, 31, 50, 20, 73, 89 \right\rbrace.$$

These are shared with the following numbers of individuals:

$$n_i=\left\lbrace 1, 6, 3, 1, 10, 2, 4, 2, 4, 8 \right\rbrace.$$

Using Mathematica to solve the \\( 10 \\) independent simultaneous equations (constraint + equality of the \\( 10 \\) expectation values), we find the following equilibrium strategy:

$$\left\lbrace p_i\right\rbrace=\left\lbrace 0.018, 0.167, 0.075, 0.038, 0.155, 0.068, 0.102, 0.037, 0.167, 0.172 \right\rbrace$$

and a Lagrange multiplier

$$\lambda=35,248.2.$$

Because the Lagrange multiplier \\( \lambda \\) is less than the cost of opening another container (\\( 50,000 \\) seashells), the Nash equilibrium is indeed this probability distribution over the set of one-container strategies (this should be contrasted with the round 4 result).

It is worth noting that the Nash equilibrium is quoted as a collection of strategies for each of the \\( N \\) teams. Many such collections of strategies can lead to the expected values above and hence a Nash equilibrium. For instance, \\( 1000 \\) teams could play with a mixed strategy equal to the \\( \left\lbrace p_i\right\rbrace \\) above which clearly leads to a net probability of \\( \left\lbrace p_i\right\rbrace \\) selecting each box. Alternatively, in the limit of a large number of teams, an approximate Nash equilibrium is found if exactly \\( p_i N \\) teams play with a pure strategy selecting box \\( i \\), since this leads to the same fraction of teams selecting each box and hence equal expectation values across the boxes. These same considerations also apply in round 4.

Now for the question of which container to pick. If we had strong beliefs about which container others would pick (relative to the Nash equilibrium), we could exploit these to do better than the other teams. However, I assume the other teams are playing optimally and roll a weighted die with the probabilities of the Nash equilibrium strategy. I end up selecting the \\( 9 \\)th box. \\( 24.06\% \\) of the other teams ended up selecting this box, which is significantly higher than the expected (from Nash) of ~\\( 16.7\% \\). As a result, I only earned \\( 26,015 \\) seashells instead of the expected \\( 35,248.2 \\). However, the result that I should definitely *not* pick a second box was correct.

# Round 3: Turtle trading
In this round, there is a "large collection" of turtles that are looking to trade flippers with you. You will set a *bid price*, which is the price you will buy all flippers at. The turtles will sell you their flippers at your bid price as long as it is above their *reserve price*, which can be thought of as the minimal value that each turtle values its flipper. You can then sell the flippers at \\( 320 \\) seashells per flipper. The pdf of reserve prices is (using Iverson bracket notation)

$$\rho(x) = \frac{1}{110}\left[160\leq x \leq 200\textrm{   or   }250\leq x \leq 320\right].$$

We are told the following fact: "The distribution of reserve prices is uniform between \\( 160–200 \\) and \\( 250–320 \\), but none of the Sea Turtles will trade between \\( 200 \\) and \\( 250 \\) due to some ancient superstition." Some teams interpreted the second part of this sentence as an explanation of the strange distribution (why do no turtles have reserve prices between \\( 200 \\) and \\( 250 \\)? Superstition!). However, as it is written, it is implying something more severe: 

1) It specifically says none of the turtles will *trade* between \\( 200 \\) and \\( 250 \\).

2) The turtles *trade* flippers with you at your *bid* price.

This should mean that no turtles will trade with you if you place a bid between \\( 200 \\) and \\( 250 \\). This won't change many of my conclusions, but it does explain some peculiarities in the graphs I will show.

In the first part of this round, this is all of the information we have. We can compute our expected return per flipper (I assume each of the large number of turtles has one flipper to trade). Suppose our bid price is \\( b_1 \\). Then, so long as we don't bid between \\( 200 \\) and \\( 250 \\), our expected return per flipper is the difference between our sale and purchase price, multiplied by the expected fraction of turtles that trade with us:

$$r_1(b_1)=\left(320-b_1\right)\int_{160}^{b_1}\textrm{d}x\textrm{ }\rho(x).$$

Our return is \\( 0 \\) if we bid between \\( 200 \\) and \\( 250 \\). Plotting this gives a maximum of \\( 480/11 \\) seashells per flipper at a first bid price of \\( b_1=200 \\):

<img src="/images/Round3Returns.png" width="700"/>

It is worth noting the local maximum at \\( 265 \\) as well, which becomes important in the second part of the round. In the second part of this round, the rules are largely the same. However, the turtles are becoming pickier. Even if you bid above the reserve price of a turtle, this turtle will only trade with you with probability

$$p(b_2,b_{avg})=\begin{cases}
    1 & b_2\geq b_{avg}, \\ 
    \left(\frac{320-b_{avg}}{320-b_2}\right)^3 & b_2<b_{avg} 
    \end{cases}$$

which heavily suppresses how many turtles will trade with you if you bid below the average second bid. Naturally, your return in this round now depends upon the value of the average second bid:

$$r_2(b_2,b_{avg})=\left(320-b_2\right)p(b_2,b_{avg})\int_{160}^{b_2}\textrm{d}x\textrm{ }\rho(x).$$

We can first plot the optimal second bid as a function of the average second bid by maximizing the above function with respect to \\( b_2 \\) at each \\( b_{avg} \\):

<img src="/images/Round3BestBids.png" width="700"/>

We can also plot the expected returns as a function of the optimal second bid:

<img src="/images/Round3BestReturns.png" width="700"/>

The mean second bid should never be less than \\( 200 \\), since no team will want to bid less than \\( 200 \\) (you will always do strictly worse than picking \\( 200 \\), no matter what the average second bid is, if you do this). At an average bid price of \\( 320-10\times 33^{2/3}=217.117 \\), the optimal choice jumps from bidding \\( 200 \\) to bidding \\( 265 \\); this is the point where the returns at the original maximum of \\( 200 \\) in the first part of this round are suppressed below the returns at the local maximum located at \\( 265 \\). At averages above \\( 265 \\), you always do optimally when you bid the average. I ended up selecting \\( 265 \\) (the highest return for a reasonable range of average bid prices), but this ended up hurting me, as the average second bid was way higher at \\( 286 \\).

Why did people bid so high? The phrasing of how the turtles would actually trade was a bit ambiguous. It turns out that the large population of turtles with only one flipper was the same for the first and second bid, meaning that the turtles with reserve prices in the range \\( [160,200] \\) were no longer available to trade with me when I placed my second bid. This changed the expected payoff in the second round rather dramatically. Rather than a cut-off parabola centered at \\( 265 \\), the expected payoff (before factoring in the average second bid) was a parabola centered at \\( 285 \\) (right in the middle of the second range \\( [250,320] \\)). Applying the above reasoning to this scenario would have led me to a bid of \\( 285 \\).

# Round 4: Competitive suitcase picking (round 2 reprise)
This round ended up being nearly identical to round 2 in theory, but with more interesting results given the selected numbers. There are now \\( 20 \\) suitcases to choose from, with identical rules for sharing as in round 2. However, one can choose to select a second suitcase for a cost of \\( 50,000 \\) seashells, and a third suitcase for an additional \\( 100,000 \\) seashells.  For completeness, the suitcases contain the following amounts:

$$c_i=10,000*\left\lbrace 80, 50, 83, 31, 60, 89, 10, 37, 70, 90, 17, 40, 73, 100, 20, 41, 79, 23, 47, 30 \right\rbrace$$

which are shared with the following numbers of individuals:

$$n_i=\left\lbrace 6, 4, 7, 2, 4, 8, 1, 3, 4, 10, 1, 3, 4, 15, 2, 3, 5, 2, 3, 2 \right\rbrace.$$

Starting with the same philosophy as round 2, we first restrict ourselves to the simpler case of one-suitcase strategies (of which there are only \\( 20 \\)) and find the distribution where their expected values are equal. In this case, the Lagrange multiplier is \\( \lambda = 56,613.8 \\), meaning that a team can improve its earnings by selecting an additional suitcase. This means that the Nash equilibrium does not contain only one-suitcase strategies, in contrast to round 2.

If we do the next-simplest thing, and restrict to one- and two-suitcase strategies, we have \\( 210 \\) possible strategies to pick from (\\( 20 \\) + \\( 20 \\) choose \\( 2 \\)). This means we would need to solve \\( 210 \\) simultaneous equations to find this restricted equilibrium strategy (which means we would need to type them into Python or Mathematica, also). Let's do this in a more creative way, using a stochastic simulation.

Suppose we start a large number of teams \\( N \\) with random selections of boxes (some teams choose one box, others choose two or three). Next, we pick a team at random. Holding all of the other teams' choices of boxes fixed, we allow the selected team to choose its strategy so that they have the largest possible return. We rinse and repeat this procedure, randomly selecting teams and allowing them to adjust their strategy to be optimal.

Now let's look at that simulation in a different way. The number of teams is essentially irrelevant; we are after the probability distribution on the set of all possible strategies. When we select a team at random, that team is using strategy \\( i \\) with probability \\( f_i \\), the total fraction of teams using strategy \\( i \\). The optimal strategy for that team depends only on the \\( \left\lbrace f_i\right\rbrace \\) (since this encodes all the information we need to compute the payoff from each strategy). If this team switches to strategy \\( j \\), then the fraction of teams using each strategy is slightly modified to

$$f_i\rightarrow f_i-\frac{1}{N},   f_j\rightarrow f_j+\frac{1}{N}$$

so \\( N \\) becomes a parameter that controls the size of the perturbations to each \\( f_i \\) when a single team swaps their strategy. Now we notice that we don't need to keep track of what each of the \\( N \\) teams is doing in order to perform a Monte Carlo step – we only need to know the values \\( \left\lbrace f_i\right\rbrace \\). We start by initializing a random initial condition for the \\( \left\lbrace f_i\right\rbrace \\) that sums to \\( 1 \\). Then we update \\( \left\lbrace f_i\right\rbrace \\) based on the above algorithm, selecting a strategy at random from the distribution \\( \left\lbrace f_i\right\rbrace \\) and moving \\( N^{-1} \\) of probability from it into the optimal strategy given the \\( \left\lbrace f_i\right\rbrace \\). This simulation rapidly converges to a fixed distribution.


<iframe width="700" height="700" src="/images/0501NashConvergenceN100000.mp4" frameborder="0" allowfullscreen></iframe>


As we computed directly, the steady-state distribution \\( \left\lbrace f_i\right\rbrace \\) contains two-container strategies. \\( 25\% \\) of teams end up selecting two containers, and none select three. Because this was an involved enough simulation, I ended up picking one of the two-container strategies with highest probability in the Nash equilibrium, expecting it not to be overselected by others (I picked the one that occurred with third-highest probability, but all of them were close, hovering in the ~\\( 0.4 \\)\\( \% \\) range). This involved selecting the suitcases with \\( 730,000 \\) and \\( 790,000 \\) seashells in them. This ultimately led to a payoff of \\( 65,801 \\) seashells, which is higher than the expected value of \\( 50,000 \\) seashells. Both of the containers that I selected ended up being worth more than \\( 50,000 \\) seashells.

With the benefit of hindsight, we can make some exact statements about this problem. An argument to see that \\( 50,000 \\) seashells is the expected payoff at the Nash equilibrium: suppose both one- and two-container strategies are contained in the Nash equilibrium. Switching from one of these one-container strategies to a two-container strategy also in the Nash equilibrium must lead to no change in the expected payoff. Hence every box that is included in the one-container strategies of the Nash equilibrium must have \\( 50,000 \\) expected value. This also shows that three-container strategies cannot be included, since choosing a third box costs double its expected value.

Why do exactly \\( 25\% \\) of teams end up picking two boxes? This has to do with the specific numbers that are given for the contents of the boxes and number of individuals. Call \\( g_i \\) the fraction of teams that select box \\( i \\). Because each individual box must have an expected value of \\( 50,000 \\) seashells by the arguments above, we have

$$\frac{c_i}{n_i+100g_i}=50,000 \implies g_i = \frac{c_i}{5000000}-\frac{n_i}{100}.$$

The sum of all of the \\( g_i \\) need not equal \\( 1 \\), since some teams pick multiple containers. It is, however, equal to \\( 1 + p_2 \\), where \\( p_2 \\) is the probability that a team will select two containers. Inserting the given numbers gives

$$p_2 = \sum_i\left(\frac{c_i}{5000000}-\frac{n_i}{100}\right) - 1 = 0.25,$$

as confirmed by the simulation.

# Round 5: Reading comprehension

In this challenge, you were tasked with reading the magazine "Goldberg" (a parody of Bloomberg) and picking some set of commodities to buy or sell in certain amounts. Essentially, there is a set of news blurbs (e.g. cactus rail spikes are responsible for a train accident) and we are tasked with buying or selling some amount of cactus rail spikes. If you are not diversified in your buying and selling, you are hit with substantial fees: I found that when a given \\( \% \\) of a commodity is bought/sold, the profit is \\( 10,000\times \\)\\( \% \\) while the fee is \\( 120\times \\)\\( \% \\)\\( ^2 \\). I ended up reading the news articles, classifying whether the news was "good" or "bad" (corresponding to "buy" and "sell"), and quantifying my certainty in the situation given the news (some news was more certain than others; some information hinted merely at "rumors").

The major thing that I missed was that we were trading the actual objects, rather than stock in the object. For instance, the commodity "Red Flags" caused a bull stampede, which destroyed most of the Red Flags. I thought I was trading stock in Red Flags (which likely would have gone down), rather than the Red Flags themselves. The supply of Red Flags went down, which drove their prices up. This caused me to switch some of the "buy" and "sell" which led to losses in this manual round.
