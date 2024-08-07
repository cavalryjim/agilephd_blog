+++
title = 'Zero-Knowledge Proofs: An Example using Python'
tags = ["ZKPs", "python"]
date = 2024-08-06
+++

Here is a simple Python example that demonstrates a zero-knowledge proof. Keeping with the senario described in [an earlier introduction to zero-knowledge proofs](https://blog.agilephd.com/posts/zero_knowlege_proof/), I will use the color-blind friend and colored balls scenario as the context.

In this example, I will simulate the process where the prover wants to prove to the verifier, who is color-blind, whether he can distinguish between two balls of different colors without revealing which is which.

### The Code

As a note, this code could be utilized in any coding environment but I would recommend a Jupyter Notebook.  A version of the code mentioned in this post can be viewed on [GitHub](https://github.com/cavalryjim/zkp_example).

#### Create the ZKP Class

This class contains two balls and methods to shuffle them and verify their order.

```python
import random

class ZeroKnowledgeProof:
    def __init__(self):
        self.balls = ["Red", "Green"]

    def shuffle_balls(self):
        random.shuffle(self.balls)

    def verify(self, initial_balls, shuffled_balls):
        return initial_balls == shuffled_balls
```

#### Function to Run the ZKP Object

This function runs the zero-knowledge proof multiple times (default 10 iterations). Each iteration involves:
- Shuffling the balls.
- Saving the order of the balls.
- The verifier (simulated here) decides to switch the balls or not.
- Verifying if the prover correctly identifies the switch.

The output will print each iteration's initial and shuffled order, and whether the verification succeeded. If the prover consistently verifies the correct order, the proof is successful, demonstrating the prover can distinguish between the balls without revealing their colors.

```python
def prove_different_colors(zkp, iterations):
    n = 1
    while n <= iterations:
        zkp.shuffle_balls()
        initial_balls = zkp.balls.copy()

        choice = random.choice([True, False])  
        if choice:
            zkp.balls.reverse()

        result = zkp.verify(initial_balls, zkp.balls)
        print(f"Initial: {initial_balls}, Shuffled: {zkp.balls}, Verified: {result}")
        
        if not result:
            return False
        n += 1

    return True
```

To run the above logic, I initialize a zkp object.

```python
zkp = ZeroKnowledgeProof()
```

#### Function to Test the ZKP

Here I am creating a function to test the zero-knowledge proof.  The output will print a success or failure message.

```python
def test_zpk(zkp, iterations=10):
    result = prove_different_colors(zkp, iterations)
    if result == True:
        print("Proof successful: The balls are different colors.")
    else:
        print("Proof failed.")
```

Lastly, I am running the test function multiple times.

```python
for i in range(10):
    test_zpk(zkp, 10)
```

### Conclusion

If you were the color blind friend, how many times would the prover have to provide the correct answer before you believed him?  Statistically, the probability of getting the order of the balls correct once is 50%.  To get the order correct twice, the probability drops to 25%.  Three times would be 12.5%.  The probability of getting the order of the balls correct 10 times in a row would be 0.09%.  As you can see, the probability is trending toward becoming statistically impossible.



