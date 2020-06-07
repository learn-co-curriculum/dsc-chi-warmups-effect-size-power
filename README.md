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


```python
#__SOLUTION__

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


```python
#__SOLUTION__

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


```python
#__SOLUTION__

df = pd.read_csv('data/data.csv')
```

#### First: is the argument accurate about Seattle?  How does the % of "junior" positions in `Construction & Services` compare to that in the `Human Services Department`?

- Find all the jobs that are "senior" by selecting those that have "Sr" as the last two characters in `job_title`

- Create a dataframe of jobs in `Construction & Services` that are not "senior" jobs

- Create a dataframe of jobs in `Human Services Department` that are not "senior" jobs

- Calculate the %age of "junior" jobs in `Construction & Services` to see if it's numerically smaller than in the `Human Services Department`


```python
#Your code here
```


```python
#__SOLUTION__

juniors = df[
    ~df['job_title']
    .isin(
        [x for x 
         in df['job_title'] 
         if x[-2:]=='Sr'
        ]
    )
]

const_jr = juniors[
    juniors.
    department=='Construction & Inspections'
]

const_total = len(df[df.department=='Construction & Inspections'])

hr_jr = juniors[
    juniors.
    department=='Human Services Department'
]

hr_total = len(df[df.department=='Human Services Department'])


print(f'jr jobs in construction and human services: {len(const_jr)} and {len(hr_jr)}')
print()
print(f'and as a %age: {len(const_jr)/const_total}, {len(hr_jr)/hr_total}')
```

    jr jobs in construction and human services: 343 and 319
    
    and as a %age: 0.8932291666666666, 0.8242894056847545


#### So Seattle would make a good test case to see if human services "junior" jobs pay worse on average than construction jobs

#### What is our null and alternative hypothesis?


```python
'''
Write hypotheses here
'''
```


```python
#__SOLUTION__

'''
Null hypothesis: there are no differences in the mean between junior job hourly rate
compensation in construction and in human services

Alternative hypothesis: the mean of junior job hourly rate compensation in
human services is lower than in construction

note: we don't merely want to find evidence that they're *different*; we're specifcally looking
for evidence that human services is *worse*
'''
```

#### To find evidence rejecting the idea there are no differences, find the sample size needed to generate an independent t-test w/ power = .8 and $\alpha$ = .05


```python
#Your code here
```


```python
#__SOLUTION__


diff = const_jr['hourly_rate'].mean() - hr_jr['hourly_rate'].mean()

n1 = len(const_jr['hourly_rate'])
n2 = len(hr_jr['hourly_rate'])
var1 = const_jr['hourly_rate'].var(ddof=1)
var2 = hr_jr['hourly_rate'].var(ddof=1)

# Calculate the pooled variance
pooled_var = ((n1-1) * var1 + (n2-1) * var2) / (n1 + n2 - 2)

# Calculate Cohen's d statistic
cohen_d = diff / np.sqrt(pooled_var)

power_analysis = TTestIndPower()

power = .8
alpha = .05
sample_size = power_analysis.solve_power(effect_size=cohen_d, 
                                         power=power, 
                                         alpha=alpha)

sample_size
```




    19.509552637248213



#### If there are enough obs, sample that sample size from each of the `Construction & Inspections` and `Human Service Dept` junior employees

use `random_state=33` so we all get the same employees


```python
#Your code here
```


```python
#__SOLUTION__

const_jr_sample = const_jr.sample(20, axis=0, random_state=33)
hr_jr_sample = hr_jr.sample(20, axis=0, random_state=33)
```

#### Calculate a statistical test to determine whether to accept or reject the null hypothesis

- Determine what kind test is most appropriate

- Calculate

- Accept or reject the null?


```python
#Your code here
```


```python
#__SOLUTION__

stats.ttest_ind(hr_jr_sample['hourly_rate'], const_jr_sample['hourly_rate'], equal_var=True)

print('''
We do not find evidence to reject the null hypothesis, since the comparison p-value of .13
indicates the probability of finding this result by chance is above the threshold we had 
picked which would indicate sufficient unlikely-ness to show up by chance
''')
```

    
    We do not find evidence to reject the null hypothesis, since the comparison p-value of .13
    indicates the probability of finding this result by chance is above the threshold we had 
    picked which would indicate sufficient unlikely-ness to show up by chance
    


#### Hm, you think

Maybe it is accurate to reject the null hypothesis, and our test lacks sufficient power to pick it up

#### Calculate, using 100 different samples (w/ `random_state`$\in$[0:99]), what %age of t-tests we would expect to see as not providing evidence sufficiently different from chance **when we should, in fact, reject the null**


```python
#Your code here
```


```python
#__SOLUTION__

results = []

for sample in range(0,100):
    hr = hr_jr.sample(20, axis=0, random_state=sample)['hourly_rate']
    const = const_jr.sample(20, axis=0, random_state=sample)['hourly_rate']
    
    result = stats.ttest_ind(hr, const, equal_var=True).pvalue
    results.append(result)
    
print(f'% samples sig diff: {len([x for x in results if x<.05])/100}')
```

    % samples sig diff: 0.82


#### Ah, we chose a power level admitting 20% of tests which incorrectly fail to reject the null, and our sample was part of the "unlucky" 20%

#### What sample size do we need for a power level of .99?  ($\alpha$ remains at .05)


```python
#your code here
```


```python
#__SOLUTION__

power = .99
alpha = .05
sample_size = power_analysis.solve_power(effect_size=cohen_d, 
                                         power=power, 
                                         alpha=alpha)

sample_size
```




    44.293341304655215



#### Sample that number and re-calculate a test (`random_state`==33)


```python
#Your code here
```


```python
#__SOLUTION__

hr = hr_jr.sample(45, axis=0, random_state=33)['hourly_rate']
const = const_jr.sample(45, axis=0, random_state=33)['hourly_rate']
    
stats.ttest_ind(hr, const, equal_var=True)
```




    Ttest_indResult(statistic=-3.396174831151907, pvalue=0.0010273980766671534)



#### With a test of sufficient power, what evidence have we found that allows us to counter the argument that, since there are a higher %age of "junior" workers in `Construction & Inspection` compared to `Human Services`, that `Construction & Inspection` workers are underpaid?


```python
'''
Your answer here
'''
```


```python
#__SOLUTION__


```

# A note on best practices

It is not often that we are able to continually sample in order to figure out a test of sufficient power.  Often, that decision must be made *in order to sample*, and you get one shot.

Additionally: adjusting a sample-size to get a test of sufficient power without making other adjustments can be a form of [p-hacking](https://www.textbook.ds100.org/ch/18/hyp_phacking.html)


```python

```
