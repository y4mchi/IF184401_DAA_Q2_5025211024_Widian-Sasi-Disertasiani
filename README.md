# IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani
I made a program called maze solver for this QUIZ 2 assigment. I’m using DFS, BFS, and A*search to execute the program. Before I elaborate the program, I will explain DFS, BFS and A*search first. DFS is an algorithm used for traversing or searching tree or graph data structures. DFS explores a graph by visiting vertices (or nodes) and following their adjacent edges until it reaches a dead end, and then backtracking to visit other unexplored vertices. BFS is an algorithm used for traversing or searching tree or graph data structures. Unlike Depth-First Search (DFS) that explores a graph by going as deep as possible before backtracking, BFS explores the graph in a breadthward motion, visiting all vertices at the same level before moving to the next level. A*search is an informed search algorithm used for finding the shortest path between a starting vertex and a goal vertex in a graph or a weighted grid. It is widely used in pathfinding and graph traversal problems.

How my program works? First I put walls to make maze, after I put walls I choose which Algorithm I’d like to use, then my program will start to scanning the shortest path based on the algorithm that I’ve choose, then it will marked up the shortest path for us to see. 

There are few classes that I made to create my maze, such as Dropdown, checkbox, home page, play button, solve button, mouse trigger, so if I clicked on my left mouse, it will put walls, if I clicked on my right mouse, it will delete the walls. To prevent missclick while processing the maze, I blocked the mouse button while it processing. 

## DESIGN
```c 
class Maze():
    def __init__(self, board, algorithm):
        self.board = board
        self.start = (0,0)
        self.goal = (9,9)
        self.algorithm = algorithm
        self.walls = []
        self.height = self.width = len(self.board)

        for i in range(self.height):
            row = []
            for j in range(self.width):
                if self.board[i][j] and (i,j) not in (self.start, self.goal):
                    row.append(True)
                else:
                    row.append(False)
            self.walls.append(row)
```
Maze class represents a maze-solving problem. It takes a board, which is a 2D grid representing the maze, and an algorithm parameter to determine the search strategy.“__init__(self, board, algorithm)” this is the constructor method that initializes the Maze object. It takes a board (a 2D grid representing the maze) and an algorithm parameter to determine the search strategy. It also sets the start and goal positions, initializes the walls list based on the board, and sets the height and width attributes. 

```c
def find_neighbors(self, state):

        self.neighbors = []

        x,y = state

        possible_actions = [
            ('down', (x, y+1)),
            ('right', (x+1, y)),
            ('up', (x, y-1)),
            ('left', (x-1, y))
        ]

        random.shuffle(possible_actions)

        for action, result in possible_actions:
            x, y = result
            try:
                if not self.walls[x][y] and (0 <= x < self.width) and (0 <= y < self.height):
                    self.neighbors.append((action, result))
            except IndexError:
                continue

        return self.neighbors

```
This method finds the neighboring cells for a given state (position) in the maze. It generates a list of possible actions and their resulting positions. It shuffles the possible actions randomly to introduce randomness in the search. It then checks if each resulting position is valid (not a wall and within the maze boundaries) and adds it to the neighbors list. Finally, it returns the list of valid neighboring cells.

```c
def solve(self, show_steps):

        self.cost = 0
        
        self.cells = list() if show_steps else set()

        start = Node(parent = None, state = self.start, action = None)

        if self.algorithm == 1:
            frontier = StackFrontier()
        elif self.algorithm == 2:
            frontier = QueueFrontier()
        elif self.algorithm == 3:
            frontier = ManhattanFrontier()

        frontier.add(start)
        
        while True:
            if frontier.empty():
                return None

            self.cost += 1

            nodes = frontier.remove(self.cost) if self.algorithm == 3 else frontier.remove()

            for node in nodes:

                if self.goal == node.state:
                    moves = []
                    while node.parent != None:
                        moves.append(node.state)
                        node = node.parent
                    moves.reverse()
                    if show_steps:
                        return (self.cells, moves)
                    else:
                        return moves

                if show_steps and node.state not in self.cells:
                    self.cells.append(node.state)
                if not show_steps:
                    self.cells.add(node.state)

                for action, state in self.find_neighbors(node.state):
                    if not frontier.contains_state(self.goal) and state not in self.cells:
                        child = Node(parent = node, state = state, action = action)
                        frontier.add(child)
```
This method solves the maze problem. It initializes the cost variable to keep track of the number of nodes explored. If show_steps is True, it initializes an empty list cells to store the visited cells, otherwise, it initializes an empty set cells. It creates a start node and initializes the frontier based on the selected algorithm. The method then enters a loop where it checks if the frontier is empty. If it is, it means there are no more nodes to explore, and the method returns None. The method retrieves nodes from the frontier, incrementing the cost by 1 for Manhattan distance-based frontier (ManhattanFrontier). For each node, it checks if it has reached the goal state. If it has, it traces back the path from the goal node to the start node and returns the path as a list of moves. If show_steps is True, it returns a tuple containing the visited cells and the path moves. If show_steps is False, it returns only the path moves. For each node, it adds the node's state to the cells list or set, depending on the value of show_steps. Finally, it expands the current node by generating child nodes from the valid neighboring cells and adds them to the frontier if they are not already in the cells and the goal state is not in the frontier.

```cclass DropDown():

    def __init__(self, x,y,w,h,color, highlight_color, font, options, selected = 0):
        self.color = color
        self.highlight_color = highlight_color
        self.rect = pygame.Rect(x,y,w,h)
        self.font = font
        self.options = options
        self.selected = selected
        self.draw_menu = False
        self.menu_active = False
        self.active_option = -1
    
    def draw(self, surface):
        pygame.draw.rect(surface, self.highlight_color if self.menu_active else self.color, self.rect)
        pygame.draw.rect(surface, (0,0,0), self.rect, 2) #border
        msg = self.font.render(self.options[self.selected], 1, (0,0,0))
        surface.blit(msg, msg.get_rect(center = self.rect.center))

        if self.draw_menu:
            for i, text in enumerate(self.options):
                rect = self.rect.copy()
                rect.y += (i+1) * self.rect.height
                if i == 0:
                    pygame.draw.rect(surface, (200,200,200), rect)
                else:
                    pygame.draw.rect(surface, self.highlight_color if i == self.active_option else self.color, rect)

                msg = self.font.render(text, 1, (0,0,0))
                surface.blit(msg, msg.get_rect(center = rect.center))
            outer_rect = (self.rect.x, self.rect.y + self.rect.height, self.rect.width, self.rect.height * (len(self.options)))
            pygame.draw.rect(surface, (0,0,0), outer_rect, 2) 

        else:
            for i in range(len(self.options)):
                rect = self.rect.copy()
                rect.y += (i+1) * self.rect.height
                pygame.draw.rect(surface, (0,0,0), rect)

        
    def update(self, events):
        mpos = pygame.mouse.get_pos()
        self.menu_active = self.rect.collidepoint(mpos)

        for i in range(len(self.options)):
            rect = self.rect.copy()
            rect.y += (i+1) * self.rect.height
            if rect.collidepoint(mpos):
                if i != 0:
                    self.active_option = i
                    break

        if not self.menu_active and self.active_option == -1:
            self.draw_menu = False

        for event in events:
            if event.type == MOUSEBUTTONDOWN and event.button == 1:
                if self.menu_active:
                    self.draw_menu = not self.draw_menu
                elif self.draw_menu and self.active_option >= 0:
                    self.selected = self.active_option
                    self.draw_menu = False
                    return self.active_option

        return -1
```
DropDown class provides functionality to create and interact with a dropdown menu. It allows for selecting an option from the menu and provides methods to draw the menu and update its state based on user input. __init__(self, x, y, w, h, color, highlight_color, font, options, selected=0) this is the constructor method that initializes the DropDown object. It takes various parameters such as the position (x and y), width (w) and height (h) of the dropdown menu, colors for the menu (color and highlight_color), font style (font), a list of options to display in the menu, and an optional selected index to set the initial selected option. Draw(self, surface) this method is responsible for drawing the dropdown menu on the specified surface. It uses the pygame library to draw rectangles and text. It draws the main dropdown menu box using the provided colors and font. If the menu is activated, it also draws the individual option boxes for each option in the menu. The selected option is highlighted, and the other options are displayed with the regular color. The method also draws a border around the menu. Update(self, events) this method updates the state of the dropdown menu based on the events that occurred. It takes a list of pygame events as input. It checks the mouse position and determines if the menu is active (mouse is over the menu). It also checks if any individual option in the menu is active (mouse is over an option). If an option is active, it sets the active_option attribute to the index of that option. The method also handles mouse button events. If the menu is active and the left mouse button is clicked, it toggles the draw_menu attribute to show or hide the individual options. If the menu is already open and an option is selected (the mouse is over an option), it sets the selected attribute to the index of the selected option, hides the menu, and returns the index of the selected option. Finally, the method returns -1 if no option is selected or the menu is not activated. 

```c
class Checkbox():
    def __init__(self, rect, text, font):
        self.rect = rect
        self.fill_rect = pygame.Rect(self.rect.x + 5, self.rect.y + 5, self.rect.width // 1.6, self.rect.height // 1.6)
        self.selected = False
        self.hovered = False
        self.font = font
        self.filled_color = (232, 44, 100)
        self.empty_color = (255,255,255)
        self.text = text

    def draw(self, surface):
        pygame.draw.rect(surface, self.empty_color, self.rect)
        if self.hovered:
            pygame.draw.rect(surface, self.filled_color, self.fill_rect)
        if self.selected:
            pygame.draw.rect(surface, self.filled_color, self.fill_rect)
        msg = self.font.render(self.text, 1, (255,255,255))
        surface.blit(msg, (self.rect.x + 30, self.rect.y + 2.5))
        

    def update(self, events):

        mpos = pygame.mouse.get_pos()

        if self.rect.collidepoint(mpos):
            self.hovered = True
        else:
            self.hovered = False

        for event in events:
            if self.rect.collidepoint(mpos):
                if event.type == MOUSEBUTTONDOWN and event.button == 1:
                    self.selected = not self.selected
                    break

        return self.selected
        
```
Checkbox class provides functionality to create and interact with a checkbox. It allows for toggling the state of the checkbox and provides methods to draw the checkbox and update its state based on user input.

## IMPLEMENTATION
```C
#DEPTH FIRST SEARCH (DFS)
class StackFrontier():
    def __init__(self):
        self.frontier = []
        
    def add(self, node):
        self.frontier.append(node)

    def contains_state(self, state):
        return any(node.state == state for node in self.frontier)

    def empty(self):
        return len(self.frontier) == 0

    def remove(self):
        if self.empty():
            raise Exception('Frontier already empty!')
        else:
            node = self.frontier[-1]  
self.frontier.remove(node)   return [node]

```
__init__(self) Initializes the frontier list as an empty list. add(self, node) adds a node to the frontier by appending it to the frontier list. contains_state(self, state) checks if the frontier contains a node with a specific state. It returns True if any node in the frontier list has a matching state, otherwise False. empty(self) checks if the frontier is empty. It returns True if the frontier list is empty, otherwise False. remove(self) removes and returns the last node (top) from the frontier, following the Last-In-First-Out (LIFO) principle of a stack. It raises an exception if the frontier is already empty.

```C
#BREADTH FIRST SEARCH (BFS)
class QueueFrontier(StackFrontier):
    def remove(self):
        if self.empty():
            raise Exception('Frontier already empty!')
        else:
            node = self.frontier[0] 
            self.frontier.remove(node)
            return [node]
```
The remove method in the QueueFrontier class removes and returns the first node from the frontier, rather than the last node as in the StackFrontier class. It follows the FIFO principle of a queue by removing the node from the beginning of the frontier list (index 0). By inheriting from the StackFrontier class and overriding the remove method, the QueueFrontier class provides the functionality required for maintaining a frontier using a queue in the BFS algorithm.

```C
#A* SEARCH FRONTIER (MANHATTAN DISTANCE HEURISTIC FUNCTION)
class ManhattanFrontier(StackFrontier):
    def remove(self, cost):
        if self.empty():
            raise Exception('Frontier already empty!')
        else:
            best_neighbor = set()
            best_neighbor_cost = 0
            for node in self.frontier:
                x,y = node.state
                absolute_cost = cost + abs(x-9) + abs(y-9)
                if len(best_neighbor) == 0 or absolute_cost < best_neighbor_cost:
                    best_neighbor = {node}
                    best_neighbor_cost = absolute_cost
                elif absolute_cost == best_neighbor_cost:
                    best_neighbor.add(node)

            for neighbor in best_neighbor:
                self.frontier.remove(neighbor)

            return best_neighbor
```
The remove method in the ManhattanFrontier class selects the neighbor nodes from the frontier that have the lowest cost based on the Manhattan distance heuristic. It iterates over each node in the frontier list and calculates the Manhattan distance between the node's state and the goal state (9, 9). The absolute cost is obtained by adding the current cost (passed as an argument) to the Manhattan distance. The method keeps track of the neighbor nodes with the lowest cost using the best_neighbor set and best_neighbor_cost variable. If a neighbor has a lower cost than the current best neighbor, the best_neighbor set and best_neighbor_cost are updated accordingly. If a neighbor has the same cost as the current best neighbor, it is added to the best_neighbor set. After iterating through all the nodes, the method removes the selected best neighbor nodes from the frontier and returns them as a set. By inheriting from the StackFrontier class and overriding the remove method, the ManhattanFrontier class provides the functionality required for maintaining a frontier using the A* Search algorithm with the Manhattan distance heuristic.

```size = (900,600)
w,h = size

YELLOW = (255,255,0)
WHITE = (255,255,255)
GREEN = (0,255,0)
LGREEN =(140, 199, 57)
BLACK = (0,0,0)
RED = (252, 3, 73)
ORANGE = (255,125,123)
PURPLE = (81,61,130)
PINK = (255, 61, 161)
BLUE = (102, 223, 250)
```

Defining size and colors that we will used for our program.

```C
def draw_board():
    for row in range(1,11):
        boxes = []
        for col in range(1,11):
            box = pygame.Rect(50*row,50*col,50,50)
            if (row, col) not in ((1,1),(10,10)):
                pygame.draw.rect(screen, PURPLE, box, 3)
            else:
                pygame.draw.rect(screen, RED, box)
                pygame.draw.rect(screen, PURPLE, box, 3)
            boxes.append(False)
            coordinates.append((50*row,50*col))
        board.append(boxes)

board = []
coordinates = []


LARGE_TEXT = pygame.font.SysFont('segoeuisemibold', 30)
BTN_TEXT = pygame.font.SysFont('segoeuisemibold', 22) 

reset_button = pygame.Rect(600,100,100,50)
algo_list = DropDown(600,200,190,40, WHITE, (100,200,255), BTN_TEXT, ['Pick an Algorithm','DFS','BFS','A* Search'])
solve_button = pygame.Rect(600,475,100,50)
checkbox = Checkbox(pygame.Rect(600,425,25,25), 'Show steps taken', BTN_TEXT)
play_button = pygame.Rect(w/2-50,350,100,50)

while running:  
    events = pygame.event.get()
    for event in events:

        #QUIT PROGRAM
        if event.type == pygame.QUIT:
            running = False
            break

        #HOME PAGE
        if homepage:
            #TITLE
            title = LARGE_TEXT.render("QUIZ 2 5025211024", 1, GREEN)
            title_rect = title.get_rect()
            title_rect.center = (w/2, 80)
            screen.blit(title, title_rect)

            #INSTRUCTIONS
            instructions = [
                'Click the cells to make walls',
                'Right-click to erase walls',
                'Pick an algorithm to run',
                'Check "Show Steps Taken" to see the algorithm in action!'
                
            ]

            for i, text in enumerate(instructions):
                line = BTN_TEXT.render(text, 1, RED)
                line_rect = line.get_rect()
                line_rect.center = (w/2, 150 + i*40)
                screen.blit(line, line_rect)

            #PLAY BUTTON
            pygame.draw.rect(screen, YELLOW, play_button)
            play = BTN_TEXT.render('PLAY', 1, BLACK)
            screen.blit(play, (play_button.x + 25, play_button.y + 12.5))

            mpos = pygame.mouse.get_pos()

            if play_button.collidepoint(mpos):
                if event.type == MOUSEBUTTONDOWN and event.button == 1:
                    homepage = False
                    screen.fill(BLACK)
                
            
        else:

            #DRAWS THE EMPTY BOARD
            if not board_drawn:
                draw_board()
                board_drawn = True

            #draws the reset button
            pygame.draw.rect(screen, WHITE, reset_button)
            reset_button_text = BTN_TEXT.render("Reset", 1, (BLACK))
            screen.blit(reset_button_text, (reset_button.x + 22.5, reset_button.y + 12.5))

            #draws the solve button
            pygame.draw.rect(screen, LGREEN, solve_button)
            solve_button_text = BTN_TEXT.render('Solve',1,BLACK)
            screen.blit(solve_button_text, (solve_button.x + 22.5, solve_button.y + 12.5))
            
            #DROPDOWN MENU
            selected_option = algo_list.update(events)
            if selected_option > 0:
                algorithm = selected_option
            algo_list.draw(screen)

            #CHECKBOX MENU
            checkbox_filled = checkbox.update(events)
            checkbox.draw(screen)

            #IF MOUSE IS PRESSED
            if event.type == pygame.MOUSEBUTTONDOWN:
                
                cursor_drag = True
                button = event.button
                mousePos = pygame.mouse.get_pos()

                #SOLVE BUTTON
                if solve_button.collidepoint(mousePos):
                    
                    #IF TOO LITTLE WALLS
                    if sum(sum(row) for row in board) < 25 and algorithm in (2,3):
                        rect = pygame.Rect(600,565,100,50)
                        msg = BTN_TEXT.render('Please put more walls!', 1, RED)
                        screen.blit(msg, (rect.x,rect.y))

                    else:
                        #REMOVE MORE WALLS TEXT
                        rect = pygame.Rect(600,565,100,50)
                        msg = BTN_TEXT.render('Please put more walls!', 1, BLACK)
                        screen.blit(msg, (rect.x,rect.y))

                        #EMPTY BOARD 
                        for i in range(len(board)):
                            for j in range(len(board)):
                                if not board[i][j] and (i,j) not in ((0,0),(9,9)):
                                    box = pygame.Rect((j+1)*50, (i+1)*50, 50, 50)
                                    pygame.draw.rect(screen, BLACK, box)
                                    pygame.draw.rect(screen, PURPLE, box, 3)
                                    
                        if algorithm != 0:

                            #REMOVE PICK AN ALGORITHM TEXT
                            rect = pygame.Rect(600,50,100,50)
                            msg = BTN_TEXT.render('Please pick an algorithm', 1, BLACK)
                            screen.blit(msg, (rect.x,rect.y))

                            #MAKE MAZE OBJECT
                            maze = Maze(board, algorithm)
                            pygame.display.set_caption('Loading... DO NOT CLICK ANYTHING WHILE LOAD!')
                            solved = maze.solve(checkbox_filled)

                            #PREVENT USER FROM CLICKING WHILE ALGORITHM IS RUNNING
                            pygame.event.set_blocked(MOUSEBUTTONDOWN)

                            if solved and checkbox_filled:

                                #REMOVE UNSOLVABLE TEXT
                                rect = pygame.Rect(600,535,100,50)
                                msg = BTN_TEXT.render('Unsolvable!', 1, BLACK)
                                screen.blit(msg, (rect.x,rect.y))

                                #SHOW ALGORITHM PATHFINDING
                                pygame.display.set_caption('QUIZ 2 5025211024')
                                steps, solution = solved
                                for step in steps:
                                    if step not in ((0,0),(9,9)):
                                        time.sleep(0.1)
                                        i,j  = step
                                        box = pygame.Rect((j+1)*50, (i+1)*50, 50, 50)
                                        pygame.draw.rect(screen, BLUE, box)
                                        pygame.draw.rect(screen,PURPLE , box, 3)
                                        pygame.display.update()

                            if solved:
                                solution = solution if checkbox_filled else solved
                                #SHOW FINAL SOLUTION
                                for move in solution:
                                    if move not in ((0,0),(9,9)):
                                        time.sleep(0.1)
                                        i,j = move
                                        box = pygame.Rect((j+1)*50, (i+1)*50, 50, 50)
                                        pygame.draw.rect(screen, GREEN, box)
                                        pygame.draw.rect(screen, PURPLE, box, 3)
                                        pygame.display.update()
                            
                            #IF UNSOLVABLE
                            else:
                                rect = pygame.Rect(600,535,100,50)
                                msg = BTN_TEXT.render('Unsolvable!', 1, RED)
                                screen.blit(msg, (rect.x,rect.y))

                            pygame.event.set_allowed(MOUSEBUTTONDOWN)

                        #IF ALGORITHM NOT YET PICKED
                        else:
                            rect = pygame.Rect(600,50,100,50)
                            msg = BTN_TEXT.render('Please pick an algorithm', 1, RED)
                            screen.blit(msg, (rect.x,rect.y))

                #RESET BUTTON
                if reset_button.collidepoint(mousePos):
                    board_drawn = False
                    board = []
                    coordinates = []
                    screen.fill(BLACK)

                #CLICK ON SQUARE(S)   
                else:
                    for x,y in coordinates:
                        if (x,y) not in ((50,50),(500,500)):
                            box = pygame.Rect(x,y,50,50)
                            if box.collidepoint(mousePos):
                                if event.button == 1:
                                    pygame.draw.rect(screen, PINK, box)
                                    pygame.draw.rect(screen, PURPLE, box,3)
                                    board[int(y/50-1)][int(x/50-1)] = True
                                elif event.button == 3:
                                    pygame.draw.rect(screen, BLACK, box)
                                    pygame.draw.rect(screen, PURPLE, box,3)
                                    board[int(y/50-1)][int(x/50-1)] = False                        
                                break
            
            #IF MOUSE IS LIFTED UP
            if event.type == pygame.MOUSEBUTTONUP:
                cursor_drag = False
            
            #IF MOUSE IS DRAGGED AROUND
            if event.type == pygame.MOUSEMOTION:
                if cursor_drag:
                    mousePos_x, mousePos_y = pygame.mouse.get_pos()
                    x, y = round(mousePos_x / 50) * 50, round(mousePos_y / 50) * 50
                    if (x, y) in coordinates and (x,y) not in ((50,50),(500,500)):
                        box = pygame.Rect(x, y, 50,50)    
                        if box.collidepoint(x, y):
                            if button == 1:
                                pygame.draw.rect(screen, PINK, box)
                                pygame.draw.rect(screen, PURPLE, box,3)
                                board[int(y/50-1)][int(x/50-1)] = True
                                pygame.display.update()
                                break
                            elif button == 3:
                                pygame.draw.rect(screen, BLACK, box)
                                pygame.draw.rect(screen, PURPLE, box,3)
                                board[int(y/50-1)][int(x/50-1)] = False
                                pygame.display.update()
                                break   
        pygame.display.update()
```
Main Loop: The code enters a while loop that continues until the running variable becomes False. The loop handles various events, such as quitting the program and mouse interactions.
Home Page: If the homepage variable is True, the code displays the home page. The home page shows a title, instructions, and a "PLAY" button. If the "PLAY" button is clicked, the homepage variable becomes False, and the game board is drawn.
Game Board: The draw_board function is responsible for drawing the initial game board, which consists of a 10x10 grid of cells. The grid cells are represented by pygame.Rect objects. Walls are drawn as purple rectangles, and the start and end points are drawn as red rectangles.
Buttons and Dropdown Menu: The code creates several buttons and a dropdown menu using pygame.Rect objects. Buttons include the "Reset" button, "Solve" button, and a checkbox for "Show steps taken". The dropdown menu allows the user to pick an algorithm for solving the maze.
Mouse Interactions: The code handles mouse events, such as clicking, dragging, and releasing the mouse button. When a cell is clicked, it can be filled with a wall (left-click) or erased (right-click). The "Reset" button clears the game board. The "Solve" button triggers the maze-solving algorithm based on the chosen algorithm from the dropdown menu. The checkbox controls whether the steps taken during the algorithm execution are displayed.
Maze Solving: If the "Solve" button is clicked and an algorithm is chosen, the maze-solving process begins. The Maze class is responsible for solving the maze using the selected algorithm. The maze-solving process can show the steps taken and the final solution by marking cells with different colors. If the maze is unsolvable, an error message is displayed. 
Display Update: After handling events and making changes to the game window, the code updates the display to reflect the changes.

## ANALYSIS
Output :
   
Click your left mouse to create a wall and drag it to make walls. Right mouse for deleting the walls
![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/e0676cfd-c828-4f30-898e-3d58c332ba1a)

![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/014946be-ee06-4beb-bbfc-241eb9a2be17)

Click your left mouse to create a wall and drag it to make walls. Right mouse for deleting the walls
![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/afcb0c33-c478-4abe-b16c-dd8010de9dd7)

![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/94c836d2-bfbd-4093-9ed5-6f50c9c85a08)

Choose the algorithm that u want to test before solving the maze. You can either show the steps that’s been taken or not by clicking on “show steps taken”. You can also reset the walls and the algorithm if you feel like it. 
![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/e70de5c1-4168-4d2e-8135-3215184006eb)

![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/1c5cce1d-3662-429f-a784-ed604775c3e6)

CONDITIONS:
IF there is no way to solve the maze, program will tell you that it’s unsolvable. If did not put enough wall, program will tell you to put more walls 

![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/643714fc-4c05-4b31-9f6d-18a8c4590b8f)

![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/1f7aaa96-e1ab-4642-a63b-8526818a25f5)

TESTING:

A* search:
Program will count all the possible path first(blue) then will execute the shortest one (green)
![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/85b5ff26-20a7-4041-9c60-a02c9f62330d)

![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/066913c3-3fff-4858-9e74-ab5fa83a07d3)

Execute time: 00.07,00 (With steps) 00.01,90 (Without) 

DFS:
Program will count all the possible path first(blue) then will execute the shortest one (green)
![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/b03761d6-9064-4918-8ee2-040f7afe3c75)

![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/b0e56da2-d22a-45a0-b99c-de4d641431d3)

Execute time: 00.04,78 (With steps) 00.02,30 (Without)

BFS:
Program will count all the possible path first(blue) then will execute the shortest one (green)
![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/c5825d20-b2cf-494d-bafb-3ee860aa2752)

![image](https://github.com/y4mchi/IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani/assets/100952724/c7a9b088-7376-445b-b4eb-4abc6cade0e3)

Execute time: 00.10,47 (With steps) 00.01,70 (Without)

## Conclusion 
I’m using DFS, BFS, and A*search to run the program. First, place the walls to create a maze. After setting the walls, select the algorithm to use. Then the program will start scanning for the shortest paths based on the selected algorithm and highlight the shortest paths it can see. I have a few classes I created to create mazes. B. Dropdown, checkbox, home, play button, solution button, mouse trigger. So a left click builds a wall and a right click clears a wall. To avoid accidental clicks while editing the maze, we blocked the mouse button while editing. Based on the output of these three algorithms, BFS was the fastest, but it took much longer than the other two algorithms to scan or compute all paths. 

## Job Description and Distribution
Widian Sasi Disertasiani (DAA H) (5025211024) (100%) = Design, Implementation, Making report and Descriping the program. 




  





