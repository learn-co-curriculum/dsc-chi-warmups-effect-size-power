# Power and Effect Size Warmup

![](viz/power.gif)

## Conceptual Writing

![](viz/writing.jpg)

#### 1) What is effect size, and what is its relationship to p-values and significance?  

#### 2) What are three elements that affect power, and how do they do so?


```python
'''
1)



2)


'''
```

## Calculations 

![](viz/nine_thousand.gif)

# Note:

This is an exercise in demonstrating how power affects the success of a test, and does not represent "best practices"

See more at the [end of the exercise](#A-note-on-best-practices)

## A tiff has broken out in the media between city construction workers and human services departments

A federal construction agency believes there's too much bloat in city human services departments.  They point to generally more "junior positions" in construction than in human services, even though there are about the same number of positions total.  

Your task, as a scrappy young member of the Seattle `Human Services` department, is to de-fang that argument.

You consider that a good counter-argument would be that, even though there are more "junior" positions in construction than human services, the construction jobs pay more.

### Imports


```python
#Run cell as-is

#data manip
import numpy as np 
import pandas as pd

#stats
from scipy import stats
from statsmodels.stats.power import TTestIndPower
```

### Import data from data folder

You know what data this is


```python
#your code here
```

#### First: is the argument accurate about Seattle?  How does the % of "junior" positions in `Construction & Services` compare to that in the `Human Services Department`?

- Find all the jobs that are "senior" by selecting those that have "Sr" as the last two characters in `job_title`

- Create a dataframe of jobs in `Construction & Services` that are not "senior" jobs

- Create a dataframe of jobs in `Human Services Department` that are not "senior" jobs

- Calculate the %age of "junior" jobs in `Construction & Services` to see if it's numerically smaller than in the `Human Services Department`


```python
#Your code here
```

#### So Seattle would make a good test case to see if human services "junior" jobs pay worse on average than construction jobs

#### What is our null and alternative hypothesis?


```python
'''
Write hypotheses here
'''
```

#### To find evidence rejecting the idea there are no differences, find the sample size needed to generate an independent t-test w/ power = .8 and $\alpha$ = .05


```python
#Your code here
```

#### If there are enough obs, sample that sample size from each of the `Construction & Inspections` and `Human Service Dept` junior employees

use `random_state=33` so we all get the same employees


```python
#Your code here
```

#### Calculate a statistical test to determine whether to accept or reject the null hypothesis

- Determine what kind test is most appropriate

- Calculate

- Accept or reject the null?


```python
#Your code here
```

#### Hm, you think

Maybe it is accurate to reject the null hypothesis, and our test lacks sufficient power to pick it up

#### Calculate, using 100 different samples (w/ `random_state`$\in$[0:99]), what %age of t-tests we would expect to see as not providing evidence sufficiently different from chance **when we should, in fact, reject the null**


```python
#Your code here
```

#### Ah, we chose a power level admitting 20% of tests which incorrectly fail to reject the null, and our sample was part of the "unlucky" 20%

#### What sample size do we need for a power level of .99?  ($\alpha$ remains at .05)


```python
#your code here
```

#### Sample that number and re-calculate a test (`random_state`==33)


```python
#Your code here
```

#### With a test of sufficient power, what evidence have we found that allows us to counter the argument that, since there are a higher %age of "junior" workers in `Construction & Inspection` compared to `Human Services`, that `Construction & Inspection` workers are underpaid?


```python
'''
Your answer here
'''
```

# A note on best practices

It is not often that we are able to continually sample in order to figure out a test of sufficient power.  Often, that decision must be made *in order to sample*, and you get one shot.

Additionally: adjusting a sample-size to get a test of sufficient power without making other adjustments can be a form of [p-hacking](https://www.textbook.ds100.org/ch/18/hyp_phacking.html)


```python

```
