---
layout: single
title: "Ron Graham's Game"
permalink: ron-grahams-game 
tags: [Artificial Intelligence]
---

For a job interview at WHO I was asked by build a numeric version of Noughts and Cross (Tic-Tac-Toe to some), this is called [Ron Graham](https://en.m.wikipedia.org/wiki/Ronald_Graham)'s Game ([repo](https://github.com/bquast/WHO-Academy-Challenge-Round-1-Ron-Grahams-Game/)).

# Ron Graham's Game

Ron Graham's Game is a numerical variant of Naughts and Crosses / Tic-Tac-Toe. In the general form, the board is a square matrix of length `L >= 3`, Player 1 has stones for all the Odd numbers in the range `1:L`, Player 2 has stones for all the Even numbers in the range `2:(L-1)`, a player wins if it completes a row/column/diagonal and the sum equals `(1:L)L/2`.

Ron Graham's Game is the variant where `L==3`, it has shown that Ron Graham's Game has an optimal strategy for the Player 1 (Odds), the variant where `L=4` has been shown to have an optimal strategy for Player 2 (Evens).

## Markowsky's definition
In the [Journal of Recreational Mathematics](https://en.m.wikipedia.org/wiki/Journal_of_Recreational_Mathematics) ([ISSN](https://en.m.wikipedia.org/wiki/International_Standard_Serial_Number): [0022-412X](https://www.worldcat.org/search?fq=x0:jrnl&q=n2:0022-412X)) [Vol. 22(2) 114-123, 1990](http://aturing.umcs.maine.edu/~markov/tictactoe.pdf) George Markowsky defines Ron Graham's Game as follows:

> Ron Graham's Game uses the standard 3x3 Tic-Tac-Toe board. Instead of calling the players X and O, we call them Odd and Even. Odd gets the numbers 1, 3, 5, 7, and 9, while Even gets the numbers 2, 4, 6, and 8. Odd goes first, after which the players take turns placing one of their numbers in the empty cells of the standard Tic-Tac-Toe board. Numbers may be used only once. The object of the game is to be the player who completes a line that sums to 15. As in Tic-Tac-Toe, a line is a row, column, or diagonal. Completing a line means putting the final number in the line so it sums to 15. Players are allowed to use numbers placed by the opponent to reach the sum of 15. Once a line contains two numbers whose sum is 15 or greater there is no way to complete that line, although filling in the remaining cell might be necessary to complete a different line. The setup for playing Graham's Game is quite simple. Start out with a piece of paper which has the familiar Tic-Tac-Toe board on it. To one side write a row with the numbers 1 3 5 7 9 in it and below it write a row with the numbers 2 4 6 8 in it. As the players use the numbers they must cross them off. Figure 1 shows the setup for playing Graham's Game. Before you read the analyses of Graham's Game, I recommend that you play it several times so you can form your own opinion about the best strategy.

![Figure 1](https://github.com/bquast/WHO-Academy-Challenge-Round-1-Ron-Grahams-Game/blob/master/Numeric-TicTacToe-Figure-1.png)

## Usage

The file [RonGrahamsGame.py](https://github.com/bquast/WHO-Academy-Challenge-Round-1-Ron-Grahams-Game/blob/master/RonGrahamsGame.py) is a self contained Python 3 file including the game and the optimiser to play against, it can be executed using:

    $ python RonGrahamsGame.py

It will first prompt you if you wish to level up (play against the optimiser), you can chose to do so, simply by pressing `Enter` (since it is the default option).

![demonstration](https://github.com/bquast/WHO-Academy-Challenge-Round-1-Ron-Grahams-Game/blob/master/demo.gif)


## Troubleshooting

If you encounter the error:

      File "RonGrahamsGame.py", line 71
        print('{} '.format(move), end='')
                                 ^
    SyntaxError: invalid syntax

This is caused by executing with `Python 2` instead of `Python 3`, because your system has `Python 2` as its default.
In that case you can execute using:

    $ python3 RonGrahamsGame.py


## Development

[RonGrahamsGame.py](https://github.com/bquast/WHO-Academy-Challenge-Round-1-Ron-Grahams-Game/blob/master/RonGrahamsGame.py) was developed with `Python 3.8.3` on a Arch Linux machine with Kernel `v.5.7.1`, it should run on any `Python 3` machine.


## License

The [LICENSE](https://github.com/bquast/WHO-Academy-Challenge-Round-1-Ron-Grahams-Game/blob/master/LICENSE) is MIT.


## Attribution

The game implementation is adapted from Alec Roques, the optimiser is based on Peter Norvig's AIMA code.


