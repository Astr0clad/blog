---
layout: post
title: "Advent of Code 2024: Day 1 - Historian Hysteria"
date: 2024-12-01 12:00:00 -0700
tags: advent-of-code-2024
---

# Day 1:  Historian Hysteria

### [See the Problem](https://adventofcode.com/2024/day/1)

## Part 1
Initially approaching this problem looked a lot harder than it ended up being. The problem was primarily just asking to find the total distance between the smallest numbers between the two lists, then the next smallest, and so on. I was given a list in the format:
```
12345   67890
12345   67890
12345   67890
```
The two lists are split using spaces and lines. By utilizing the python `.splitlines()` and `.split()` functions, I was able to split the two lists into two separate lists. Having this done was by far the hardest part of the problem for me, as I had never used the `.splitlines()` function before, so some searching was needed. Thanks to these functions, the numbers could be any length and could be separated by any number of spaces, and the code would still work, making the code-writing even easier!

Next I was able to use the length of the left list to control a for loop and iterate through the two lists, finding the smallest number in each list using `min()`. These smallest numbers could then be added to the total difference using `abs(left - right)` and then the numbers could be removed from the lists. Once the for loop had iterated across the entirety of the lists, the total difference was printed out and the problem was solved.

## Part 2
After having built the confidence from solving part 1, I was able to quickly solve part 2. The problem was asking to find how many times a number in the left list occurred in the right list and then multiply the number of occurrences by the number itself. 

I initially approached the problem by using a nested for loop, iterating through the left list and then iterating through the right list to find the number of occurrences. This worked, but it was not the most efficient way to solve the problem.

```python
for i in range(len(left_column_s)):
    current_left = left_column_s[i]

    count_right = 0
    for right in right_column_s:
        if current_left == right:
            count_right += 1

    similarity += (count_right * current_left)
```

After verifying that this was a valid solution and some thought and research, I was able to come up with a more line-efficient solution. By using the python `count()` function, I was able to count the number of occurrences of each number in the right list.

```python
for i in range(len(left_column_s)):
    current_left = left_column_s[i]

    count_right = right_column_s.count(current_left)

    similarity += (count_right * current_left)
```

While this change did not return an improvement in the time complexity of the solution (both are O(n^2)), it did make the code more readable and easier to understand.

## Conclusion
I got to start quite late (about 1.5 hours before the release of day 2), so my ranking on the main leaderboard is pretty low, however on the private leaderboards with friends I am placed pretty well. Overall, this problem was a great introduction to Advent of Code and I am excited to continue solving the problems presented to me throughout the month 👀. 

Global Leaderboard Part 2 Ranking: 100205

## Solution
```python
left_column_d = []
right_column_d = []

left_column_s = []
right_column_s = []

with open('input.txt', 'r') as file:
    data = file.read()

    lines = data.splitlines()
    for line in lines:
        left, right = line.split()

        left_column_d.append(int(left))
        right_column_d.append(int(right))

        left_column_s.append(int(left))
        right_column_s.append(int(right))


# Part 1

total = 0

for i in range(len(left_column_d)):
    smallest_left = min(left_column_d)
    smallest_right = min(right_column_d)

    total += abs(smallest_left - smallest_right)

    # print(f"{smallest_left} - {smallest_right} = {abs(smallest_left - smallest_right)}")

    left_column_d.remove(smallest_left)
    right_column_d.remove(smallest_right)

print("Total Difference:", total)


# Part 2

similarity = 0

for i in range(len(left_column_s)):
    current_left = left_column_s[i]

    count_right = right_column_s.count(current_left)

    similarity += (count_right * current_left)

print("Total Similarity:", similarity)
```