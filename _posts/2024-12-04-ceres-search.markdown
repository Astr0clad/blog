---
layout: post
title: "Advent of Code 2024: Day 4 - Ceres Search"
date: 2024-12-04 12:00:00 -0700
tags: advent-of-code-2024
---

### [See the Problem](https://adventofcode.com/2024/day/4)

## Part 1
Oh my, this is going to be a struggle... Today's problem is a bit different. I was given a grid of characters and I had to find the word "XMAS" in all directions. The directions included horizontally, vertically, and diagonally. The input was formatted as:

```
MMMSXXMASM
MSAMXMSMSA
AMXSXMAAMM
MSAMASMSMX
XMASAMXAMM
XXAMMXXAMA
SMSMSASXSS
SAXAMASAAA
MAMMMXMMMM
MXMXAXMASX
```

My initial approach was this:
```python
count = 0
rows = len(lines)
cols = len(lines[0]) if rows > 0 else 0


# Check horizontal and reversed horizontal
for line in lines:
    count += line.count('XMAS')
    count += line[::-1].count('SAMX')

# Check vertical and reversed vertical
for col in range(cols):
    vertical = ''.join(lines[row][col] for row in range(rows))
    count += vertical.count('XMAS')
    count += vertical[::-1].count('SAMX')

# Check diagonal and reversed diagonal
for d in range(-rows + 1, cols):
    diagonal = ''.join(lines[i][i - d] for i in range(max(0, d), min(rows, cols + d)))
    count += diagonal.count('XMAS')
    count += diagonal[::-1].count('SAMX')

# Check anti-diagonal and reversed anti-diagonal
for d in range(rows + cols - 1):
    anti_diagonal = ''.join(lines[i][d - i] for i in range(max(0, d - cols + 1), min(rows, d + 1)))
    count += anti_diagonal.count('XMAS')
    count += anti_diagonal[::-1].count('SAMX')
```
This approach didn't work, but I will explain how it was *intended* to work anyways. The idea was to loop through the grid and check for the word "XMAS" in all directions. The first loop checked horizontally and reversed horizontally. The second loop checked vertically and reversed vertically. The third loop checked diagonally and reversed diagonally. The fourth loop checked anti-diagonally and reversed anti-diagonally. The count was incremented for each instance of "XMAS" or "SAMX" found. The way I was checking was creating a new string of characters by iterating through the grid to format as needed, e.g., vertical, diagonal as a row, etc. Then I could use the `count()` method to find the number of occurrences. After getting a fail, I decided to try a different approach.

```python
# Directions: (row step, col step)
directions = [
    (0, 1),  # Horizontal, right
    (0, -1), # Horizontal, left
    (1, 0),  # Vertical, down
    (-1, 0), # Vertical, up
    (1, 1),  # Diagonal, down-right
    (1, -1), # Diagonal, down-left
    (-1, 1), # Diagonal, up-right
    (-1, -1) # Diagonal, up-left
]

word = "XMAS"
word_length = len(word)
rows = len(grid)
cols = len(grid[0])
count = 0

# Check each starting point in the grid
for row in range(rows):
    for col in range(cols):
        for dr, dc in directions:
            # Check if the word fits in this direction
            match = True
            for i in range(word_length):
                r, c = row + dr * i, col + dc * i
                if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != word[i]:
                    match = False
                    break
            if match:
                count += 1
```

This approach is kind of hard to read, but essentially what is happening is that I am iterating across every single cell in the grid. For each cell, I am checking in all directions to see if the word "XMAS" can be found. The `directions` list contains tuples that represent the row and column steps to take in each direction. I then loop through each direction and check if the word fits in that direction. If it does, I increment the count. This approach is somewhat similar to the previous approach, but a bit more elegant. This solution worked and I was able to get the correct answer for Part 1.

## Part 2
I didn't think it would get a lot harder than the first part, but here we are. Now we need to only find MAS in an X pattern in the same input:

```
M.S
.A.
M.S
```

I honestly didn't really know how to go about this programatically, so I decided to try a brute force approach. I looped through the grid and checked for the pattern in all directions. My first attempt is as follows:

```python
rows = len(grid)
cols = len(grid[0])
count = 0

for row in range(1, rows - 1):
    for col in range(1, cols - 1):
        # middle letter is A
        if grid[row][col] == 'A':
            continue

        # check 4 possible MAS pairs
        # MAS top-left to bottom-right
        if (
            grid[row - 1][col - 1] == 'M' and grid[row + 1][col + 1] == 'M' and
            grid[row - 1][col + 1] == 'S' and grid[row + 1][col - 1] == 'S'
        ):
            count += 1
        
        # SAM at top-left to bottom-right
        if (
            grid[row - 1][col - 1] == 'S' and grid[row + 1][col + 1] == 'S' and
            grid[row - 1][col + 1] == 'M' and grid[row + 1][col - 1] == 'M'
        ):
            count += 1
        
        # MAS at top-right to bottom-left
        if (
            grid[row - 1][col + 1] == 'M' and grid[row + 1][col - 1] == 'M' and
            grid[row - 1][col - 1] == 'S' and grid[row + 1][col + 1] == 'S'
        ):
            count += 1
        
        # SAM at top-right to bottom-left
        if (
            grid[row - 1][col + 1] == 'S' and grid[row + 1][col - 1] == 'S' and
            grid[row - 1][col - 1] == 'M' and grid[row + 1][col + 1] == 'M'
        ):
            count += 1
```
I'm not 100% sure why, but on this code did not yield the correct response. I probably could actually read over it again to figure it out, but that is a lot of effort, and I decided to start from scratch. For my second attempt here, I rewrote the entire solution for both parts. I went for the good old case matching paradigm. You can see that solution down in the solution section of this post. This attempt provided the correct answer for part 2, so I was once again able to finish a day.

## Conclusion
The solution I got is not elegant, it is not pretty, it was not fun to write, and it is not very robust. However, it worked, and that is all that matters. I am happy that I was able to finish the day and get the correct answer. Let's see what happens tomorrow...

## Solution

```python
data = None

with open('input.txt', 'r') as file: data = file.read()

pt1 = 0
pt2 = 0

G = data.split('\n')
R = len(G)
C = len(G[0])

for r in range(R):
    for c in range(C):
        # Check for "XMAS" horizontally
        if c+3<C and G[r][c]=='X' and G[r][c+1]=='M' and G[r][c+2]=='A' and G[r][c+3]=='S':
            pt1 += 1
        # Check for "XMAS" diagonally down-right
        if r+3<R and c+3<C and G[r][c]=='X' and G[r+1][c+1]=='M' and G[r+2][c+2]=='A' and G[r+3][c+3]=='S':
            pt1 += 1
        # Check for "SAMX" vertically down
        if r+3<R and G[r][c]=='S' and G[r+1][c]=='A' and G[r+2][c]=='M' and G[r+3][c]=='X':
            pt1 += 1
        # Check for "XMAS" vertically down
        if r+3<R and G[r][c]=='X' and G[r+1][c]=='M' and G[r+2][c]=='A' and G[r+3][c]=='S':
            pt1 += 1
        # Check for "SAMX" horizontally
        if c+3<C and G[r][c]=='S' and G[r][c+1]=='A' and G[r][c+2]=='M' and G[r][c+3]=='X':
            pt1 += 1
        # Check for "SAMX" diagonally down-right
        if r+3<R and c+3<C and G[r][c]=='S' and G[r+1][c+1]=='A' and G[r+2][c+2]=='M' and G[r+3][c+3]=='X':
            pt1 += 1
        # Check for "SAMX" diagonally up-right
        if r-3>=0 and c+3<C and G[r][c]=='S' and G[r-1][c+1]=='A' and G[r-2][c+2]=='M' and G[r-3][c+3]=='X':
            pt1 += 1
        # Check for "XMAS" diagonally up-right
        if r-3>=0 and c+3<C and G[r][c]=='X' and G[r-1][c+1]=='M' and G[r-2][c+2]=='A' and G[r-3][c+3]=='S':
            pt1 += 1

        # Check for "MAS" in a 3x3 square pattern for pt2
        if r+2<R and c+2<C and G[r][c]=='M' and G[r+1][c+1]=='A' and G[r+2][c+2]=='S' and G[r+2][c]=='M' and G[r][c+2]=='S':
            pt2 += 1
        # Check for "SAM" in a 3x3 square pattern for pt2
        if r+2<R and c+2<C and G[r][c]=='S' and G[r+1][c+1]=='A' and G[r+2][c+2]=='M' and G[r+2][c]=='S' and G[r][c+2]=='M':
            pt2 += 1
        # Check for "MAS" in a 3x3 square pattern for pt2
        if r+2<R and c+2<C and G[r][c]=='M' and G[r+1][c+1]=='A' and G[r+2][c+2]=='S' and G[r+2][c]=='S' and G[r][c+2]=='M':
            pt2 += 1
        # Check for "SAM" in a 3x3 square pattern for pt2
        if r+2<R and c+2<C and G[r][c]=='S' and G[r+1][c+1]=='A' and G[r+2][c+2]=='M' and G[r+2][c]=='M' and G[r][c+2]=='S':
            pt2 += 1


print(f'Pt 1: XMAS found {pt1} times')
print(f'Pt 2: X-MAS found {pt2} times')
```