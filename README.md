# IF184401_DAA_Q2_5025211024_Widian-Sasi-Disertasiani
I made a program called maze solver for this QUIZ 2 assigment. I’m using DFS, BFS, and A*search to execute the program. Before I elaborate the program, I will explain DFS, BFS and A*search first. DFS is an algorithm used for traversing or searching tree or graph data structures. DFS explores a graph by visiting vertices (or nodes) and following their adjacent edges until it reaches a dead end, and then backtracking to visit other unexplored vertices. BFS is an algorithm used for traversing or searching tree or graph data structures. Unlike Depth-First Search (DFS) that explores a graph by going as deep as possible before backtracking, BFS explores the graph in a breadthward motion, visiting all vertices at the same level before moving to the next level. A*search is an informed search algorithm used for finding the shortest path between a starting vertex and a goal vertex in a graph or a weighted grid. It is widely used in pathfinding and graph traversal problems.
How my program works? First I put walls to make maze, after I put walls I choose which Algorithm I’d like to use, then my program will start to scanning the shortest path based on the algorithm that I’ve choose, then it will marked up the shortest path for us to see. 
There are few classes that I made to create my maze, such as Dropdown, checkbox, home page, play button, solve button, mouse trigger, so if I clicked on my left mouse, it will put walls, if I clicked on my right mouse, it will delete the walls. To prevent missclick while processing the maze, I blocked the mouse button while it processing. 
