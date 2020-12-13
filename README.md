# Nonogram Game Session Analysis

![](illustration.jpg)

A [nonogram](https://en.wikipedia.org/wiki/Nonogram) is a puzzle game in which cells in a grid must be colored or left blank according to numbers at the side of the grid to reveal a hidden picture.

A nonogram is solved when the player has filled the right cells, based on the given clues, and has left the others empty.

We need to score a player who has solved a nonogram depending on the size of this nonogram, the duration it took to the player to complete this nonogram, and the number of actions the player performed.

## Preamble

Create a folder `nonogram` where you will create a file `analysis.py` and the file `__init__.py` (required to make Python treat the directories as containing [package](https://docs.python.org/3/tutorial/modules.html#packages)). You will write the code of this mission in the file `analysis.py`:

```shell
.
├── Pipfile
└── nonogram
    ├── __init__.py
    └── analysis.py
```

## Waypoint 1: String Representation of the Clues of a Nonogram

The clues of a nonogram indicate for each column and each row of the nonogram the length of groups of marks of a column, respectively of a row, separated with one or more blanks.

For example:

```text
            ║   | 1 |   | 3 | 1 |   |   | 2 ║
            ║ 6 | 3 | 6 | 1 | 1 | 1 | 3 | 2 ║
            ║ 1 | 1 | 1 | 2 | 1 | 6 | 1 | 1 ║
    ========+===+===+===+===+===+===+===+===+
          6 ║ X | X | X | X | X | X |   |   ║
    --------+---+---+---+---+---+---+---+---+
      1 2 1 ║ X |   | X | X |   |   |   | X ║
    --------+---+---+---+---+---+---+---+---+
          8 ║ X | X | X | X | X | X | X | X ║
    --------+---+---+---+---+---+---+---+---+
        3 2 ║ X | X | X |   |   | X | X |   ║
    --------+---+---+---+---+---+---+---+---+
          8 ║ X | X | X | X | X | X | X | X ║
    --------+---+---+---+---+---+---+---+---+
    1 1 1 1 ║ X |   | X |   |   | X |   | X ║
    --------+---+---+---+---+---+---+---+---+
        1 2 ║   |   |   | X |   | X | X |   ║
    --------+---+---+---+---+---+---+---+---+
      4 1 1 ║ X | X | X | X |   | X |   | X ║
    ========+===+===+===+===+===+===+===+===+
```

The clues for each column from left to right are:

- column 1: `6-1`
- column 2: `1-3-1`
- column 3: `6-1`
- column 4: `3-1-2`
- column 5: `1-1-1`
- column 6: `1-6`
- column 7: `3-1`
- column 8: `2-2-1`

We represent the clues for the columns with a coma-separated string:

```text
6-1,1-3-1,6-1,3-1-2,1-1-1,1-6,3-1,2-2-1
```

The clues for each row from top to bottom are:

- column 1: `6`
- column 2: `1-2-1`
- column 3: `8`
- column 4: `3-2`
- column 5: `8`
- column 6: `1-1-1-1`
- column 7: `1-2`
- column 8: `4-1-1`

We represent the clues for the rows with a coma-separated string:

```text
6,1-2-1,8,3-2,8,1-1-1-1,1-2,4-1-1
```

Finally, we represent the clues of the nonogram with a string where the column clues and the line clues are separated with the character `|`:

```text
6-1,1-3-1,6-1,3-1-2,1-1-1,1-6,3-1,2-2-1|6,1-2-1,8,3-2,8,1-1-1-1,1-2,4-1-1
```

We define the following language, using [Backus–Naur Form (BNF)](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form), to declare the clues of a nonogram:

```text
column_clues ::= integer "-" column_clues | integer
columns_clues ::= column_clues "," columns_clues | column_clues

row_clues ::= integer "-" row_clues | integer
rows_clues ::= row_clues "," rows_clues | row_clues

clues :== columns_clues "|" rows_clues
```

Write a function `convert_nonogram_clues_string_to_tuples` that takes an argument `s` (a string representation of the clues of a nonogram, which respects our BNF language) and that returns a tuple `(column_clues, row_clues)` where:

- `column_clues`: A tuple of tuples of integers representing the clues of the nonogram's columns from left to right.
- `row_clues`: A tuple of tuples of integers representing the clues of the nonogram's rows from top to bottom.

For example:

```python
>>> nonogram_clues = convert_nonogram_clues_string_to_tuples('6-1,1-3-1,6-1,3-1-2,1-1-1,1-6,3-1,2-2-1|6,1-2-1,8,3-2,8,1-1-1-1,1-2,4-1-1')
>>> import pprint
>>> pprint.pprint(nonogram_clues)
(((6, 1), (1, 3, 1), (6, 1), (3, 1, 2), (1, 1, 1), (1, 6), (3, 1), (2, 2, 1)),
 ((6,), (1, 2, 1), (8,), (3, 2), (8,), (1, 1, 1, 1), (1, 2), (4, 1, 1)))
```

## Waypoint 2: Class `NonogramClues`

Write a class `NonogramClues` that represents the clues of a nonogram.

The constructor of this class takes two arguments:

- `column_clues`: A tuple of tuples of integers representing the clues of the nonogram's columns from left to right.
- `row_clues`: A tuple of tuples of integers representing the clues of the nonogram's rows from top to bottom.

For example:

```python
>>> nonogram_clues = NonogramClues(
...     ((6, 1), (1, 3, 1), (6, 1), (3, 1, 2), (1, 1, 1), (1, 6), (3, 1), (2, 2, 1)),
...     ((6,), (1, 2, 1), (8), (3, 2), (8,), (1, 1, 1, 1), (1, 2), (4, 1, 1))
... )
```

Add the read-only properties `column_clues` and `row_clues` that returns the list of arrays of integers representing the clues of the nonogram's columns, respectively the list of array of integers representing the clues of the nonogram's rows.

For example:

```python
>>> nonogram_clues.column_clues
((6, 1), (1, 3, 1), (6, 1), (3, 1, 2), (1, 1, 1), (1, 6), (3, 1), (2, 2, 1))
>>> nonogram_clues.row_clues
((6,), (1, 2, 1), (8), (3, 2), (8,), (1, 1, 1, 1), (1, 2), (4, 1, 1))
```

### Factory Method

Add a class method `from_string` that takes an argument `s` corresponding to the string representation of the columns' and the rows' clues of a nonogram. This method returns an object `NonogramClues`.

```python
>>> nonogram_clues = NonogramClues.from_string('6-1,1-3-1,6-1,3-1-2,1-1-1,1-6,3-1,2-2-1|6,1-2-1,8,3-2,8,1-1-1-1,1-2,4-1-1')
>>> nonogram_clues.column_clues
((6, 1), (1, 3, 1), (6, 1), (3, 1, 2), (1, 1, 1), (1, 6), (3, 1), (2, 2, 1))
>>> nonogram_clues.row_clues
((6,), (1, 2, 1), (8), (3, 2), (8,), (1, 1, 1, 1), (1, 2), (4, 1, 1))
```

### String Representations of an object `NonogramClues`

[Override](https://en.wikipedia.org/wiki/Method_overriding) the built-in function [`__str__`](https://docs.python.org/3/reference/datamodel.html#object.__str__) in your class `NonogramClues`. This function is used for returning a nice string representation of an object.

For example:

```python
>>> str(nonogram_clues)
'6-1,1-3-1,6-1,3-1-2,1-1-1,1-6,3-1,2-2-1|6,1-2-1,8,3-2,8,1-1-1-1,1-2,4-1-1'
```

Override the built-in function [`__repr__`](https://docs.python.org/3/reference/datamodel.html#object.__repr__) in your class `NonogramClues`. This function is used for returning the official string representation of an object.

The official Python documentation states that: "_If at all possible, this should look like a **valid Python expression** that could be used to recreate an object with the same value (given an appropriate environment)._"

For example:

```python
>>> nonogram_clues
NonogramClues(((6, 1), (1, 3, 1), (6, 1), (3, 1, 2), (1, 1, 1), (1, 6), (3, 1), (2, 2, 1)), ((6,), (1, 2, 1), (8,), (3, 2), (8,), (1, 1, 1, 1), (1, 2), (4, 1, 1)))
```

## Waypoint 3: String Representation of the Actions of a Nonogram

We define the following Backus–Naur Form (BNF) to describe the syntax of a player's action performed on the cell of a nonogram:

```text
action ::= action_type "-" cell_x "-" cell_y "-" action_time
action_time ::= integer
action_type ::= "F" | "M" | "C"
cell_x ::= integer
cell_y ::= integer
```

The term `cell_x` and `cell_y` represent the location of a cell in the nonogram's matrix. The origin `(0, 0)` of the nonogram is located at the upper left of the matrix.

The term `action_time` represents a variant of the [UNIX timestamp](https://www.epochconverter.com/), that is the number of milliseconds that have elapsed since the Unix epoch (00:00:00 UTC on 1 January 1970), minus leap seconds.

The term `action_type` represents the action of a player:

- `F`: Fill the cell
- `M`: Mark the cell as empty
- `C`: Clear the cell

For example:

```text
F-5-3-1586485456389
```

Represents the action of filling the cell `(5, 3)` of a nonogram, performed by a player on Friday, April 10, 2020 at 02:24:16.389 AM:

```text
      ║   |   |   |   |   |   |   |
    ==+===+===+===+===+===+===+===+...
      ║   |   |   |   |   |   |   |
    --+---+---+---+---+---+---+---+...
      ║   |   |   |   |   |   |   |
    --+---+---+---+---+---+---+---+...
      ║   |   |   |   |   |   |   |
    --+---+---+---+---+---+---+---+...
      ║   |   |   |   |   | X |   |
    --+---+---+---+---+---+---+---+...
      :   :   :   :   :   :   :   :
      .   .   .   .   .   .   .   .
```

### Class `NonogramAction` & Constructor

Write a class `NonogramAction` that represent the action performed by a player on a nonogram matrix. The **constructor** of this class must implement the following specification:

```python
"""
Build an object `NonogramAction`.


:param action_type: An item of the enumeration `ActionType`.

:param x: The column number of the cell, starting with `0`.

:param y: The row number of the cell, starting with `0`.

:param action_time: An object `datetime`, with milliseconds precision,
    representing the exact time when the player performed this action.
"""
```

The [Python enumeration](https://docs.python.org/3/library/enum.html) `ActionType` represents the different possible actions of a player:

```python
ActionType = enum.Enum(
    'ActionType',
    'F M C'
)
```

For example:

```python
>>> import datetime
>>> nonogram_action = NonogramAction(
...     NonogramAction.ActionType.F,
...     5,
...     3,
...     datetime.datetime(2020, 4, 10, 2, 24, 16, 389000, tzinfo=datetime.timezone.utc))
```

### Properties

Write the read-only properties `type`, `x`, `y`, and `time` of the class `NonogramAction` that return the corresponding value of a nonogram action.

For example:

```python
>>> nonogram_action.type
<ActionType.F: 1>
>>> nonogram_action.x
5
>>> nonogram_action.y
3
>>> nonogram_action.time
datetime.datetime(2020, 4, 10, 2, 24, 16, 389000, tzinfo=datetime.timezone.utc)
```

### String Representations of an object `NonogramAction`

Override the built-in function [`__str__`](https://docs.python.org/3/reference/datamodel.html#object.__str__) in your class `NonogramAction`. This function is used for returning a nice string representation of an object.

For example:

```python
>>> str(nonogram_action)
'F-5-3-1586485456389'
```

Override the built-in function [`__repr__`](https://docs.python.org/3/reference/datamodel.html#object.__repr__) in your class `NonogramAction`. This function is used for returning the official string representation of an object.

For example:

```python
>>> nonogram_clues
NonogramAction(NonogramAction.ActionType.F, 5, 3, datetime.datetime(2020, 4, 10, 2, 24, 16, 389000, tzinfo=datetime.timezone.utc))
```

### Factory Methods

Write a class method `from_string` that takes an argument `s`, a string representation of an action performed on the cell of a nonogram. This method returns an object `NonogramAction`.

For example:

```python
>>> nonogram_action = NonogramAction.from_string('F-5-3-1586485456389')
>>> nonogram_action.type
<ActionType.F: 1>
>>> nonogram_action.x
5
>>> nonogram_action.y
3
>>> nonogram_action.time
datetime.datetime(2020, 4, 10, 2, 24, 16, 389000, tzinfo=datetime.timezone.utc)
```

Write a class method `from_strings` that takes an argument `s`, a comma-separated string representation of actions. This method returns a list of objects `NonogramAction`.

For example:

```python
>>> nonogram_actions = NonogramAction.from_strings('F-3-5-1588598226098,M-3-5-1588598234546,C-3-5-1588598235322,C-0-5-1588598236091,C-1-5-1588598236834,M-2-5-1588598238819,C-2-5-1588598239204,F-2-5-1588598239706')
>>> pprint.pprint(nonogram_actions)
[NonogramAction(NonogramAction.ActionType.F, 3, 5, datetime.datetime(2020, 5, 4, 13, 17, 6, 98000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.M, 3, 5, datetime.datetime(2020, 5, 4, 13, 17, 14, 546000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.C, 3, 5, datetime.datetime(2020, 5, 4, 13, 17, 15, 322000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.C, 0, 5, datetime.datetime(2020, 5, 4, 13, 17, 16, 91000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.C, 1, 5, datetime.datetime(2020, 5, 4, 13, 17, 16, 834000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.M, 2, 5, datetime.datetime(2020, 5, 4, 13, 17, 18, 819000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.C, 2, 5, datetime.datetime(2020, 5, 4, 13, 17, 19, 204000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.F, 2, 5, datetime.datetime(2020, 5, 4, 13, 17, 19, 706000, tzinfo=datetime.timezone.utc))]
```

## Waypoint 4: Nonogram Game Session of a Player

Write a class `NonogramSession` whose constructor takes the following required arguments:

- `user`: An object `User` representing the player who played a nonogram.
- `start_time`: An object `datetime.datetime` that represents the time when the nonogram game session started, i.e., the time when nonogram was displayed to the player.
- `clues`: A list of objects `NonogramClues` corresponding to the clues of the nonogram that was given to the player to solve.
- `actions`: A list of objects `NonogramActions` that represent the actions the player performed to solve (or not) the nonogram. This list can be given in whatever order the actions were performed.

### Properties

Add the following read-only properties to the class `NonogramSession`:

- `user`: An object `User` representing the player who played a nonogram.
- `start_time`: The object `datetime.datetime` that represents the time when the nonogram game session started.
- `clues`: The list of objects `NonogramClues` corresponding to the clues of the nonogram that was given to the player to solve.
- `actions`: The list of objects `NonogramActions` that represent the actions the player performed.

For example:

```text
            ║ 1 |   |   ║
            ║ 1 | 2 | 2 ║
    ========+===+===+===+
          2 ║ X | X |   ║
    --------+---+---+---+
          2 ║   | X | X ║
    --------+---+---+---+
        1 1 ║ X |   | X ║
    ========+===+===+===+
```

```python
>>> nonogram_clues = NonogramClues.from_string('1-1,2,2|2,2,1-1')
>>> nonogram_actions = NonogramAction.from_strings('F-0-0-1588598226098,F-1-0-1588598229019,M-0-1-1588598234546,F-1-1-1588598235322,F-2-1-1588598236091,F-2-2-1588598236834,M-1-2-1588598238819,F-0-2-1588598239204')
>>> nonogram_session = NonogramSession(
...     None,
...     datetime.datetime(2020, 5, 4, 8, 17, 6, 389000, tzinfo=datetime.timezone.utc),
...     nonogram_clues,
...     nonogram_actions)
>>> nonogram_session.user
>>> nonogram_session.start_time
datetime.datetime(2020, 5, 4, 8, 17, 6, 389000, tzinfo=datetime.timezone.utc)
>>> nonogram_session.clues
NonogramClues(((6, 1), (1, 3, 1), (6, 1), (3, 1, 2), (1, 1, 1), (1, 6), (3, 1), (2, 2, 1)), ((6,), (1, 2, 1), (8,), (3, 2), (8,), (1, 1, 1, 1), (1, 2), (4, 1, 1)))
>>> pprint.pprint(nonogram_session.actions)
[NonogramAction(NonogramAction.ActionType.F, 2, 5, datetime.datetime(2020, 5, 4, 13, 17, 5, 466000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.F, 3, 5, datetime.datetime(2020, 5, 4, 13, 17, 6, 98000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.M, 3, 5, datetime.datetime(2020, 5, 4, 13, 17, 14, 546000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.C, 3, 5, datetime.datetime(2020, 5, 4, 13, 17, 15, 322000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.C, 0, 5, datetime.datetime(2020, 5, 4, 13, 17, 16, 91000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.C, 1, 5, datetime.datetime(2020, 5, 4, 13, 17, 16, 834000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.M, 2, 5, datetime.datetime(2020, 5, 4, 13, 17, 18, 819000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.C, 2, 5, datetime.datetime(2020, 5, 4, 13, 17, 19, 204000, tzinfo=datetime.timezone.utc)),
 NonogramAction(NonogramAction.ActionType.F, 2, 5, datetime.datetime(2020, 5, 4, 13, 17, 19, 706000, tzinfo=datetime.timezone.utc))]
```

Also add the following read-only calculated properties:

- `cells`: A 2D array that represents the cells, from top to bottom, and from left to right, whose boolean values indicate whether the respective cell has been filled or not.
- `height`: The number of rows of the nonogram.
- `width`: The number of columns of the nonogram.
- `size`: Returns a tuple `(width, height)` that represents the number of columns, respectively the number of rows, of the nonogram.

```python
>>> nonogram_session.cells
[[True, True, False],
 [False, True, True],
 [True, False, True]]
>>> nonogram_session.width
3
>>> nonogram_session.height
3
>>> nonogram_session.size
(3, 3)
```

These properties are calculating, using formulas to derive their values from the values of other attributes. The calculation of these properties **SHOULD NOT** be performed in the constructor of the class `NonogramSession`, but when these properties are read ([_lazy loading_](https://en.wikipedia.org/wiki/Lazy_loading)). Indeed, the calculation of these properties **SHOULD NOT** be performed each time these properties are read from ([_cache_](<https://en.wikipedia.org/wiki/Cache_(computing)>)).

### String Representation of an object `NonogramSession`

Override the built-in function [`__str__`](https://docs.python.org/3/reference/datamodel.html#object.__str__) in your class `NonogramSession`. This function is used for displaying the matrix representing the response of the player where the character `X` represents a cell that the player has filled in and the character `.` represents an empty cell.

For example:

```python
>>> str(nonogram_session)
'XX.\n.XX\nX.X'
>>> print(str(nonogram_session))
XX.
.XX
X.X
```

## Waypoint 5: Check whether a Nonogram is Solved

Write a read-only property `is_solved` of the class `NonogramSession` that indicates whether the player has solved the nonogram, i.e., whether the player's actions successfully matched the clues of this nonogram.

For example:

```python
>>> nonogram_clues = NonogramClues.from_string('1-1,2,2|2,2,1-1')
>>> nonogram_actions = NonogramAction.from_strings('F-0-0-1588598226098,F-1-0-1588598229019,M-0-1-1588598234546,F-1-1-1588598235322,F-2-1-1588598236091,F-2-2-1588598236834,M-1-2-1588598238819,F-0-2-1588598239204')
>>> nonogram_session = NonogramSession(
...     None,
...     datetime.datetime(2020, 5, 4, 8, 17, 6, 389000, tzinfo=datetime.timezone.utc),
...     nonogram_clues,
...     nonogram_actions)
>>> nonogram_session.is_solved
True
```

## Waypoint 6: Calculate the Duration of a Player's Nonogram Game Session

Write a function `calculate_nonogram_effective_duration` that takes an argument `NonogramSession` and that returns the duration in milliseconds between the time the game session started and the time the player performed their last action.

For example:

```python
>>> calculate_nonogram_effective_duration(nonogram_session)
18012815.0
```

## Waypoint 7: Calculate the Difficulty to Solve a Column or a Row of a Nonogram

We would like to evaluate the difficulty of solving a particular column or row (line) of a nonogram.

Write a function `calculate_nonogram_line_difficulty` that takes 2 arguments `clues` and `line_length`:

- `clues`: A tuple of integers representing the clues of a line.

- `line_length`: The number of cells of the line.

The function returns a value between `0.0` and `1.0` that corresponds to the difficulty of solving this line.

For example:

```python
>>> calculate_nonogram_line_difficulty((5,), 5)
0.0
>>> calculate_nonogram_line_difficulty((1, 1, 1), 5)
0.0
>>> calculate_nonogram_line_difficulty((1, 2), 5)
0.19999999999999996
>>> calculate_nonogram_line_difficulty((1, 2, 1), 8)
0.25
```

We describe below, a heuristic to calculate the difficulty of solving the line of a nonogram.

### Difficulty based on the Degree of Freedom

Our methodology relies on the number of clues that are given to the player to solve the line and the number of cells these clues allow the player to fill in this line. The less choices (degree of freedom) the player has to complete the line, the more deterministic, the easier. The more possibilities, the more difficult.

(1) For example:

 Solving the following row composed of 5 cells, for which the clues are the integer `5`, is trivial:

```text
---+---+---+---+---+---+        ---+---+---+---+---+---+
 5 ║   |   |   |   |   ║  --->   5 ║ X | X | X | X | X ║
---+---+---+---+---+---+        ---+---+---+---+---+---+
```

This is a no brainer. The difficulty is `0.0`.

(2) Another similar example is:

```text
-------+---+---+---+---+---+        -------+---+---+---+---+---+
 1 1 1 ║   |   |   |   |   ║  --->   1 1 1 ║ X |   | X |   | X ║
-------+---+---+---+---+---+        -------+---+---+---+---+---+
```

The answer is obvious. There a not many possibilities, but only one. The difficulty is also `0.0`.

(3) The next example is more complicated:

```text
-----+---+---+---+---+---+
 1 2 ║   |   |   |   |   ║
-----+---+---+---+---+---+
```

There are several possibilities to solve this line:

```text
-----+---+---+---+---+---+   -----+---+---+---+---+---+   -----+---+---+---+---+---+
 1 2 ║ X |   | X | X  |  ║    1 2 ║ X |   |   | X | X ║    1 2 ║   | X |   | X | X ║
-----+---+---+---+---+---+   -----+---+---+---+---+---+   -----+---+---+---+---+---+
```

1. These clues `(1, 2)` allows to fill in **`3`** of the **`5`** cells in this line.

2. There are `2` clues (`clues_count`). This means there is at least **`1`** empty cell between these `2` series of filled cells.

Therefore the difficulty to fill this line is `1.0 - (3 + 1) / 5`, i.e., **`0.2`** (or `0.19999999999999996` with [Python floating-point numbers](https://docs.python.org/3/tutorial/floatingpoint.html)...).

(3) A last example:

```text
-------+---+---+---+---+---+---+---+---+
 1 2 1 ║   |   |   |   |   |   |   |   ║
-------+---+---+---+---+---+---+---+---+
```

1. The line has **`8`** cells.

2. The clues `(1, 2, 1)` allows us to fill **`4`** cells in this line.

3. There are `3` clues (`clues_count`). This means there are at least **`2`** empty cell between these `3` series of filled cells.

Therefore the difficulty to fill this line is `1.0 - (4 + 2) / 8`, i.e., **`0.25`**.

### Difficulty to Read and Interpret the Clues

The process of data analysis becomes more complex with the increasing number of connected information. The more clues for a line, the more time the human brain takes to process.

Even if the following lines are trivial to solve, as there is only one possibility to solve these lines, no degree of freedom, they are still increasing in difficulty because of the number of clues that need to be processed.

```text
---+---+---+---+---+---+---+---+---+         ---+---+---+---+---+---+---+---+---+
 8 ║   |   |   |   |   |   |   |   ║  --->    8 ║ X | X | X | X | X | X | X | X ║
---+---+---+---+---+---+---+---+---+         ---+---+---+---+---+---+---+---+---+

-----+---+---+---+---+---+---+---+---+         -----+---+---+---+---+---+---+---+---+
 3 4 ║   |   |   |   |   |   |   |   ║  --->    3 4 ║ X | X | X |   | X | X | X | X ║
-----+---+---+---+---+---+---+---+---+         -----+---+---+---+---+---+---+---+---+

-------+---+---+---+---+---+---+---+---+        -------+---+---+---+---+---+---+---+---+
 1 3 2 ║   |   |   |   |   |   |   |   ║  -->    1 3 2 ║ X |   | X | X | X |   | X | X ║
-------+---+---+---+---+---+---+---+---+        -------+---+---+---+---+---+---+---+---+
```

A line can have a maximum of `n` clues, where `n` is half the number of the cells contained in this line. For example, if a line has `8` cells, the maximum number of clues is `4`:

```text
--------+---+---+---+---+---+---+---+---+
1 1 1 1 ║ X |   | X |   | X |   | X |   ║
--------+---+---+---+---+---+---+---+---+
```

We define the difficulty to read and interpret the clues of a line as the ratio of the number of clues that are given (minus `1`) and the maximum number of clues a line of this length can have (minus `1`).

(1) For example :

```text
---+---+---+---+---+---+---+---+---+         ---+---+---+---+---+---+---+---+---+
 8 ║   |   |   |   |   |   |   |   ║  --->    8 ║ X | X | X | X | X | X | X | X ║
---+---+---+---+---+---+---+---+---+         ---+---+---+---+---+---+---+---+---+
```

The readability difficulty is `(1 - 1) / (4 - 1)`, which is `0`.

(2) Another example:

```text
-------+---+---+---+---+---+---+---+---+        -------+---+---+---+---+---+---+---+---+
 1 3 2 ║   |   |   |   |   |   |   |   ║  -->    1 3 2 ║ X |   | X | X | X |   | X | X ║
-------+---+---+---+---+---+---+---+---+        -------+---+---+---+---+---+---+---+---+
```

The readability difficulty is `(3 - 1) / (4 - 1)`, which is `0.6666666666666666`.

### Overall Difficulty

The function `calculate_nonogram_line_difficulty` returns the average of the two difficulties mentioned above.

For example:

```text
-----+---+---+---+---+---+
 1 2 ║ X |   | ? | ? | ? ║
-----+---+---+---+---+---+
```

| Difficulty        | Calculation         | Score      |
| ----------------- | ------------------- | ---------- |
| Degree of Freedom | `1.0 - (3 + 1) / 5` | `0.2`      |
| Readability       | `(2 - 1) / (3 - 1)` | `0.5`      |
| Overall           | `(0.2 + 0.5) / 2`   | **`0.35`** |

```python
>>> calculate_nonogram_line_difficulty((1, 2), 5)
0.35
>>> calculate_nonogram_line_difficulty((8, ), 8)
0.0
>>> calculate_nonogram_line_difficulty((1, 3, 2), 8)
0.3333333333333333
```

## Waypoint 8: Calculate the Optimal Number of Actions to Solve a Nonogram

Write a function `calculate_nonogram_optimal_action_number` that takes an argument `nonogram_session` (an object `NonogramSession`) and returns the minimal number of actions (filling cells) a player SHOULD have done to solve this nonogram.

This function raises an exception `ValueError` if the nonogram session `nonogram_session` has not been solved by the player.

For example:

```python
>>> nonogram_clues = NonogramClues.from_string('1-1,2,2|2,2,1-1')
>>> nonogram_actions = NonogramAction.from_strings('F-0-0-1588598226098,F-1-0-1588598229019,M-0-1-1588598234546,F-1-1-1588598235322,F-2-1-1588598236091,F-2-2-1588598236834,M-1-2-1588598238819,F-0-2-1588598239204')
>>> >>> nonogram_session = NonogramSession(
...     None,
...     datetime.datetime(2020, 5, 4, 8, 17, 6, 389000, tzinfo=datetime.timezone.utc),
...     nonogram_clues,
...     nonogram_actions)
>>> nonogram_session.is_solved
True
>>> print(str(nonogram_session))
XX.
.XX
X.X
>>> calculate_nonogram_optimal_action_number(nonogram_session)
6
```

## Waypoint 9: Calculate the Effective Number of Actions Performed by the Player

Write a function `calculate_nonogram_effective_action_number` that takes an argument `nonogram_session` (an object `NonogramSession`) and returns the number of effective actions that the player performed to solve (or not) the nonogram. An effective action is an action that the player performs to fill (`F`) or to clear (`C`) a cell of the nonogram.

This function also takes an optional argument `toggle_mode` (a boolean that defaults to `False`) that indicates whether the actions `F`ill, `M`ark, and `C`lear on a cell are performed by multiple clicks on this cell:

```text
1st click    2nd click   3rd click   4th click
 -+---+-      -+---+-     -+---+-     -+---+-
  | X |        | / |       |   |       |   |
 -+---+-      -+---+-     -+---+-     -+---+-

  Fill          Mark       Clear       Fill

[<--- Toggle State Selection --->] ...
```

The toggle mode is active when the nonogram game application uses a game controller (input device) that associates a click to a direct action (`F`ill, `M`ark, and `C`lear) on the cell that is clicked. The toggle mode is not used when a nonogram game application allows the player to select the particular action he would like to perform on a cell.

When the toggle mode is active, a player who wants to mark an empty cell needs to click two times on this cell: the first click fills the cell, the second click marks the cell as empty. Therefore, when the toggle mode is active, the function `calculate_nonogram_effective_action_number` must count only `1` action (`M`), not `2` actions. In this mode, a `M`ark action actually replaces a `F`ill action.

For example:

```text
       ║ 1 | 1 |   ║     F-0-0-1593160278088
       ║ 1 | 1 | 2 ║     F-0-1-1593160279070
    ===+===+===+===+     M-0-1-1593160279246
     2 ║ X | X |   ║     F-0-2-1593160281156
    ---+---+---+---+     F-1-2-1593160281839
     1 ║ / | / | X ║     F-2-2-1593160282492
    ---+---+---+---+     F-1-0-1593160284516
     3 ║ X | X | X ║     F-1-1-1593160285415
    ===+===+===+===+     M-1-1-1593160285586
                         F-2-1-1593160288354
```

```python
>>> nonogram_clues = NonogramClues.from_string('1-1,1-1,2|2,1,3')
>>> nonogram_actions = NonogramAction.from_strings('F-0-0-1593160278088,F-0-1-1593160279070,M-0-1-1593160279246,F-0-2-1593160281156,F-1-2-1593160281839,F-2-2-1593160282492,F-1-0-1593160284516,F-1-1-1593160285415,M-1-1-1593160285586,F-2-1-1593160288354')
>>> nonogram_session = NonogramSession(
...     None,
...     datetime.datetime(2020, 6, 26, 8, 31, 14, 729000, tzinfo=datetime.timezone.utc),
...     nonogram_clues,
...     nonogram_actions)
>>> print(str(nonogram_session))
XX.
..X
XXX
>>> calculate_nonogram_optimal_action_number(nonogram_session)
6
>>> calculate_nonogram_effective_action_number(nonogram_session)
8
>>> calculate_nonogram_effective_action_number(nonogram_session, toggle_mode=True)
6
```

## Waypoint 10: Calculate Game Session Optimal Duration

Write a function `calculate_nonogram_optimal_duration` that takes two arguments `clues` and `cells`:

- `nonogram_session`: An object `NonogramSession`. The player MUST have solved this nonogram; the function does calculate a solution for this nonogram (which is out of the scope of this library which is only used for scoring a player who successfully solved a nonogram).

- `optimal_time_per_action`: Duration in milliseconds for a player to think and perform the next action in order to solve a nonogram.

We assume that the larger a nonogram is to solve, the more time it takes to figure out the next possible action. These values are totally empirical:

| Size  | Time per Action |
| ----- | --------------- |
| 3x3   | 0.8 s           |
| 5x5   | 0.9 s           |
| 8x8   | 1.3 s           |
| 10x10 | 1.8 s           |

The function `calculate_nonogram_optimal_duration` first sums the difficulties of all the columns and all the rows, and calculates the **overall average difficulty** of the nonogram.

The function calculates the estimated duration for a player to solve a nonogram based on the number of cells that composed the nonogram and the optimal time per action:

```python
standard_duration = number_of_cells * optimal_time_per_action
```

The function finally calculates the optimal duration as the estimated duration to complete a nonogram of this size, weighted with the actual difficulty of this nonogram:

```python
optimal_duration = standard_duration * (1 - difficulty)
```

For example:

```python
>>> nonogram_clues = NonogramClues.from_string('1-1,1-1,2|2,1,3')
>>> nonogram_actions = NonogramAction.from_strings('F-0-0-1593160278088,F-0-1-1593160279070,M-0-1-1593160279246,F-0-2-1593160281156,F-1-2-1593160281839,F-2-2-1593160282492,F-1-0-1593160284516,F-1-1-1593160285415,M-1-1-1593160285586,F-2-1-1593160288354')
>>> nonogram_session = NonogramSession(
...     None,
...     datetime.datetime(2020, 6, 26, 8, 31, 14, 729000, tzinfo=datetime.timezone.utc),
...     nonogram_clues,
...     nonogram_actions)
>>> print(str(nonogram_session))
XX.
..X
XXX
>>> calculate_nonogram_optimal_duration(nonogram_session, 0.8 * 1000)
5866.666666666666
```

## Waypoint 11: Score the Nonogram Game Session of a Player

Write a function `calculate_nonogram_score` that calculate the score of a player who played a nonogram. The function takes an argument `noongram_session` (an object `NonogramSession`). The function returns a score between `0.0` and `1.0`, where `0.0` is the worst possible score and `1.0` is the best possible score.

If the player has been unable to solve the nonogram, the function returns the value `0.0`.

Otherwise, the function calculates the player's efficiency as the ratio between the optimal number of actions to solve the nonogram with the actual number of actions the player performed to solve this nonogram:

```python
efficiency_score = optimal_action_number / effective_action_number
```

The function also calculates the player's speed to complete the nonogram as the ratio between the optimal duration to solve the nonogram with the actual duration it took the player to solve this nonogram:

```python
speed_score = optimal_duration / effective_duration
```

The function calculates the overall score of the player as the average of the efficiency score and the speed score. However, the efficiency score and the speed score are weighted depending on the size of the nonogram:

| Size | Efficiency Weight | Speed Weight |
| ---- | ----------------- | ------------ |
| 3x3  | 1                 | 1            |
| 5x5  | 3/5               | 2/5          |
| 8    | 2/3               | 1/3          |
| 10   | 3/4               | 1/4          |

```python
>>> nonogram_clues = NonogramClues.from_string('1-1,1-1,2|2,1,3')
>>> nonogram_actions = NonogramAction.from_strings('F-0-0-1593160278088,F-0-1-1593160279070,M-0-1-1593160279246,F-0-2-1593160281156,F-1-2-1593160281839,F-2-2-1593160282492,F-1-0-1593160284516,F-1-1-1593160285415,M-1-1-1593160285586,F-2-1-1593160288354')
>>> nonogram_session = NonogramSession(
...     None,
...     datetime.datetime(2020, 6, 26, 8, 31, 14, 729000, tzinfo=datetime.timezone.utc),
...     nonogram_clues,
...     nonogram_actions)
>>> print(str(nonogram_session))
XX.
..X
XXX
>>> calculate_nonogram_score(nonogram_session)
0.5902905198776758
```
