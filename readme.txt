My approach to building a sudoku solver was to create a backtracking algorithm using two heuristics which are MRV (Minimum remaining values) and LCV (Least constraint value).  
 
Initially, I began only using the min_empty_cells function to determine the coordinates of the next empty cell which had the least number of possibilities by using the is_valid_move function as a checker. I assigned theses coordinates as row, col in the solve_board function and iterated through numbers 1-9, calling the is_valid_move function to determine whether we could place that value in that current empty cell. We recursively called the solve_board again if the move was valid, proceeding onto the next empty cell and repeating the process. 
If the number was not a valid move, we would backtrack and reset the cell back to zero. 

* Processor : Apple M1 2020 *

My results with only using MRV: 
 
Very Easy: average time 0.005505866666663905 
Easy: average time 0.0027552666666660268 
Medium: average time 0.019018933333334567 
Hard: average time 13.346826666666663 
 
Hard sudoku puzzles 8 and 14 were above the 20 second threshold with solving times of 25.506 and 55.3479 seconds respectively. 
 
Hence, I decided to implement the LCV heuristic to be able to reduce the search space. I modified the min_empty_cell function to return a list of min_possible_values for that empty cell as well. By adding a num_constraint function which counts the number of constraints that given value would introduce if placed at that empty cell. I was able to call this in the solve_board function by ordering the possible_values in ascending order based on their number of constraints. Thus, instead of checking every number from 1-9, I only iterated through the possible value list for that particular empty cell. This helped the function to make progress and potentially find a solution faster, as it avoids dead ends and backtracking as much as possible. If the function is unable to find a solution using the least constraint value, it will try the next least constraint value, and so on, until it either finds a solution or exhausts all possibilities.  
 
My results with MRV & LCV: 
 
Very Easy: average time 0.004872733333333305 
Easy: average time 0.0022963333333333668 
Medium: average time 0.013285199999999974 
Hard: average time 9.246178266666668 
 
As you can see adding an extra heuristic had a significant improvement in solving the Hard puzzles, where the average time decreased by 30.72%. Puzzle 8 and 14 had a total time taken of 17.589 and 37.874 respectively. As a result, we were able to solve puzzle 8 within the 20 second threshold and significantly improve the time solving for the last hard puzzle. 



Below is a brief explanation of each function.

Is_valid_move function : 
 
The is_valid_move function is used to determine whether a particular number can be placed at a specific cell in the sudoku puzzle. It takes in four arguments: sudoku, row, col, and num. 
 
The row and col arguments are the indices of the cell in the puzzle where the function is checking whether the number can be placed. The num argument is the number that is being checked. 
 
The function checks if the number num is already present in the row, column and grid  of the cell being checked. If it is, the function returns False as the number cannot be placed in that cell. It returns True otherwise. 
 
 
Min_empty_cell function:  
 
In this function we used the minimum remaining values heuristic (MNV), because it allowed us to choose the empty cell with the fewest possible values first. Which reduced the number of branches in the search tree, thus reducing the time complexity. 
 
The function first initializes the variables min_remaining_values to 10, min_cell to a tuple of None values, and min_possible_values to an empty list. These variables will be used to track the minimum number of remaining values, the cell with the minimum number of remaining values, and the list of those remaining values, respectively. 
 
The function then iterates through each cell in the puzzle using nested for loops. For each cell, if it is empty (i.e. its value is 0), the function counts the number of possible values that can be placed in that cell by creating a list comprehension [num for num in range(1,10) if is_valid_move(sudoku, i, j, num)] which generates a list of numbers from 1 to 9 that are valid moves in the cell at the current row and column. The function then stores the length of this list in the variable num_possible_values. 
 
If num_possible_values is less than the current value of min_remaining_values, the function updates min_remaining_values to the new value, min_cell to the current cell's coordinates, and min_possible_values to the list of possible values. 
 
Finally, the function returns a tuple containing min_cell and min_possible_values.                   
 
 
Num_constraints:  
 
This function is called num_constraints and it takes in four arguments: sudoku, row, col, and value.  
 
The function returns an integer representing the number of constraints that the value has in the puzzle. A constraint is a condition that must be satisfied for a given cell in the puzzle. In this case, we are counting the number of times value appears in the same row, column, or 3x3 grid as the cell represented by row and col. 
 
 
Solve_board: 
 
The solve_board function is used to solve a given Sudoku puzzle by finding the next empty cell with the minimum number of possible values and trying them one by one in a recursive manner. If a valid solution is found, the function returns True, otherwise it returns False. 
 
This function makes use of the min_empty_cells and is_valid_move functions to determine the next cell to be filled and whether a particular value is valid for that cell. It also makes use of the num_constraints function to sort the possible values for each empty cell in ascending order of the number of constraints they introduce. This means that, when trying to fill in an empty cell with a value, the function first tries the value that introduces the least number of constraints, i.e. the value that conflicts with the fewest other cells in the row, column, and 3x3 grid.  
 
 
Row_checker, col_checker and grid_checker: 
 
These functions check if the given row, column and grid in the sudoku puzzle is valid or not. It does this by extracting all the non-zero values and checks if there are any duplicates among the remaining values. Thus, if there are no duplicates, it returns True. 
 
 
Sudoku_solver: 
 
This function is used to solve a Sudoku puzzle using a backtracking algorithm implemented in the solve_board function. It first attempts to solve the puzzle using the solve_board function. If the function returns False, indicating that the puzzle cannot be solved, the function returns a 9x9 array of -1's. If the puzzle is solved, the function then checks if the solution is a valid Sudoku puzzle by checking that each row, column, and sub-squares using the row_checker, col_checker, and grid_checker functions, respectively. If the solution is a valid Sudoku puzzle, the function returns the solved puzzle. 