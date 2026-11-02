# My Answers - Assignment 1 (CFLP: VNS vs Gurobi)

**Student Information:**
- **Student ID:** 447540240
- **Student Name:** Lama Naif Alnefaie
- **Gurobi Version Used:** [x] Academic [ ] Free
- **Date Completed:** 2026-11-02

All the numbers in my answers are from my own run (`results_student_447540240.csv`, `report_summary_student_447540240.txt` and the plots in `analysis_plots_student_447540240/`). The gap means how much more expensive the VNS solution is compared with the Gurobi optimal one, and the speedup is the Gurobi time divided by the VNS time.

---

## Section A: Solution Quality Analysis (4 marks)

### A.1 (2 marks) - Gap Analysis by Categories

**Analysis of Plot 01:**

In Plot 01 all the boxes are above the zero line, so VNS never reached the optimal cost in any group. The median gap goes up a little when the size gets bigger (small 11.56%, medium 13.07%, large 14.40%), but the difference between the difficulty levels is bigger than the difference between the sizes. Moderate instances are clearly the worst and easy instances are the best.

The median gap for each size and difficulty:

| | Easy | Moderate | Expensive |
|---|---|---|---|
| Small | 10.36 | 14.66 | 11.56 |
| Medium | 10.56 | 17.13 | 14.98 |
| Large | 10.53 | 16.97 | 15.74 |

**Specific Observations:**
- Highest median gap size: Large
- Median gap value: 14.40%
- Effect of difficulty on gap: easy has the lowest median (10.44%) and it stays almost the same for the three sizes, moderate has the highest (15.84%), and expensive is in the middle (13.73%) but it gets worse when the size gets bigger, from 11.56% for small to 15.74% for large.

**Outliers identified:**

The circles in Plot 01 are instance 028 (small, moderate) with 30.29%, which is the highest gap in the whole experiment, instance 098 (large, easy) with 27.74%, and instance 007 (small, easy) with 19.74%. Also the medium-moderate box has a long whisker up to 27.70% (instance 069).

I think these are runs where the starting solution and the local search ended in a bad set of open facilities and the shaking could not get out of it. Instance 098 had 0 improving iterations, so VNS stopped at the first local optimum it found, which was 27.7% above the optimal. Instance 028 had 7 improving iterations but it was still 30% away, so maybe it was searching in the wrong area. I did not test these reasons, so they are only my explanation.

**Interpretation:**

Moderate instances are hard for VNS because the fixed cost is about the same as the service cost, so choosing which facilities to open really matters, and a wrong choice costs a lot. In easy instances the fixed costs are low, so opening many facilities is almost always good and even a greedy start is not far from the best answer. In expensive instances the best solution opens only a few facilities. The gap also grows with the size because there are more possible combinations of facilities, but VNS uses the same budget (100 iterations, and it stops after 20 without improvement).

---

### A.2 (2 marks) - Overall Solution Quality Statistics

**From Excel File (Sheet "All Results"):**

- Total instances analyzed: 144
- Instances with gap < 5%: 1 ( 0.7% )
- Instances with gap < 10%: 33 ( 22.9% )
- Mean gap across all instances: 13.73%
- Standard deviation of gap: 5.19%

I also calculated the median (12.64%), the minimum (4.18%), the maximum (30.29%), 92 instances (63.9%) under 15%, and 16 instances (11.1%) over 20%.

**Interpretation:**

VNS is not giving consistently good solutions here. Only one instance is under 5% and less than a quarter are under 10%, and a normal run ends 13 to 14% more expensive than the optimal. The standard deviation of 5.19 points shows that the quality changes a lot between instances (from 4% to 30%). But the solutions are all feasible and the Gurobi vs VNS cost plot (bottom right of Plot 05) shows that all points are above the perfect-match line and close to it, so VNS is always a bit worse by a similar amount and it is not random. I would say it is a quick approximation and not a near-optimal method.

---

## Section B: Computational Efficiency Analysis (4 marks)

### B.1 (2 marks) - Time Comparison

**Analysis of Plot 02:**

**Size at which VNS becomes significantly faster:**

VNS is faster at the median for all the sizes, and the difference is clearer from Medium. The median times are: small Gurobi 1.32 s and VNS 0.67 s, medium 2.07 s and 1.00 s, large 1.88 s and 1.02 s. For the averages the biggest difference is at Medium (Gurobi 9.10 s and VNS 1.14 s). The right side of Plot 02 shows that the difficulty matters more than the size, because on easy instances Gurobi is faster (0.50 s against 0.65 s) and on expensive instances it is much slower (14.73 s against 1.58 s).

**Most challenging configurations:**
- For VNS: Medium-Expensive with time: 1.78 seconds (then Large-Expensive 1.65 s)
- For Gurobi: Medium-Expensive with time: 22.40 seconds (then Large-Expensive 12.91 s)

**Detailed Comparison:**

Both heatmaps have the darkest cell at Medium-Expensive, so the same instances are hard for both methods, but not in the same way. Gurobi goes from 0.47 s on Medium-Easy to 22.40 s (about 48 times more) and VNS goes from 0.62 s to 1.78 s (about 3 times). The slowest Gurobi runs were 103.5 s (instance 132), 98.7 s (089) and 81.4 s (095), all expensive, and the slowest VNS run was only 4.65 s. In total Gurobi took 910.6 s and VNS 150.0 s, so about 6 times less. VNS time is almost constant because it stops after at most 100 iterations, and Gurobi's time depends a lot on how close the fixed and service costs are.

---

### B.2 (2 marks) - Speedup Analysis

**Analysis of Plot 03:**

**Average speedup factors:**
- Small instances: 4.30x faster
- Medium instances: 6.01x faster
- Large instances: 3.75x faster

(The overall mean is 4.69x, VNS is faster in 110 of 144 instances (76.4%), 16 instances have a speedup above 10x, and the maximum is 58.5x on instance 089.)

**Instances where Gurobi is faster (speedup < 1):**

Yes, there are 34 instances (23.6%): 28 are easy, 4 are moderate and 2 are expensive. They are spread over the sizes (10 small, 12 medium, 12 large), so it is about the difficulty and not the size. Large-Easy is the only cell in the heatmap under 1 (0.76x). What they share is that Gurobi finishes them in less than a second (0.47 to 0.55 s on average for easy), probably because with cheap facilities the problem is easy for the solver (I did not check this in the Gurobi log). VNS always has some fixed work to do (the greedy start, the local search, and at least 20 shaking rounds before it can stop), so it cannot go under about 0.5 s.

**Practical Implications:**

A speedup of 4 to 6 times looks big but the real time saved is small here, about 5.3 s per instance (6.32 s for Gurobi and 1.04 s for VNS). It only matters if the problem must be solved many times, like thousands of scenarios, or if the instances are much bigger than mine. For one planning decision it is better to wait 6 seconds for the exact answer than to lose about 14% of the cost. Also the speedup is not guaranteed, because on easy instances VNS is slower and worse at the same time.

---

## Section C: Scalability Analysis (3 marks)

### C.1 (1.5 marks) - Scalability Behavior

**Analysis of Plot 04:**

**VNS Scalability:**

In the top-left panel of Plot 04 the VNS points (triangles) stay in a narrow band between 0.1 s and 4.65 s, mostly around 1 s, and go up only a little when the problem size (facilities x clients) gets bigger. The average goes from 0.80 s (small) to 1.14 s (medium) to 1.19 s (large).

**Gurobi Scalability:**

The Gurobi points (circles) are spread over three orders of magnitude, from 0.09 s to 103 s for the same problem size, and they do not follow a clear line. The averages are 4.09 s, 9.10 s and 5.78 s, so they go up and then down, because they depend on how many hard (expensive) instances are in each group more than on the size.

**Comparison:**

VNS scales better in my data, because its time is small, stable and almost does not depend on the size, while Gurobi's time is unpredictable and has big outliers. One limit is that my instances are all quite small (between 765 and 1,836 facility-client pairs), so the size range is narrow and I cannot say what happens with much bigger instances from this plot.

---

### C.2 (1.5 marks) - Growth Rate and Predictions

**Observed Growth of Average Runtime (from your results):**

| | Small | Medium | Large | Small -> Medium (x) | Medium -> Large (x) |
|---|---|---|---|---|---|
| VNS avg time (s) | 0.80 | 1.14 | 1.19 | 1.43 | 1.04 |
| Gurobi avg time (s) | 4.09 | 9.10 | 5.78 | 2.23 | 0.64 |

**Shape of growth in Plot 04:**
- VNS: [ ] Roughly steady [x] Steadily increasing [ ] Sharply increasing
- Gurobi: [x] Roughly steady [ ] Steadily increasing [ ] Sharply increasing

**Justification:**

VNS goes up 1.43 times and then 1.04 times, so it increases slowly and starts to flatten. Gurobi's average jumps 2.2 times and then goes down to 0.64, so it does not grow in a regular way. I chose "roughly steady" because the medians are almost the same for medium and large (2.07 s and 1.88 s), and the average is pushed up and down by a few expensive instances that take more than 50 s. So I do not think the Gurobi ratios are a real growth rate, they are mostly noise from the mix of difficulties.

**Rough Prediction for Extra Large Instance (n=50, m=20):**
- Predicted VNS time: about 1.2 to 1.5 seconds
- Predicted Gurobi time: about 3.7 to 7 seconds
- How I computed it: first I used the Medium -> Large ratio again (VNS 1.19 x 1.04 = 1.24 s, Gurobi 5.78 x 0.64 = 3.7 s). Since the Gurobi ratio is under 1, which does not make sense for a bigger problem, I also used the average step from Small to Large (VNS 1.19 / 0.80 = 1.49 in two steps, so about 1.22 per step and 1.45 s, Gurobi 5.78 / 4.09 = 1.41, so about 1.19 per step and 6.9 s). The two methods give the ranges above. Also n=50 clients and m=20 facilities is only 1,000 pairs, which is inside my small range, so I treated "Extra Large" as the next size after my Large group.

**Suitability for Very Large Instances:**

From what I saw, yes, VNS looks better for very large problems, because its time stays low and predictable while Gurobi can jump to 100 s for one hard instance of only about 1,500 pairs, so it will probably get worse for bigger ones. But I did not test large instances, VNS is also about 14% away from the optimal, and its time per iteration will grow with the size because every neighbor needs a full cost and feasibility check.

---

## Section D: Quality-Time Tradeoff Analysis (3 marks)

### D.1 (1.5 marks) - Sweet Spot Identification

**Analysis of Plot 05:**

**Sweet Spot:**

In the top-left panel of Plot 05 most points are between 0.3 and 1.5 s with a gap of 8 to 18%, and there is no run that is both very fast and very accurate, so there is no real sweet spot at the low-gap side. In the efficiency heatmap the best cell is Medium-Expensive (10.18), then Small-Expensive (6.23) and Large-Expensive (5.32). So the best balance is on expensive instances, where Gurobi needs 9 to 22 s on average and VNS gives a solution 12 to 15% above the optimal in 1.3 to 1.8 s. On easy instances the score is about 1 or less (0.68 to 1.03), so VNS has no advantage there.

**Time Savings for High-Quality Solutions (gap < 5%):**

Only one instance has a gap under 5%, instance 016 (small, easy) with 4.18%.
- Average VNS time: 1.02 seconds
- Average Gurobi time: 0.22 seconds
- Absolute savings: -0.80 seconds (negative, VNS is slower)
- Relative savings: -369% (VNS takes about 4.7 times longer)

One instance is not a good sample, so I also looked at the 33 instances with gap under 10%: the average VNS time is 0.89 s and the average Gurobi time is 3.36 s, so the saving is 2.47 s (73.6%).

**Interpretation:**

For a person using it, this means that with this VNS the high quality and the big time saving do not come together. The instances where VNS is close to the optimal are mostly easy ones, which Gurobi also solves in less than a second (20 of the 33 under 10% are easy), and the ones where VNS saves a lot of time are the expensive ones where it loses 12 to 15% in quality.

---

### D.2 (1.5 marks) - Practical Decision Making

**Scenarios Justifying 10-15% Gap:**

1. **Quick what-if planning:** if a planner wants to test many scenarios in a meeting (a new demand level, closing a warehouse, a new location), he needs answers in seconds, and a solution 10 to 15% above the optimal is enough to compare the scenarios, then the best one can be solved exactly at the end.

2. **Urgent re-planning:** if a warehouse is lost or the demand changes suddenly, a feasible plan fast is more important than saving 10% of the cost, and VNS has a predictable time (never more than 4.65 s in my data) while Gurobi can take 100 s.

3. **Inside a bigger algorithm:** if the facility problem is a small part of a simulation or a routing heuristic, it is solved thousands of times, so 4 to 6 times faster per call makes the whole thing possible, and the lost quality is acceptable at that level.

**Recommendation for Real-World Logistics Company:**

For a real company, if the instances are like mine and the decision is strategic (where to open warehouses for years), I recommend Gurobi, because it proved the optimal for all 144 instances in at most 104 s, and a 13.7% average gap on a long-term decision is a lot of money. I would use VNS for fast or repeated decisions, or when the problem gets too big or has extra constraints that make the exact model too slow. A good way is to run VNS first to get a fast feasible solution and then give it to Gurobi as a starting point.

---

## Section E: Algorithm Understanding (3 marks)

### E.1 (1.5 marks) - VNS Neighborhood Structures

(This is based on the functions in the notebook: `n1_open_facility`, `n2_close_open_facility`, `n3_swap_facilities` and `n4_reassign_clients`. After any change of the open facilities the clients are assigned again with a greedy routine that respects the capacity, and a neighbor that breaks a capacity is thrown away.)

**Role of Each Neighborhood:**

**N1 (Open Facility):**
- Purpose: open one closed facility and assign the clients again.
- Contribution: [ ] Intensification [x] Diversification
- Explanation: it is the only move that adds a facility, so it gives more capacity and shorter distances, but it always adds a fixed cost, so it rarely gives an improvement by itself. In the shaking it pushes the search to a different structure.

**N2 (Close Facility):**
- Purpose: close one open facility and open a closed one at the same time (it tries up to 3 closed ones for each open one).
- Contribution: [x] Intensification [ ] Diversification
- Explanation: it is a limited swap that keeps the number of open facilities the same, so it can replace a badly placed facility without paying for a new one. It never closes a facility alone, so the number of open facilities can never go down.

**N3 (Swap Facilities):**
- Purpose: swap an open facility with a closed one, trying all the pairs up to `max_neighbors`.
- Contribution: [ ] Intensification [x] Diversification
- Explanation: it is a wider version of N2 (not limited to 3 closed facilities). The changes are bigger than moving clients, so it explores more, but it costs more because every neighbor needs a full assignment.

**N4 (Reassign Clients):**
- Purpose: move one client to another open facility without changing the open ones.
- Contribution: [x] Intensification [ ] Diversification
- Explanation: it is the smallest move, it only changes the assignment and checks the capacity, so it fine-tunes the service cost for a given set of facilities and does not change the fixed cost.

**How They Complement Each Other:**

N1, N2 and N3 change which facilities are open (the location decision, where the big cost differences are), and N4 improves who is served by whom (small improvements). The local search uses the four in order and starts again from N1 after every success, and the shaking picks one of them randomly. So the facility moves are the ones that jump to new areas and N4 polishes the result. In my opinion the weak point is that N2 and N3 are almost the same move and there is no move to close a facility alone, so the mix is less varied than four neighborhoods sound.

---

### E.2 (1.5 marks) - Convergence Behavior Analysis

**Selected Convergence Plots:**

I used three of the grouped convergence images (16 instances each) that are in `sample_convergence_plots/`: small_easy, medium_expensive and large_moderate. None of my instances shows a smooth slow decrease. The curves are either a flat line or a short staircase, so the "smooth" case here is the flat line (the search converges at once and nothing changes after that).

Counting from the three images and checking the CSV, small-easy has 1 instance out of 16 with an improvement (instance 016), medium-expensive has 9 out of 16, and large-moderate has 0 out of 16. For all 144 instances, 110 (76.4%) had **0** improving iterations after the first local search, and the maximum was 28 (instance 043). The correlation between the number of improving iterations and the final gap is only 0.07.

**Plot 1:** [Instance name: `113_cflp_large_moderate_fac22_cli65`, from the large-moderate image, top-left panel]
- Pattern: [x] Smooth (flat) [ ] Multiple jumps
- Description: the red VNS line is flat at about 22,300 from iteration 0 to 100, and the Gurobi optimal (dashed line) is about 17,865, so the final gap is 24.8%. All 16 panels of this image are flat.
- What it reveals: the first local optimum was never improved by the shaking and the local search (the run stops after 20 iterations without improvement, and the x-axis up to 100 is only the plot range). It shows that for these large moderate instances the local optimum is deep and far from the global one.

**Plot 2:** [Instance name: `088_cflp_medium_expensive_fac22_cli65`, from the medium-expensive image, second row, last panel]
- Pattern: [ ] Smooth [x] Multiple jumps
- Description: the cost starts at about 31,200, goes down to about 30,700 at iteration 2 and again to about 30,575 at iteration 8, and the final gap is 8.1% (the Gurobi optimal is 28,288). Instance `091_cflp_medium_expensive_fac23_cli67` in the same image has a similar staircase (a drop at iteration 3 and a bigger one at iteration 8, final gap 14.6%).
- What it reveals: the improvements come in rare steps and not as a smooth slide, which fits a landscape with many separate local optima, where a better one is found only when a shaking move lands in its area. The total improvement is only about 2% of the cost while the gap that remains is still 8%.

**Plot 3:** [Instance name: `016_cflp_small_easy_fac18_cli62`, from the small-easy image, last panel]
- Pattern: [ ] Smooth [x] Multiple jumps (one late jump)
- Description: the curve is flat at about 12,700 until iteration 5 and then makes one small drop to 12,682, and the final gap is 4.2%, the lowest of all the 144 instances. The other 15 panels of this image are flat lines.
- What it reveals: this is the only small-easy instance where the shaking found something better, and it is also the only run under 5%. It shows that even when VNS improves, the final answer was already very close to the first local optimum.

**Role of Shaking Mechanism:**

In this VNS the shaking takes the best solution found so far, does one random move from one of the four neighborhoods, and then the local search tries to improve it. It is the only way to leave a local optimum, because the acceptance rule keeps only strictly better solutions. The plots show that it works only partly. It made the staircases in medium-expensive (9 out of 16 improved), where there are many facility swaps with different costs, but it did almost nothing in large-moderate (0 out of 16) and small-easy (1 out of 16). My explanation, which I did not test, is that one random move is a very small change, so the local search, which uses all the four neighborhoods, just goes back to the same local optimum. A stronger shaking (several moves together, growing after every failure) would be needed to reach other areas.

---

## Section F: Critical Reflection (3 marks)

### F.1 (1 mark) - Limitations and Improvements

**Main Limitations of Current VNS Implementation:**

1. **Very little searching happens.** In 110 of 144 instances (76.4%) the shaking and local search never found a better solution, so the run did 20 failed iterations and stopped, and the final solution is basically the first local optimum. This explains why the gap is so stable around 14%.

2. **Weak neighborhoods.** There is no move to close a facility without opening another, N2 tries only 3 closed facilities for each open one, and N3 is almost the same as N2. So the search cannot reduce the number of open facilities, which hurts moderate and expensive instances where the optimal opens fewer facilities.

3. **Greedy assignment and first improvement on a small sample.** Every time the open facilities change, the clients are assigned in their normal order to the cheapest facility that still has room, which is not an optimal assignment, so a good set of facilities can look bad. Also only up to 15 random neighbors are checked in each neighborhood, so many improving moves are never seen.

**Suggested Improvements:**

1. Add a real "close facility" move and assign the clients in a better way (for example with a regret order or a small transportation problem). I expect the biggest gain on moderate and expensive instances.

2. Make the shaking stronger and adaptive, changing more facilities after every failure (k = 1, 2, 3 ...) as in the original VNS idea, and sometimes accept a slightly worse solution so the search can leave the first local optimum.

3. Tune the parameters and the stopping rule, with more neighbors in each neighborhood (`max_neighbors`), a bigger `max_no_improve`, and a time limit. VNS uses only about 1 s while Gurobi takes 6 s on average, so there is time left that could be used for a better search.

---

### F.2 (1 mark) - Real-World Deployment Considerations

**Additional Constraints for Real Logistics Company:**

1. **Distance, delivery time windows and service levels:** customers need delivery in a given time, so a client cannot go to a facility that is too far even if it is the cheapest.

2. **Many periods and uncertain demand:** the demand changes with the season and it is not certain, and facilities cannot be opened and closed every month, so the model should decide with scenarios.

3. **Routing and transport capacity:** the assignment cost in this model is a fixed number, but in real life it depends on the routes, the number of vehicles and the loading, so the location and the routing should be solved together.

**Additional Objectives:**

Besides cost, a company also cares about the service level or the maximum delivery time, being safe when something fails (a backup facility), the carbon emissions, a balanced workload between warehouses, and the possibility to expand later. So it becomes a problem with more than one objective, and the managers choose from a set of good trade-offs.

---

### F.3 (1 mark) - Exact Method vs Heuristic in Practice

**Gurobi Optimality Status (from the `gurobi_status` column):**
- Instances solved to proven optimality: 144 out of 144
- Instances stopped at the time limit: 0 out of 144
- Do the time-limit instances share a size/difficulty? Not applicable, since none reached the 300 s limit. The slowest ones (103.5 s, 98.7 s and 81.4 s) were all expensive instances of medium or large size.

**What VNS Gives Up and What It Gains:**

VNS gives up the optimal solution and the guarantee, because its solutions are on average 13.73% more expensive than the optimal (up to 30.29%), it never reached the optimal in my results, and it does not tell how far it is. In exchange it is faster, with 1.04 s on average against 6.32 s (150.0 s in total against 910.6 s, so about 83.5% less), and more predictable (maximum 4.65 s against 103.5 s). In this experiment the exchange is not very attractive, because Gurobi solved every instance to the proven optimal in at most 104 s, so the exact method was affordable, and the time VNS saves (about 5 s per instance) is small compared with the quality it loses.

**Practical Value of Heuristics:**

Even if VNS lost in my experiment, heuristics are important in industry when the exact solver cannot finish, for example with much bigger instances, extra real constraints that make the model very hard, or a very short time limit. They are also useful to give Gurobi a good starting solution, to run many scenarios fast, and when a feasible answer now is better than the optimal one later. What I learned from my results is that a heuristic must be compared with the exact solver on the real instance sizes, and if the exact method works, like here, it should be preferred.

---

## Section G: BONUS - VNS Improvement Challenge (+3 marks, optional)

*Improved notebook: `447540240_Lama_Naif_Alnefaie_Assignment1_IMPROVED.ipynb`. Its results are in the folder `improved_run/` of my repository.*

**A note about the hint in the guide.** The guide says the bug is in the acceptance criterion of `vns_solve_cflp` (the `else` block). In the notebook I received (cell 16) this part is already correct: a new solution is kept only if `new_cost < best_cost`, the `else` only does `no_improve_count += 1`, and the shaking starts from `best_solution`. The code even has comments saying "FIXED" and "DO NOT accept worse solution". So I could not find that bug in my copy. Instead I tested the algorithm on my 144 instances and looked for the real reasons of the 13.7% average gap. I am writing this clearly so the marker can see why my fix is in a different place from the hint.

### G.1 (1 mark) - Bug Identification

**Where is the bug?** (cell / function / line numbers in `Assignment_1.ipynb`):

In two places, both outside the acceptance rule.

1. Cell 7, function `assign_clients_to_nearest_open_facility`. The docstring says it uses "demand-based sorting", but the code does this:

```python
# Process clients in natural order (no sorting) for flexibility
for client_idx in range(num_clients):
    costs = costs_to_open[client_idx]
    sorted_fac_positions = np.argsort(costs)
```

2. Cell 10, function `n2_close_open_facility` ("close facility"). It starts with:

```python
# Need at least 1 open and 1 closed facility for swap
if len(opened_indices) == 0 or len(closed_indices) == 0:
    return neighbors
```

and after that it builds only swaps (close one and open one).

**What does the bug do?**

(1) Every neighbor that changes the open facilities (N1, N2, N3), and also the starting solution, gets its assignment from this function. It takes the clients in the order of their numbers and sends each one to the cheapest facility that still has room, so a client with no good alternative can find its best facility already full. (2) N2 can only swap, so no neighborhood can reduce the number of open facilities. In my results the final VNS solutions had almost all the facilities open (for example 16 of 18, and 21 of 21), and when all are open N2 and N3 return nothing.

There is a third problem that comes from (1). On tight instances (the total capacity is only about 1.11 times the demand) the assignment can fail to place every client, and the original code still returned that solution. When I ran the original code again, 3 of the 144 solutions broke a capacity constraint (instances 075, 116 and 117), and they were still counted in the gap statistics as if they were valid.

**Why does it degrade VNS performance?**

I tested it and did not guess. For 8 instances I took the facilities that VNS opened and computed the best possible assignment for the same set, and the gap went down from 8 to 24% to 2 to 8%. So most of the gap does not come from choosing the wrong facilities but from the poor assignment used to judge them. A good set of facilities looks expensive when it is judged with a bad assignment, so VNS refuses it. Together with the missing close move, this explains why 110 of the 144 original runs (76%) never found any improvement after the first local search (see E.2), and why the original gap was over 20% on 16 instances.

---

### G.2 (1 mark) - The Fix

**Corrected code:**

Fix 1 (cell 7): the clients are taken by decreasing regret (the difference between their second-best and best cost among the open facilities).

```python
if _order == 'regret' and costs_to_open.shape[1] > 1:
    _sorted_costs = np.sort(costs_to_open, axis=1)
    client_order = np.argsort(-(_sorted_costs[:, 1] - _sorted_costs[:, 0]), kind='stable')
else:
    client_order = np.argsort(-client_demands, kind='stable')   # fallback order
all_fit = True
for client_idx in client_order:
    ...
```

Fix 3 (cell 7): if some client did not fit, the function calls itself once with the biggest demand first, which packs tight instances better.

```python
if not all_fit and _order == 'regret':
    return assign_clients_to_nearest_open_facility(service_costs, facility_opened,
                                                   facility_capacities, client_demands, _order='demand')
```

Fix 2 (cell 10): a real "close facility" move is added at the start of `n2_close_open_facility`, and the early return happens only when there is no open facility.

```python
if len(opened_indices) == 0:
    return neighbors
...
if len(opened_indices) > 1:
    for j_open in opened_indices:
        if len(neighbors) >= max_neighbors:
            break
        neighbor_facility_opened = facility_opened.copy()
        neighbor_facility_opened[j_open] = 0          # only CLOSE, nothing is opened
        neighbor_client_assignments = assign_clients_to_nearest_open_facility(
            service_costs, neighbor_facility_opened, facility_capacities, client_demands)
        if is_feasible(neighbor_facility_opened, neighbor_client_assignments, facility_capacities, client_demands):
            neighbors.append((neighbor_facility_opened, neighbor_client_assignments))
```

The fix is more than the 1 to 3 lines that the guide mentions, because the guide talks about a different bug, but each of the three changes is small and local.

**Why does the fix work and why does it not break the rest of the algorithm?**

The regret order puts first the clients that lose the most if they miss their best facility, so the little capacity is not used by clients that have cheap alternatives. The fallback order covers the cases where regret fails. The close move lets the search remove a facility whose fixed cost is not worth what it saves, which the old neighborhoods could not do. Nothing else changes, the functions only get an optional extra argument with a default value, every neighbor is still checked with `is_feasible`, and the acceptance rule, the shaking and the stopping rule are the same. In my local tests all the final solutions of the improved code were feasible (0 of 144 infeasible, against 3 of 144 for the original).

---

### G.3 (1 mark) - Demonstrated Improvement

**Before vs After (same 144 instances, same Gurobi costs, same settings):**

| Metric | Original VNS | Improved VNS |
|---|---|---|
| Mean gap (%) | 13.73 | 8.01 |
| Std of gap (%) | 5.19 | 4.89 |
| Instances with gap < 5% | 1 | 38 |
| Instances with gap > 20% | 16 | 3 |
| Mean VNS time (s) | 1.04 | 2.14 |

More numbers from the same two runs: the median gap went from 12.64% to 6.99%, the instances under 10% from 33 to 102, the minimum gap from 4.18% to 0.07%, and the maximum gap from 30.29% to 25.49%.

| Mean gap (%) | Original | Improved |
|---|---|---|
| Easy | 10.97 | 4.84 |
| Moderate | 16.66 | 11.78 |
| Expensive | 13.57 | 7.40 |
| Small | 12.62 | 7.04 |
| Medium | 13.99 | 7.91 |
| Large | 14.58 | 9.07 |

**Analysis:**

The improvement is clear, the average gap went down by 5.7 points (a 42% reduction), and the improved version is better on 136 of the 144 instances and worse on only 8 (which can be normal because the search uses random choices). The instances under 5% went from 1 to 38, and the ones over 20% from 16 to 3. Easy instances improved the most (10.97% to 4.84%).

It did not reach the target in the guide (from over 20% to under 5% on average). The mean gap is still 8%, and moderate instances are still the hardest (11.78%). I think the reason is that my original code did not have the acceptance bug from the guide, so there was no single big error to remove. The gap that remains probably comes mostly from the greedy assignment, which is still a heuristic (in my quick test on 8 instances, the best assignment for the same open facilities gave 2 to 8%).

The price is running time. The improved VNS takes 2.14 s on average against 1.04 s, because the close moves and the regret calculation add work. VNS is now faster than Gurobi in 54 of 144 instances and not in 110, and the average speedup went from 4.69x to 1.73x. This comparison is only approximate, because Gurobi was run again in the improved run and it was faster in that run (4.87 s on average against 6.32 s), which also makes the speedup smaller. In short, the improved version gives much better solutions for about twice the time, and the speed advantage over Gurobi on these small instances is mostly gone.

**Improved results files (names in your repository):** `improved_run/results_student_447540240.csv`, `improved_run/results_student_447540240.xlsx`, `improved_run/comparison_analysis_student_447540240.png`, `improved_run/analysis_plots_student_447540240/` and the notebook `447540240_Lama_Naif_Alnefaie_Assignment1_IMPROVED.ipynb`.

---

## Additional Comments (Optional)

My instances were smaller than the ranges in the guide (the guide says 30 to 150 facilities and 150 to 1,500 clients, but my files have 15 to 27 facilities and 51 to 80 clients), so I used the real sizes from my own results in all my answers. Gurobi was run with an academic WLS license.

---

## Declaration

I hereby declare that:
- I have completed this assignment independently
- All numerical results are from MY experiments using MY student ID
- All analysis and interpretations are MY own work
- I have not copied results or answers from other students
- I understand the consequences of academic dishonesty

**Signature:** Lama Naif Alnefaie

**Date:** 2026-11-02

---

**END OF MY ANSWERS**
