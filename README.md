# ComS-228-8-Puzzle

# 1. Problem Description
The task in this project is to solve the 8-puzzle. Our objective here is to rearrange a given initial
configuration of eight numbered tiles residing on a 3 x 3 board into a given final configuration
called the goal state. An example puzzle is given below:
 Initial State Goal State
 2 3 1 2 3
 1 8 4 8 4
 7 6 5 7 6 5
The rearrangement is allowed to proceed only by sliding one of the tiles onto the empty square
from an orthogonally adjacent position. There are four possible moves LEFT, RIGHT, UP, and
DOWN, which apply respectively to the four neighboring tiles, if exist, to the right of, to the left of,
below, and above the empty square. Below describes a sequence of three moves that solves
the above example.
 2 3 RIGHT 2 3 UP 1 2 3 LEFT 1 2 3
 1 8 4 -----> 1 8 4 -----> 8 4 -----> 8 4
 7 6 5 7 6 5 7 6 5 7 6 5
The solution path has length 3.
In this project, the goal state will always have the empty square in the middle and the eight tiles
surrounding it with their numbers increasing clockwise from 1 at the upper left corner. In other
words, the goal state is as follows:
 1 2 3
 8 4
 7 6 5
 
# 2. Heuristics
How to solve the 8-puzzle? Examining the same example above, we notice that the initial state
has three successor states, that is, states that can be generated from the initial state with one
move.
 2 3
 1 8 4
7 6 5
 |
 _________________|_________________
 | | |
 | | |

 2 3 2 8 3 2 3
 1 8 4 1 4 1 8 4
 7 6 5 7 6 5 7 6 5

 (𝐴) (𝐵) (𝐶)
Which of the three alternatives 𝐴, 𝐵, or 𝐶 appears most promising? In other words, which will
likely lead to a rearrangement consisting of the smallest number of moves? An exhaustive
search of subsequent moves in the puzzle can find out which of the three results in the shortest
path to the goal state. However, such a search is utterly impractical as the number of states is
destined to "explode" combinatorially in those puzzles where the path length from the initial
state to the goal state is large. So we have to rely on some heuristics, or rules of thumb,
instead to judge which successor is most promising. In Artificial Intelligence (AI), the use of
heuristics in problem solving is a common practice.
One of the judgments is estimating how close a state is to the goal. In the 8-puzzle, there are
two very common heuristics that are used for estimating the proximity of one state to another.
The first is the number of mismatched tiles, those by which the two states differ. Call this
heuristic function ℎ1. The second is the sum of the (horizontal and vertical) distances of the
mismatched tiles between the two states, which we will call heuristic function ℎ2. The
function ℎ2 is also known as the Manhattan distance. Below are the estimates for the states 𝐴,
𝐵, and 𝐶 from the goal state in the previous example:
 ℎ1(𝐴) = 2 ℎ1(𝐵) = 3 ℎ1(𝐶) = 4
 ℎ2(𝐴) = 2 ℎ2(𝐵) = 4 ℎ2(𝐶) = 4
Both ℎ1 and ℎ2 give estimates that are no greater than the minimum number of moves
required to reach the goal from the current state. The function ℎ1 says that at least one move is
needed to correct each mismatched tile. For instance, tile 2 in the initial state does not match
tile 2 in the goal state; so at least one move is needed. Function ℎ2 is based on the fact that the
number of moves involved in correcting a mismatch is no less than the Manhattan distance
between the positions of the same tile in the two states. For instance, it requires at least two
moves to return tile 8 in state B to its position in the goal state.
Heuristics such as ℎ1 and ℎ2 that do not overestimate the minimum solution length for the
current state are called admissible. Heuristic ℎ2 seems to be better than ℎ1 because it gives a
closer estimate of the minimum number of moves to solve the 8-puzzle.
To derive a heuristic that estimates the number of moves taking the initial state to the goal state,
we need to include the cost from the initial state to the current state as well. So the cost for an
intermediate state 𝑠 is a function
𝑓(𝑠) = 𝑔(𝑠) + ℎ(𝑠)
where 𝑔(𝑠) is the number of moves from the initial state to 𝑠, and ℎ(𝑠) is a heuristic function
(like ℎ1 and ℎ2) that estimates the number of moves from 𝑆 to the goal state. The value of 𝑔 at
the initial state and the value of ℎ at the goal state are both 0.

# 3. The A* Algorithm
To solve the 8-puzzle, you are required to use the so-called A* algorithm, from which all
involved search algorithms (including the one that the IBM Deep Blue used to beat World Chess
Champion Garry Kasparov in 1997) have more or less evolved. The A* algorithm maintains two
lists, OPEN and CLOSED, of 8-puzzle states. The states in the CLOSED list have been expanded;
that is, they have been examined and their successor states have been generated. The states
in the OPEN list have yet to be expanded.
The A* algorithm has the following six steps:
1. Put the start state 𝑠0 on OPEN. Let 𝑔(𝑠0) = 0 and estimate ℎ(𝑠0).
2. If OPEN is empty, exit with failure. This will not happen with an 8-puzzle if before calling A*
you use the method from Appendix to check that the puzzle is indeed solvable. (The
situation may occur when applying A* to solve a different type of problem.)
3. The states on the list OPEN will be ordered in the non-decreasing value of 𝑓. Remove the
first state 𝑠 from OPEN and place on the list CLOSED.
4. If 𝑠 is the goal state, exit successfully and print out the entire solution path (step-by-step
state transitions).
5. Otherwise, generate 𝑠's all possible successor states in one valid move and set their
predecessor links back to 𝑠. For every successor state 𝑡 of 𝑠:
a. Estimate ℎ(𝑡) and compute 𝑓(𝑡) = 𝑔(𝑡) + ℎ(𝑡) = 𝑔(𝑠) + 1 + ℎ(𝑡).
b. If 𝑡 is not already on OPEN or CLOSED, then put it on OPEN.
c. If 𝑡 is already on OPEN, compare its old and new 𝑓 values and choose the
minimum. In the case that the new 𝑓 value is chosen, reset the predecessor link
of 𝑡 (along the path yielding the lowest 𝑔(𝑡)).
d. If 𝑡 is on CLOSED and its new 𝑓 value is less than the old one. The state may
become promising again because of this value decrease. Put 𝑡 back on OPEN and
reset its predecessor link.
6. Go to step 2. 

# 4. Implementation
The names of the valid moves and available heuristics are given in two enum types Move and
Heuristic, respectively.
The class State implements the configuration (or state) of the board. It has two constructors
for generating the initial state only:
 public State(int[][] board) throws IllegalArgumentException
 public State (String inputFileName) throws FileNotFoundException,
 IllegalArgumentException
All other states are generated from existing states via one move using the following method:
public State successorState(Move m) throws IllegalArgumentException
The class is ready to represent a node in a circular doubly-linked list, as it has both next and
previous links. It also has a predecessor link for tracing all the moves back to the initial state if
this state is the final state.
The method isGoal() checks if this state is the goal state. The method solvable() determines
whether moves exist to transform this state to the goal state, applying the method described in
the Appendix.
In the State class, the value of the function 𝑔 (defined in Section 2) is stored in the public
instance variable numMoves. The method
 public int cost() throws IllegalArgumentException
evaluates 𝑔 + ℎ1 or 𝑔 + ℎ2 (ℎ1 and ℎ2 are also defined Section 2) depending on the heuristic
used. The functions ℎ1 and ℎ2 are evaluated respectively by the methods
computeNumMismatchedTiles() and computeManhattanDistance(). The chosen heuristics is
stored in the static variable heu.
The State class implements the compareTo() method, which compares the total cost (𝑔 + ℎ1 or
𝑔 + ℎ2) of this state with the argument state based on the heuristic heu used. Two states are
also compared using the compare() method of the StateComparator class. This second
comparison uses the lexicographic order of the tile number sequence on the board.
The OrderedStateList class represents a circular doubly-linked list with a dummy head
node. It can be used to represent either the OPEN list or the CLOSE list, depending on the value of
the boolean instance variable isOpen. In the OPEN list, the nodes are ordered using the
compareTo() method of the State class; and in the CLOSE list, they are ordered using the
compare() method of the StateComparator class. Ordering of nodes employs the
compareStates() method, which chooses one of the above two options for comparison based
on isOpen. The constructor of OrderedStateList accepts a provided heuristic to initialize the
static variable heu for the State class (shared by all objects of State) so its compareTo() method
will perform accordingly. 
The class EightPuzzle has two static methods solve8Puzzle() and AStar(). The first method
accepts an initial board configuration, checks if the 8-puzzle has a solution (see Appendix), and
if so, calls the second method twice, each time with a different heuristic, to solve the puzzle.
The method AStar implements the A* algorithm as described in Section 3.
While you are encouraged to use helper functions wherever needed, please do not change the
signature or access modifiers for already defined methods and variables in the template
provided.

# 5. Input and Output
The initial board configuration is input from a file in the following format. The file has three rows,
each containing three digits with exactly one blank in between. Every row starts with a digit.
The nine digits are from 0 to 8 with no duplicates.
The method AStar() returns a string that represents the solution to an 8-puzzle. It starts with
the total number of moves (along with the used heuristic), and continues with a sequence of
states which begins with the initial state and ends with the goal state. Every intermediate state
(the final state included) in the sequence must result from a single move from its preceding
state. The sequence should be printed vertically with every two adjacent states separated by
the move causing the state transition.
The method Solve8Puzzle() calls AStar() twice, and concatenate the two solution strings into
one. Below is the printout of the string returned from a sample call to the method
Solve8Puzzle(). (Your code may generate different solutions with the same minimum number
of moves.)
10 moves in total (heuristic: the Manhattan distance)
1 2 3
6 5 7
8 4
DOWN
1 2 3
6 5
8 4 7
RIGHT
1 2 3
6 5
8 4 7
UP
1 2 3
6 4 5
8 7
LEFT
1 2 3
6 4 5
8 7
DOWN
1 2 3
6 4
8 7 5
RIGHT
1 2 3
6 4
8 7 5
RIGHT
1 2 3
6 4
8 7 5
UP
1 2 3
8 6 4
7 5
LEFT
1 2 3
8 6 4
7 5
DOWN
1 2 3
8 4
7 6 5
10 moves in total (heuristic: number of mismatached tiles)
1 2 3
6 5 7
8 4
RIGHT
1 2 3
6 5 7
8 4
DOWN
1 2 3
6 7
8 5 4
LEFT
1 2 3
6 7
8 5 4
UP
1 2 3
6 7 4
8 5
RIGHT
1 2 3
6 7 4
8 5
DOWN
1 2 3
6 4
8 7 5
RIGHT
1 2 3
6 4
8 7 5
UP
1 2 3
8 6 4
7 5
LEFT
1 2 3
8 6 4
7 5
DOWN
1 2 3
8 4
7 6 5
In case the 8-puzzle has no solution, then the string should print out this information as well as
the initial state.
No solution exists for the following initial state:
4 1 2
5 3
8 6 7
