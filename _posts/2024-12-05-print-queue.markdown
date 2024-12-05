---
layout: post
title: "Advent of Code 2024: Day 5 - Print Queue"
date: 2024-12-05 13:30:00 -0700
tags: advent-of-code-2024
---

### [See the Problem](https://adventofcode.com/2024/day/5)

## Part 1
Today has a ***lot*** of text. It took me like 5-10 minutes to just read and comprehend the prompt. This prompt is to take a set of rules and a set of "updates" (sequence of numbers) to determine whether a given update is valid. Then taking all of the valid updates, I need to find the middle number in each update and sum them. The input was formmated as follows, with the rules being formatted `X|Y` and the updates being formatted `X,Y,Z`:

```
47|53
97|13
97|61

75,47,61,53,29
97,61,53,29,13
75,29,13
```

Parsing the input initially concerned me, however I realized I could use the double `\n` to split the rules and updates into a list of tuples and a list of lists respectively. To approach this problem I decided to check every single rule for each and every position in each update.

```python
def is_valid_update(update):
    position = {page: index for index, page in enumerate(update)}

    for x, y in rules:
        if x in position and y in position:
            if position[x] >= position[y]:
                return False
    return True
```

The `enumarate()` function is amazing and allowed me to convert the update into a dictionary of `{page: index}`. This way I could easily check if the rule was violated. I then looped through all the updates and checked if they were valid. If they were valid, I could return true to count the middle number.

Then to go through all of the updates and find the middle number, I used the following code:

```python
for update in updates:
    if is_valid_update(update):
        middle_page = update[len(update) // 2]
        valid_middle_pages.append(middle_page)
```

This code was able to get the correct answer for part 1, so I was able to move on to part 2.

## Part 2
As I sort of expected, part 2 involved sorting all of the invalid updates. Initially I was kinda of confused as to how to approach this problem, so I decided to attempt to utilize the same paradigm as the previous part. I went individually through each rule at each position to check if the rule was violated. If it was, I swapped the two numbers and continued to loop through the rules until no more swaps were needed.

```python
def fix_update(update):
    update = update[:]
    changed = True

    while changed:
        changed = False

        for x,y in rules:
            if x in update and y in update:
                ix, iy = update.index(x), update.index(y)

                if ix > iy:
                    update[ix], update[iy] = update[iy], update[ix]
                    changed = True
    return update
```

In this code `ix` and `iy` represent the index of x and y respectively, and I believe the rest is self-explanatory. I then looped through all of the updates and checked if they were valid using part 1. If they were not, I fixed them and added the middle number to a list.

```python
for update in updates:
    if is_valid_update(update):
        middle_page = update[len(update) // 2]
        valid_middle_pages.append(middle_page)
    else:
        corrected = fix_update(update)
        middle_page = corrected[len(corrected) // 2]
        corrected_middle_pages.append(middle_page)
```

## Conclusion
I'm quite proud of the simplicity of today's solution. I was able to solve both parts in a reasonable amount of time with just a bit of stress. The concepts took a bit to settle in my brain, and once they did it seemed quite clear on how to solve the problem. In the future it could be fun to learn more about topological sorts and see if that could be applied to this problem.

## Solution

```python
with open('input.txt', 'r') as file: data = file.read()

rules_section, updates_section = data.split("\n\n")
    
# Parse rules
rules = []
for line in rules_section.splitlines():
    x, y = map(int, line.split('|'))
    rules.append((x, y))

# Parse updates
updates = [list(map(int, line.split(','))) for line in updates_section.splitlines()]
  
# Part 1

def is_valid_update(update):
    position = {page: index for index, page in enumerate(update)}

    for x, y in rules:
        if x in position and y in position:
            if position[x] >= position[y]:
                return False
    return True


# Part 2
def fix_update(update):
    update = update[:]
    changed = True

    while changed:
        changed = False

        for x,y in rules:
            if x in update and y in update:
                ix, iy = update.index(x), update.index(y)

                if ix > iy:
                    update[ix], update[iy] = update[iy], update[ix]
                    changed = True
    return update


# run it all here because it is easier
valid_middle_pages = []
corrected_middle_pages = []

for update in updates:
    if is_valid_update(update):
        middle_page = update[len(update) // 2]
        valid_middle_pages.append(middle_page)
    else:
        corrected = fix_update(update)
        middle_page = corrected[len(corrected) // 2]
        corrected_middle_pages.append(middle_page)

print("Part 1:", sum(valid_middle_pages))
print("Part 2:", sum(corrected_middle_pages))
```