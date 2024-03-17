from collections import deque

# Define actions and their costs
ACTIONS = [(1, 0, 2), (0, 1, 2), (1, 1, 3)]  # (row_change, col_change, cost)

# FILE_READING
def read_maze(filename):
    with open(filename, 'r') as file:
        rows, cols = map(int, file.readline().split())
        start = tuple(map(int, file.readline().split()))
        goal = tuple(map(int, file.readline().split()))
        maze = [list(map(int, line.strip())) for line in file]
    return rows, cols, start, goal, maze

def is_valid_move(rows, cols, maze, visited, row, col):
    return 0 <= row < rows and 0 <= col < cols and maze[rows - row - 1][col] == 0 and (row, col) not in visited

def bfs(start, goal, maze):
    rows = len(maze)
    cols = len(maze[0])
    queue = deque([(start, 0)])
    visited = set()
    visited.add(start)
    parents = {start: None}
    
    while queue:
        current_pos, cost = queue.popleft()
        if current_pos == goal:
            path = []
            while current_pos is not None:
                path.append(current_pos)
                current_pos = parents[current_pos]
            return cost, path[::-1]
        
        for dr, dc, move_cost in ACTIONS:
            new_row, new_col = current_pos[0] + dr, current_pos[1] + dc
            new_cost = cost + move_cost
            new_pos = (new_row, new_col)
            if is_valid_move(rows, cols, maze, visited, new_row, new_col):
                queue.append((new_pos, new_cost))
                visited.add(new_pos)
                parents[new_pos] = current_pos
    
    return None, None

def display_path(rows, cols, maze, path):
    for r in range(rows):
        for c in range(cols):
            if (rows - r - 1, c) in path:
                print('*', end=' ')
            else:
                print(maze[r][c], end=' ')
        print()



# MAIN
filename = input("Enter Filename: ")
rows, cols, start, goal, maze = read_maze(filename)
print("Rows:", rows)
print("Cols:", cols)
print("Start coordinates:", start)
print("Goal coordinates:", goal)
print("Maze:")
for i in maze:
    print(i)

min_cost, path = bfs(start, goal, maze)
if min_cost is not None:
    print("Minimum cost:", min_cost)
    print("Path:", path)
    print("Maze with path marked by '*':")
    display_path(rows, cols, maze, path)
else:
    print("No path found.")
