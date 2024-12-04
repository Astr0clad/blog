---
layout: post
title: "Advent of Code 2024: Day 3 - Mull It Over"
date: 2024-12-03 09:00:00 -0700
tags: advent-of-code-2024
---

# Day 3:  Mull It Over

### [See the Problem](https://adventofcode.com/2024/day/3)

## Part 1
Something unique! Today's problem was quite different compared to the previous two days. In today's first part I was given a long input that represented a program. The idea was to find the `mul()` function and multiply the two numbers given, then keep a running total of all the `mul()` functions. The input was formatted as:

```
xmul(2,4)%&mul[3,7]!@^do_not_mul(5,5)+mul(32,64]then(mul(11,8)mul(8,5))
```

Pretty random if you ask me. When I think random text, I think regular expressions (regex). After opening the txt file and reading the input, I used the `re` module to find all the `mul()` functions:

```python
pattern = r'mul\((\d+),(\d+)\)'
matches = re.findall(pattern, data)
```

Regex sucks to read, but is incredibly powerful. How about we break this down? The `r` before the string is a raw string literal, which means that backslashes are treated as literal characters. The `mul\((\d+),(\d+)\)` pattern is looking for the string `mul(`, followed by two groups of digits separated by a comma, and ending with a closing parenthesis. The `(\d+)` is a capturing group that matches one or more digits. The `re.findall()` method returns a list of tuples containing the two numbers from each `mul()` function.

After capturing all the `mul()` functions, I looped through the matches and multiplied the two numbers together:

```python
total = 0

for match in matches:
    total += int(match[0]) * int(match[1])
```

Bam! We have the solution for Part 1.


## Part 2
Part two of today's challenge appeared like it was going to be the bane of my existence on initial glance. This part introduced enabling (`do()`) and disabling (`don't()`) to the mix. The `mul()` funccions could only be counted if they were enabled. While retaining the same formatting:

```
xmul(2,4)&mul[3,7]!^don't()_mul(5,5)+mul(32,64](mul(11,8)undo()?mul(8,5))
```

At first I thought this would require a complete rewrite because of the complexity of the problem. However, I decided to take a look at some [regex documentation](https://www.regular-expressions.info/quickstart.html). I found the magical pipe (`|`) operator. This acts as an "or" operator in regex. This magical thing allowed me to create a single regex statement that would find all of the matches:

```python
new_pattern = r"mul\(\d+,\d+\)|do\(\)|don't\(\)"
```

The method is the exact same as explained above, but utilizing the pipe operator to find all the `mul()`, `do()`, and `don't()` functions. To keep track of the enabling and disabling while looping through all of the matches, a boolean variable is perfect. Then I can utilize a conditional to enable, disable, or multiply the numbers based on the current match:

```python
enabled = True
for match in matches:
    if match == "do()":
        enabled = True
    elif match == "don't()":
        enabled = False
    else:
        a, b = match.split(",")
        a = a.replace("mul(", "")
        b = b.replace(")", "")

        if enabled:
            total += int(a) * int(b)
```

And that's it! The solution I got for Part 2.


## Conclusion
Today was a fun day that made me dig pretty deep into my knowledge level of regex. I've always been a bit scared of regex, but I think I'm starting to get the hang of it. Day 3 also brought my first sub 1000 part 1 ranking on the global leaderboard, which was pretty exciting.

Global Leaderboard Part 1 Ranking: 526

Global Leaderboard Part 2 Ranking: 2807

## Solution

```python
import re

# Part 1

with open('input.txt', 'r') as file:
    data = file.read()
    
    pattern = r'mul\((\d+),(\d+)\)'
    matches = re.findall(pattern, data)

    total = 0

    for match in matches:
        total += int(match[0]) * int(match[1])

    print("Pt 1 Total:", total)

    # Part 2

    # regex is pretty neat
    new_pattern = r"mul\(\d+,\d+\)|do\(\)|don't\(\)"
    matches = re.findall(new_pattern, data)

    total = 0

    enabled = True
    for match in matches:
        if match == "do()":
            enabled = True
        elif match == "don't()":
            enabled = False
        else:
            a, b = match.split(",")
            a = a.replace("mul(", "")
            b = b.replace(")", "")

            if enabled:
                total += int(a) * int(b)
    
    print("Pt 2 Total:", total)
```