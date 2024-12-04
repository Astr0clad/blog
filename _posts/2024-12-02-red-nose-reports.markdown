---
layout: post
title: "Advent of Code 2024: Day 2 - Red-Nosed Reports"
date: 2024-12-02 09:00:00 -0700
tags: advent-of-code-2024
---

# Day 2:  Red-Nosed Reports

### [See the Problem](https://adventofcode.com/2024/day/2)

## Part 1
Starting with part one of today's challenge was pretty similar to yesterday's challenge. I first needed to parse a text file, this time into a list of lists with numbers. The text file was formatted as:

```
7 6 4 2 1
1 2 7 8 9
9 7 6 2 1
1 3 2 4 5
8 6 4 4 1
1 3 6 7 9
```

The big idea of the problem was to find which rows, or "reports", are safe. To be safe a few conditions must be met:
1. The reports must be ascending or descending (no repeating numbers)
2. Any two adjacent numbers, "levels" must differ by `1 <= x <=3`

I decided to check these sequentially, first checking if the reports were ascending or descending:

```python
is_ascending = all(int(report[i]) <= int(report[i + 1]) for i in range(len(report) - 1))
is_descending = all(int(report[i]) >= int(report[i + 1]) for i in range(len(report) - 1))
```

The `all()` function is pretty neat, it checks if all elements in an iterable are `True`. I used this to check if all the numbers in the report were ascending or descending by creating a conditional that was run using a for loop. Personally, I found this solution to be pretty nifty and pretty simple to read compared to some of the solutions I saw after the fact.

Next, I had to check if the levels were within the range of `1 <= x <= 3`. I did this by checking the difference between each level and the next level:

```python
if is_ascending:
    valid = all(1 <= int(report[i + 1]) - int(report[i]) <= 3 for i in range(len(report) - 1))
elif is_descending:
    valid = all(1 <= int(report[i]) - int(report[i + 1]) <= 3 for i in range(len(report) - 1))
```

Again, utilizing the `all()` method came in handy here. I was able to check if all the differences between the levels were within the range of `1 <= x <= 3` by using a for loop and a conditional. 

Finally, I checked if the report was valid and incremented a counter if it was:

```python
if valid:
    valid_reports += 1
```

## Part 2
Part two of today's challenge kinda broke me. It took me a long time and a lot of trial and error to figure it out. The new problem introduced an extra condition, "The Problem Dampener". This condition allowed one single number/level to be removed to make the other conditions true. I tried a few different approaches to solve this problem, starting by building off the main for loop, but couldn't get that to work. I ended up taking the solution from part 1 and creating a function with it that returned a boolean value on whether or not the report was valid for part 1, as if the report was valid for part 1, it would be valid for part 2. Then I created this nifty little for loop:

```python
for i in range(len(report)):
    modified_report = report[:i] + report[i + 1:]
    if is_safe(modified_report):
        return True
``` 

I very recently learned about the `:` operator in python. For the uninitiated the `:` operator allows you to "splice" a list. For example, `list[1:3]` would return the second and third elements of the list. By looping across the entire report and essentially removing one element at a time, I was able to check if the report was valid for part 2. If it was, I returned `True` and the counter was incremented. The `:` operator allowed me to create a modified report that contained all the values before `i` ([:i]) and all the values after `i` (`[i + 1:]`), effectively removing the `i`th element from the report. 

After way too much time this ended up being the solution when running:

```python
sum(is_safe_with_dampener(report) for report in reports)
```

This `sum()` method will sum up all the `True` values returned by the `is_safe_with_dampener()` function, effectively counting the number of valid reports for part 2.

## Conclusion
Today provided a much more challenging problem than yesterday. I was able to solve part 1 pretty quickly, but part 2 took me a long time to figure out, and through it I learned a lot about the `:` operator and how to use it to splice lists. After I submit my solution, I started looking at other people's solutions and I think that the code I wrote is a lot more readable than theirs, despite their code likely being more efficient. I'm happy with the solution I came up with and I'm excited to see what tomorrow's challenge will bring.

Global Leaderboard Part 2 Ranking: 5849

## Solution
```python
reports = []

with open('input.txt', 'r') as file:
    data = file.read()

    lines = data.splitlines()
    for line in lines:
        levels = line.split()
        reports.append(levels)


# Part 1

total_safe = 0

for report in reports:

    valid = False
    
    is_ascending = all(int(report[i]) <= int(report[i + 1]) for i in range(len(report) - 1))
    is_descending = all(int(report[i]) >= int(report[i + 1]) for i in range(len(report) - 1))

    
    if is_ascending:
        valid = all(1 <= int(report[i + 1]) - int(report[i]) <= 3 for i in range(len(report) - 1))
    elif is_descending:
        valid = all(1 <= int(report[i]) - int(report[i + 1]) <= 3 for i in range(len(report) - 1))

    if valid:
        total_safe += 1
        
print("Pt 1 Total Safe:", total_safe)


# Part 2

def is_safe(report):
    valid = False

    is_ascending = all(int(report[i]) <= int(report[i + 1]) for i in range(len(report) - 1))
    is_descending = all(int(report[i]) >= int(report[i + 1]) for i in range(len(report) - 1))
    
    if is_ascending:
        valid = all(1 <= int(report[i + 1]) - int(report[i]) <= 3 for i in range(len(report) - 1))
    elif is_descending:
        valid = all(1 <= int(report[i]) - int(report[i + 1]) <= 3 for i in range(len(report) - 1))
    
    return valid

def is_safe_with_dampener(report):
    if is_safe(report):
        return True

    for i in range(len(report)):
        modified_report = report[:i] + report[i + 1:]
        if is_safe(modified_report):
            return True

    return False

safe_with_dampener = sum(is_safe_with_dampener(report) for report in reports)

print("Pt 2 Total Safe:", safe_with_dampener)
```