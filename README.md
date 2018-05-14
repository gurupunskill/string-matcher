# String Matching Using Neuroevolution
## Introduction
This is more of an exercise on neuroevolutionary concepts than a viable, practical application for genetic algorithms. I wanted to build something simple to apply what I've learnt.  

##  Description

### Environment
Given a string S of some length with any alphanumeric characters.
```
	String = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
```
### Individual
Any string of any length is an individual in this environment.  
```
	Individual = "LAKSKDJLKMCasdkjAKSDjkj"
```

### Fitness
The fitness of an individual is proportional to how close it is to the defined string S for this environment.  

```
	Individual = "ABCDEFGHIJKLMNOPQRSTUVWXYS"  
	Fitness(Individual) = (Number of characters in Individual same as String)/(Number of characters in String)  
						  = 25/26
						  = 0.962
```
```python	
	def evaluate_fitness(actual, new):
        together = itertools.zip_longest(actual, new, fillvalue=None)
        cost = len([a for a,b in together if a == b])
        cost /= max(len(actual), len(new))
        return cost
```

### Breeding and Mutation
As per Natural Selection laws, the fittest individuals should survive (along with some random unfit undividuals) and breed to create the next generation of individuals. 

Given two parents, I've defined breeding to select a character from the two parents at random. That is, We would choose the `i`th character of `Parent 1` or the `i`th character of `Parent 2` at random for the `i`th character of the `Child`. 

To simulate mutation, given a `0 < mutation rate < 1`  if a uniformly generated pseudorandom floating point number is greater than this defined `mutation rate`, then an alphanumeric character is randomly chosen to be in that position `i` for the child. I've also used the same `mutation rate` to randomly add or remove an extra character in the child.

```python
	def make_child(parent_1, parent_2):
        longer_parent  = list(parent_1 if len(parent_1) > len(parent_2) else parent_2)
        shorter_parent = list(parent_2 if len(parent_1) > len(parent_2) else parent_1)
    
        child = longer_parent.copy()
        n = len(shorter_parent)
        i = 0
        while i < n:
            child[i] = random.choice(characters) if random.uniform(0, 1) > mutation_rate else child[i]
            
            if (i < len(shorter_parent)):
                child[i] = shorter_parent[i] if random.uniform(0, 1) > 0.5 else longer_parent[i]
            
            if(len(child) < length_max):
                if (random.uniform(0,1) > mutation_rate):
                    child.insert(i, random.choice(characters))
                    n+=1
                
            if (random.uniform(0,1) > mutation_rate):
                child.remove(child[i])
                n-=1
            i+=1
        return "".join(child)
```

After defining these, genrating a random population, choosing the best indiviuals and breeding them to create the next generation is trivial.

## Results
It works. Check the notebook [here]('String Matching'.ipynb)

## Learing outcomes
1. Neuro evolution has a similar shaped learning curve like any other minimization algorithm. It gets harder as you get closer to a perfect value.
2. Without mutation, the algorithm does not progress beyong a point. It would oscillate at a local minima.
3. A very high mutation rate ( a very low mutation rate in this case ==> more mutation) may result in the algorithm never converging. This is mainly because the mutations happen too often and pushes back progress. 
4. As the length of the input string increases, a lower mutation rate (higher mutation in this case ==> less mutation) works phenomenally better while a higher often results in oscillations at a local minima. This is something I found on a purely experimental basis. 
