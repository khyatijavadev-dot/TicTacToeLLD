Requirements : -


Size of the board should be scalable.
There are standard game rules and should be further extendible.
Allow on allow notification for tic tac toe moves, wins, draws etc.


Tic-Tac-Toe — Low-Level Design
1. Problem Overview
Tic-Tac-Toe is a two-player game played on a square grid.
Players take turns placing their symbols on empty cells. A player wins when all cells in a complete row, column, or diagonal contain that player's symbol.
If all cells are filled and neither player has achieved a winning condition, the game ends in a draw.
The implementation supports:
Configurable board size.
Two or more players.
Custom player symbols.
Alternating turns.
Move validation.
Winner detection.
Draw detection.
Console-based gameplay.
Observer-based notifications.
Factory-based game creation.
Extensible game rules.

2. Requirement Clarification
Before implementing the design, the following assumptions are made:
The board is square and its size is configurable.
A standard game requires at least two players.
Each player has a unique symbol.
Players take turns sequentially.
A move consists of a row and column.
A move is valid only when the selected cell is inside the board and empty.
The game ends immediately when a player wins or the board is full.
The standard winning condition is a complete row, column, or diagonal.
The game is played through the console.
Notifications can be sent to registered observers.
Player scores are maintained and incremented when a player wins.

3. Functional Requirements
3.1 Board Management
Create an N × N board.
Initialize every cell as empty.
Display the current board.
Check whether a cell is empty.
Place a player's symbol in an empty cell.
3.2 Player Management
Create players with:
Player ID
Name
Symbol
Score
Add players to the game.
Maintain player turn order.
3.3 Gameplay
Start the game.
Display the current board.
Ask the current player for a row and column.
Validate the move.
Place the player's symbol.
Check for a winner.
Check for a draw.
Switch to the next player if the game continues.
3.4 Notifications
The game can notify registered observers when important events occur, such as:
Game started.
A player makes a move.
A player wins.
Game ends in a draw.
3.5 Game Creation
A factory is used to create the appropriate game implementation based on GameType.

4. Non-Functional Requirements
Object-Oriented Design
The implementation separates responsibilities between Board, Player, Game, Rules, Observer, and Factory components.
Modularity
Game rules are separated from the game orchestration through the TicTacToeRules interface.
Extensibility
The design can be extended with:
Different rule sets.
AI players.
Different notification mechanisms.
Different game types.
Scoreboards.
Larger boards.
Maintainability
Each class has a relatively focused responsibility, making individual components easier to modify and test.
User Experience
The board is displayed after every turn and important events are printed to the console.

5. Core Classes
The implementation contains the following major classes and interfaces:


Component
Responsibility
Symbol
Represents a player's symbol
Board
Maintains and manipulates the game grid
TicTacToePlayer
Stores player information and score
TicTacToeRules
Defines game-rule operations
StandardTicTacToeRules
Implements standard Tic-Tac-Toe rules
IObserver
Defines notification behavior
ConsoleNotifier
Displays notifications on the console
TicTacToeGame
Coordinates the complete game
GameType
Represents supported game types
TicTacToeGameFactory
Creates game instances
TicTacToeMain
Driver class that starts the application


6. Symbol Class
Responsibility
Symbol represents the marker placed inside a board cell.
Unlike the original design that uses an enum, the current implementation uses a class so that symbols can be customized.
For example:
new Symbol('X')
new Symbol('O')


Attributes
private char mark;


Methods
Symbol(char m)
char getMark()


Design Consideration
Using a class allows the game to support arbitrary symbols instead of limiting players to predefined values.
For example:
X
O
A
B
1
2


could all potentially be represented as symbols.

7. Board Class
Responsibility
Board owns and manages the game grid.
The Board does not know about players, turns, winners, or game lifecycle.
Its responsibility is limited to maintaining board state.
Attributes
private Symbol[][] grid;
private int size;
private Symbol emptyCell;


grid
Stores the symbols currently present on the board.
size
Represents the board dimension.
For example:
size = 3


creates:
3 × 3


emptyCell
Represents an unoccupied cell.
The implementation initializes it as:
new Symbol('-')



8. Board Constructor
public Board(int s)


The constructor:
Stores the board size.
Creates the empty-cell symbol.
Creates the Symbol[][] grid.
Initializes every position with the empty symbol.
For a 3×3 board:
- - -
- - -
- - -



9. Board Methods
isCellEmpty()
public boolean isCellEmpty(int row, int col)


Checks whether the requested position is empty.
It first validates the row and column.
An invalid position returns:
false


Otherwise, it checks whether the cell contains the empty symbol.

placeMark()
public boolean placeMark(int row, int col, Symbol mark)


Places a symbol on the board.
The method performs two validations:
Position must be inside the board.
Cell must currently be empty.
If either condition fails, the method returns false.
Otherwise:
grid[row][col] = mark;


and returns true.

getCell()
public Symbol getCell(int row, int col)


Returns the symbol stored at a particular position.
If the requested position is outside the board, the method returns emptyCell.

getSize()
Returns the board dimension.
public int getSize()



getEmptyCell()
Returns the symbol representing an empty position.
public Symbol getEmptyCell()



display()
Displays the board in the console.
Example:
 0 1 2
0 X O -
1 - X -
2 O - X


The row and column numbers make it easier for the player to enter coordinates.

10. TicTacToePlayer Class
Responsibility
TicTacToePlayer represents a player participating in the game.
Attributes
private int playerId;
private String name;
private Symbol symbol;
private int score;


playerId
Uniquely identifies the player.
name
Stores the player's display name.
symbol
Stores the symbol used by the player.
score
Stores the number of games won by the player.

11. Player Constructor
public TicTacToePlayer(int playerId, String n, Symbol s)


The constructor initializes:
playerId
name
symbol
score = 0


A newly created player therefore starts with zero wins.

12. Player Methods
getName()
Returns the player's name.
getSymbol()
Returns the player's symbol.
getScore()
Returns the current score.
incrementScore()
Increases the player's score after winning a game.
score++;



13. TicTacToeRules Interface
The rules are separated from the game using an interface.
interface TicTacToeRules


This abstraction allows different rule implementations to be plugged into the game.
Methods
boolean isValidMove(Board board, int row, int col);


boolean checkWinCondition(Board board, Symbol symbol);


boolean checkDrawCondition(Board board);



14. StandardTicTacToeRules
StandardTicTacToeRules implements:
TicTacToeRules


It contains the standard Tic-Tac-Toe rules.
This class is responsible for:
Move validation.
Winner detection.
Draw detection.
The TicTacToeGame does not directly implement these rules.

15. Move Validation
The implementation delegates move validation to:
rules.isValidMove(board, row, col)


The standard implementation uses:
board.isCellEmpty(row, col)


Therefore a move is valid when:
row is inside the board
AND
column is inside the board
AND
cell is empty


If the move is invalid, the player is asked to try again.

16. Win Detection
The method:
checkWinCondition(Board board, Symbol symbol)


checks whether the specified symbol has completed a winning line.
The implementation checks:
Rows
Every row is examined.
Example:
X X X
- O -
O - -


The first row produces a win for X.
Columns
Every column is examined.
Example:
X O -
X O -
X - -


The first column produces a win for X.
Main Diagonal
The following diagonal is checked:
X - -
- X -
- - X


Anti-Diagonal
The following diagonal is checked:
- - X
- X -
X - -


If any complete line contains the requested symbol, the method returns:
true


Otherwise:
false



17. Draw Detection
The method:
checkDrawCondition(Board board)


iterates through every cell.
If at least one cell contains the empty symbol, the game is not a draw.
If there are no empty cells, the method returns:
true


The game checks for a win before checking for a draw, so a winning move on the final available cell is correctly treated as a win rather than a draw.

18. Observer Pattern
The code uses the Observer pattern to decouple notifications from the core game logic.
Observer Interface
interface IObserver {
    void update(String msg);
}


Any class that wants to receive game notifications implements this interface.

19. ConsoleNotifier
ConsoleNotifier is a concrete observer.
class ConsoleNotifier implements IObserver


Its implementation is:
public void update(String msg) {
    System.out.println("[Notification] " + msg);
}


Therefore, whenever the game sends a notification, it is printed to the console.

20. Observer Relationship
The relationship is:
TicTacToeGame
      |
      | maintains
      v
 List<IObserver>
      |
      +----> ConsoleNotifier
      +----> Future Observer
      +----> Future Observer


This makes it possible to add additional notification mechanisms without modifying the game logic.
Possible future implementations include:
EmailNotifier
FileLogger
MobileNotification
GameAnalyticsObserver



21. TicTacToeGame
TicTacToeGame is the main orchestrator of the application.
It coordinates:
Board
Players
Rules
Observers
Game lifecycle
Turn management
Score updates
Attributes
private Board board;
private Deque<TicTacToePlayer> players;
private TicTacToeRules rules;
private List<IObserver> observers;
private boolean gameOver;



22. Board Dependency
The game contains:
private Board board;


The Board is responsible for maintaining the physical game state.
The Game does not directly manipulate the grid.
Instead, it uses Board methods such as:
board.placeMark(...)
board.display()
board.getCell(...)


This provides encapsulation.

23. Player Turn Management
The implementation uses:
Deque<TicTacToePlayer> players;


The player at the front of the deque is the current player.
TicTacToePlayer currentPlayer = players.peekFirst();


After a successful non-terminal move:
players.removeFirst();
players.addLast(currentPlayer);


Therefore the current player moves to the back of the queue.
Example:
Initial:


[Player1, Player2]


Player1 plays


[Player2, Player1]


Player2 plays


[Player1, Player2]


This provides a simple mechanism for alternating turns.

24. Adding Players
Players are added using:
public void addPlayer(TicTacToePlayer player)


The player is inserted at the end of the deque:
players.addLast(player);


The first player added becomes the first player to play.

25. Adding Observers
Observers are registered through:
public void addObserver(IObserver observer)


The observer is stored in:
List<IObserver> observers



26. Notification
The Game contains:
public void notify(String msg)


It iterates through all registered observers:
for(IObserver observer : observers) {
    observer.update(msg);
}


Every observer therefore receives the same game event.

27. Game Flow
The main game loop is implemented inside:
public void play()


The high-level flow is:
Start
  |
  v
Check minimum players
  |
  v
Notify game started
  |
  v
Display board
  |
  v
Get current player
  |
  v
Read row and column
  |
  v
Validate move
  |
  +---- Invalid ---> Display error
  |                    |
  |                    +---- Retry
  |
  v
Place symbol
  |
  v
Notify move
  |
  v
Check winner
  |
  +---- Winner ---> Display board
  |                 Increment score
  |                 Notify
  |                 End game
  |
  v
Check draw
  |
  +---- Draw ----> Display board
  |                Notify
  |                End game
  |
  v
Move current player to back of queue
  |
  v
Next turn



28. Detailed play() Flow
Step 1 — Validate Players
The game requires at least two players.
if(players.size() < 2) {
    System.out.println("Need at least 2 players!");
    return;
}



Step 2 — Start Notification
The game sends:
Tic Tac Toe Game Started!


to all observers.

Step 3 — Display Board
Before every turn:
board.display();


is called.

Step 4 — Get Current Player
The current player is obtained from the front of the deque:
TicTacToePlayer currentPlayer = players.peekFirst();



Step 5 — Read Move
The console asks the player for:
row
column


For example:
0 2


means row 0, column 2.

Step 6 — Validate Move
The rules object checks:
rules.isValidMove(board, row, col)


If the move is invalid:
Invalid move! Try again.


is displayed.
The current player remains the same.

Step 7 — Place Symbol
For a valid move:
board.placeMark(row, col, currentPlayer.getSymbol());


is executed.

Step 8 — Notify Move
The game sends an event such as:
Aditya played (0,2)


to observers.

Step 9 — Check Winner
The game calls:
rules.checkWinCondition(
    board,
    currentPlayer.getSymbol()
)


If the result is true:
Board is displayed.
Winner is printed.
Player's score is incremented.
Winner notification is sent.
gameOver becomes true.

Step 10 — Check Draw
If nobody won, the game checks:
rules.checkDrawCondition(board)


If the board is full:
It's a draw!


is displayed.
The game is then terminated.

Step 11 — Switch Player
If neither a win nor a draw occurred:
players.removeFirst();
players.addLast(currentPlayer);


The next player becomes the current player.

29. Game Lifecycle
The current implementation uses:
private boolean gameOver;


Initially:
gameOver = false


The game continues while:
while(!gameOver)


When a player wins:
gameOver = true;


When the board is full:
gameOver = true;


Therefore the game loop terminates in either of the two terminal conditions.

30. GameType Enum
The implementation defines:
enum GameType {
    STANDARD
}


Currently only the standard game type is supported.
The enum provides a place to add additional game variants later.
For example:
STANDARD
CUSTOM
AI
TOURNAMENT


could be added in the future.

31. Factory Pattern
The Factory pattern is implemented using:
TicTacToeGameFactory


Its method is:
public static TicTacToeGame createGame(
    GameType gt,
    int boardSize
)


For the current implementation:
if(GameType.STANDARD == gt) {
    return new TicTacToeGame(boardSize);
}


The client therefore does not need to directly construct the game implementation.

32. Factory Relationship
TicTacToeMain
      |
      | requests
      v
TicTacToeGameFactory
      |
      | creates
      v
TicTacToeGame


The factory becomes more useful when multiple game implementations are introduced.

33. Main / Driver Class
TicTacToeMain is the driver class.
It is responsible for application startup and user interaction setup.
The flow is:
Start application
      |
      v
Read board size
      |
      v
Create game through Factory
      |
      v
Create ConsoleNotifier
      |
      v
Register observer
      |
      v
Create Player 1
      |
      v
Create Player 2
      |
      v
Add players
      |
      v
Start game.play()



34. Example Initialization
The main class creates:
TicTacToePlayer player1 =
    new TicTacToePlayer(
        1,
        "Aditya",
        new Symbol('X')
    );


TicTacToePlayer player2 =
    new TicTacToePlayer(
        2,
        "Harshita",
        new Symbol('O')
    );


The resulting configuration is:
Player 1
Name   : Aditya
Symbol : X
Score  : 0


Player 2
Name   : Harshita
Symbol : O
Score  : 0



35. Class Relationship Diagram
Conceptually, the relationships are:
                   +----------------------+
                    |   TicTacToeMain      |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    | TicTacToeGameFactory |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |    TicTacToeGame     |
                    +----+----+----+-------+
                         |    |    |
              contains  |    |    | uses
                         |    |    |
                         v    v    v
                      Board Players Rules
                              
                    +----------------------+
                    |  TicTacToeRules      |
                    +----------^-----------+
                               |
                               | implements
                               |
                    +----------------------+
                    |StandardTicTacToeRules|
                    +----------------------+


TicTacToeGame
      |
      | notifies
      v
 List<IObserver>
      |
      v
ConsoleNotifier



36. Responsibility Distribution
Class
Responsibility
Does Not Handle
Symbol
Represents a symbol
Game rules
Board
Stores/manages grid
Players/turns
TicTacToePlayer
Stores player information
Board operations
TicTacToeRules
Defines game rules
Player management
StandardTicTacToeRules
Implements standard rules
Game lifecycle
TicTacToeGame
Orchestrates gameplay
Low-level grid management
IObserver
Notification contract
Game state
ConsoleNotifier
Console notifications
Game logic
TicTacToeGameFactory
Creates games
Gameplay
TicTacToeMain
Starts application
Game rules


37. OOP Concepts Used
Encapsulation
Board state is private:
private Symbol[][] grid;


Clients interact with it through methods instead of directly modifying the grid.

Abstraction
The rules are represented through:
interface TicTacToeRules


The Game depends on the interface rather than directly implementing the rules.

Polymorphism
The following assignment demonstrates polymorphism:
TicTacToeRules rules =
    new StandardTicTacToeRules();


The Game works with the TicTacToeRules abstraction.
Another rules implementation could be substituted later.

Composition
TicTacToeGame contains:
Board
Deque<TicTacToePlayer>
TicTacToeRules
List<IObserver>


The Game therefore coordinates several independent components.

38. Design Patterns Used
38.1 Strategy Pattern
The rules interface provides a Strategy-style design:
interface TicTacToeRules


with:
StandardTicTacToeRules


as an implementation.
This allows the rules implementation to be replaced without changing the core Game orchestration.

38.2 Observer Pattern
The Observer pattern is used for notifications.
TicTacToeGame
      |
      v
IObserver
      |
      +---- ConsoleNotifier
      +---- Future observers


The Game does not need to know how each observer handles a message.

38.3 Factory Pattern
The Factory pattern is used by:
TicTacToeGameFactory


to create game instances based on:
GameType



39. Variable Board Size
The implementation does not hardcode a 3×3 board.
The user enters:
Enter board size (e.g., 3 for 3x3):


The value is passed to:
TicTacToeGameFactory.createGame(
    GameType.STANDARD,
    boardSize
);


and eventually:
new Board(boardSize)


Therefore:
3 → 3×3
4 → 4×4
5 → 5×5


can be created.
The current winning rule considers a complete row, column, or diagonal across the configured board.

40. Example Game
For a 3×3 board:
 0 1 2
0 X O -
1 - X -
2 O - -


Player X plays:
2 2


The resulting board becomes:
 0 1 2
0 X O -
1 - X -
2 O - X


The main diagonal contains:
X X X


Therefore X wins.
The player score is incremented and the game terminates.

41. Invalid Move Handling
The current implementation handles invalid moves using a boolean result.
For example:
if(rules.isValidMove(board, row, col)) {
    ...
}
else {
    System.out.println("Invalid move! Try again.");
}


Invalid moves include:
Negative row.
Negative column.
Row outside board.
Column outside board.
Selecting an occupied cell.
The current implementation does not define a custom InvalidMoveException.
This differs from a design where invalid moves would throw:
InvalidMoveException



42. Thread Safety
The current code is a console application and does not synchronize play() or placeMark().
If the same TicTacToeGame instance were exposed to multiple concurrent requests in a web application, additional synchronization would be required.
For example, a future implementation could make the complete move operation atomic:
public synchronized void makeMove(int row, int col) {
    // validate
    // place symbol
    // check winner/draw
    // switch player
}


This is not currently part of the provided implementation.

43. Current Design vs. Earlier Proposed Design
There are several differences between the original conceptual design and the supplied code.
Earlier Design
Current Code
Symbol enum
Symbol class
EMPTY enum value
Symbol('-')
Cell class
No separate Cell class
Player class
TicTacToePlayer
GameStatus enum
boolean gameOver
InvalidMoveException
Boolean validation
currentPlayerIndex
Deque<TicTacToePlayer>
makeMove()
Gameplay inside play()
Last-move win checking
Full board scan for winning lines
No explicit Observer implementation
IObserver + ConsoleNotifier
No explicit Strategy interface
TicTacToeRules
Factory
TicTacToeGameFactory

Therefore, the documentation should follow the actual code rather than describing classes that are not present.

44. Possible Future Extensions
AI Player
A future AI component can choose moves without changing Board responsibilities.
For example:
interface MoveStrategy {
    int[] chooseMove(Board board, Symbol symbol);
}


Possible implementations:
RandomMoveStrategy
MinimaxMoveStrategy



Additional Rule Implementations
Because rules are abstracted through:
TicTacToeRules


different rule implementations can be added.
For example:
StandardTicTacToeRules
CustomTicTacToeRules
TournamentTicTacToeRules



Persistent Scoreboard
Currently, each player stores their score:
private int score;


For multiple matches, a separate:
Scoreboard


could maintain aggregate statistics.

Better Exception Handling
The current boolean-based validation can be replaced with a domain exception such as:
class InvalidMoveException extends RuntimeException {
    public InvalidMoveException(String message) {
        super(message);
    }
}


This can make invalid operations more explicit.

Thread-Safe Game API
For a web-based implementation, gameplay could be exposed through a synchronized service:
public synchronized void makeMove(int row, int col)


This would ensure that validation, placement, result checking, and turn switching happen atomically.

45. Testing Considerations
The design allows individual components to be tested separately.
Board Tests
Test:
Empty board creation.
Valid placement.
Invalid row.
Invalid column.
Occupied cell.
Board size.
Rules Tests
Test:
Horizontal win.
Vertical win.
Main diagonal win.
Anti-diagonal win.
No winner.
Draw.
Partially filled board.
Player Tests
Test:
Name.
Symbol.
Initial score.
Score increment.
Game Tests
Test:
At least two players.
Turn switching.
Invalid move does not change turn.
Winner ends game.
Draw ends game.
Winner's score increases.
Observer Tests
Test that registered observers receive:
Game Started
Player Move
Winner
Draw


notifications.

46. Overall Execution Flow
                +----------------+
                 | TicTacToeMain  |
                 +-------+--------+
                         |
                         v
              +---------------------+
              | Game Factory        |
              +----------+----------+
                         |
                         v
              +---------------------+
              |   TicTacToeGame     |
              +----------+----------+
                         |
        +----------------+----------------+
        |                |                |
        v                v                v
     Board            Players           Rules
        |                                 |
        |                                 v
        |                      StandardTicTacToeRules
        |
        v
   Grid State


                         |
                         v
                    Observers
                         |
                         v
                  ConsoleNotifier



47. Summary
The provided implementation follows a modular object-oriented design.
The main responsibilities are separated as follows:
Board manages the grid.
TicTacToePlayer represents players and their scores.
TicTacToeRules defines the game rules.
StandardTicTacToeRules implements standard winning and draw conditions.
TicTacToeGame orchestrates gameplay and manages turns.
IObserver provides notification abstraction.
ConsoleNotifier provides console notifications.
TicTacToeGameFactory creates game instances.
TicTacToeMain acts as the driver/application entry point.
The implementation demonstrates Encapsulation, Abstraction, Polymorphism, Composition, Strategy-style rule separation, Observer Pattern, and Factory Pattern.
The design is also prepared for future extensions such as AI opponents, alternative rule sets, persistent scoreboards, additional observers, and web-based concurrent gameplay.
