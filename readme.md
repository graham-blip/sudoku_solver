# Sudoku Solver

## Introduction

A Sudoku puzzle is typically a grid that contains 81 cells, with nine rows and nine columns. The grid can be divided into subgrids or locals grids of three by three (total of nine cells). The goal is to fill all the empty cells from 1 to 9 so that no number is repeated in either a single column, row or subgrid.  

In the code below a backtracking recursive algorithm is used to solve sudoku puzzles. The overall premise how this works is as follows: The first empty cell is filled with a number 1 if this choice is compatible with all the existing number then it moves on to the second empty cell, where it places a 1. If a conflict is encounterd then the 1 is earesed and a 2 is palced, after placing the the first "allowed" number in that cell it moves to the next empty cell and starts again. It can be possible that the program backtracks several times before being able to move on (DELAHAYE, 2006).

## Backtracking Recursion Solver 

When running the sudoku the following code is exceted first:


```python
def sudoku_solver(sudoku):
    """
    Solves a Sudoku puzzle and returns its unique solution.

    Input
        sudoku : 9x9 numpy array
            Empty cells are designated by 0.

    Output
        9x9 numpy array of integers
            It contains the solution, if there is one. If there is no solution, all array entries should be -1.
    """
    
    solved_sudoku = sudoku.copy()
    solution = solve(solved_sudoku)
    if np.array_equal(solved_sudoku, sudoku) or not is_allowed(solved_sudoku):
        return np.full((9, 9), -1)
    else
        return solved_sudoku
```

This function takes the initial state with the clues and makes a copy (to not change the origional) and then calles the solve function which executes the sudoku solver. If it checks that either the ouput of the solver is allowed or that it is not the same as the origional state (i.e. no valid solution is avialible) then it outputs a 9 by 9 grid of -1. Otherwise it outputs the solution.

The is_allowed function below, in this case is used to verify that the initial solution is an allowed problem i.e. ensuring that no number is repeated in either its row, column or local grid.


```python
def is_allowed(sudoku):
    '''Explores the row column and 3x3 local grid for the same number to see if it is an allowed problem'''
     # Verify column constraint
    for j in sudoku:
        explored = set()
        for i in j:
            if i > 0:
                if i in explored:
                    return False
                else:
                    explored.add(i)
    # Verify row constraint
    for i in sudoku:
        explored = set()
        for col in i:
            if col > 0:
                if col in explored:
                    return False
                else:
                    explored.add(col)
    # Verify block constraint
    for x in range(3):
        for y in range(3):
            explored = set()
            for i in range(3 * x, 3 * x + 3):
                for j in range(3 * y, 3 * y + 3):
                    if sudoku[i, j] > 0:
                        if sudoku[i, j] in explored:
                            return False
                        else:
                            explored.add(sudoku[i, j])
    # The current puzzle violates no constraints
    return True
```

Then the actual solve function below, is used to solve the sudoku puzzle. It calls a search function that returns two possible results it either finds an empty cell and returns that index or if the entire grid has been filled then nothing is return if that is the case the loop ends and the puzzle has been solved.


```python
def solve(grid):
    '''Backtracking solver
    Performs a recursion until it solves the Sudoku puzzle.
    grid: 9x9 numpy array representing the Sudoku puzzle.
    '''
    searchs = search(grid)
    if not searchs:
        return True
    else:
        row, col = searchs

    for i in range(1,10): 
        if verfiy(grid, i, row, col):
            grid[row][col] = i

            if solve(grid):
                return True

            grid[row][col] = 0

    return False
```

In the solve function it calls two functions the search and the verfiy functions below:


```python
def search(sudoku):
    '''Looks for empty squares and returns the index if empty.'''
    for i in range(0,9,1):
        for j in range(0,9,1):
            if sudoku[i][j]==0:
                return i,j
    return None


def verfiy(sudoku, i, row, col):
    '''Verify that a solution is an allowed move.
    i: the proposed integer to place in the square.
    row: index of the entire row.
    col: list comprehension to keep track of column.'''
    rows = sudoku[int(row)]
    column = [sudoku[r][col] for r in range(0,9,1)]
    if i in rows:
        return False
    if i in column:
        return False
    LocalRow = (row // 3)*3
    LocalColumn = (col // 3)*3
    LocalGrid = [sudoku[x][y] for x in range(LocalRow, LocalRow+3) for y in range(LocalColumn, LocalColumn+3)]
    if i in LocalGrid:
        return False
    return True
```

The search function simply searches for an empty cell and returns the index of that cell. The indexes given by the seach function are then used in the verfiy function which iterates i (the proposed number) from 1 to 9 to verify if that number is a valid solution.  

The if solve(grid) part is the recursive section of the algorithm a brute force method that will run continuously until the puzzle is solved. Each time the function is called it adds a number however, sometimes before the solution is reached the algorithm may add incorrect numbers to the grid.  

In this case the backtracking takes place and then it runs into an issue since at one point all the proposed values of i are not valid solutions. In this case, it returns false. If a false is returned it exits out and goes back to the last value that was changed and then runs an iteration on the next value of i. The grid[row][col] = 0 resets the index, so when a "dead end" is reached it will backtrack and attempt to resolve any incorrect values it previously entered. The repeat process of this will allow it to eventually provide a valid solution to the Sudoku puzzle.

## Refrences

DELAHAYE, J., 2006. The Science Behind Sudoku. [online] Cs.virginia.edu. Available at: <http://www.cs.virginia.edu/~robins/The_Science_Behind_SudoKu.pdf> [Accessed 14 May 2021].
