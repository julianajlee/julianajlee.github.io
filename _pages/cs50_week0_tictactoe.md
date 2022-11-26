---
title: "CS50-AI Week 0. Project 1: Tic-Tac-Toe"
permalink: /_pages/cs50/week0-tictactoe
tags:
  - cs50
  - Python
toc: true
---

```python
import math, copy

X = "X"
O = "O"
EMPTY = None


def initial_state():
    """
    Returns starting state of the board.
    """
    return [[EMPTY, EMPTY, EMPTY],
            [EMPTY, EMPTY, EMPTY],
            [EMPTY, EMPTY, EMPTY]]


def player(board):
    """
    Returns player who has the next turn on a board.
    """
    
    # Initial state X first
    if board == initial_state():
        return X
    else:
        
        # If X > O, it's O's turn; vice versa
        X_count=0
        O_count=0
    
        for row in board:
            for cell in row:
                if cell == X:
                    X_count += 1
                elif cell == O:
                    O_count += 1
    
        
        if X_count > O_count:
            return O
        else:
            return X
    
    #raise NotImplementedError


def actions(board):
    """
    Returns set of all possible actions (i, j) available on the board.
    """
    
    moves = []
    
    for row in range(3):
        for cell in range(3):
            if board[row][cell] == EMPTY:
                moves.append([row,cell])
                
    return moves
    
    #raise NotImplementedError


def result(board, action):
    """
    Returns the board that results from making move (i, j) on the board.
    """
    
    new_board = copy.deepcopy(board)
    
    if (board[action[0]][action[1]] == EMPTY):
        new_board[action[0]][action[1]] = player(board)
    else:
        raise Exception
    
    return new_board


def winner(board):
    """
    Returns the winner of the game, if there is one.
    """
    win=""
    
    for i in range(3):
        # horizontal
        if board[i][0] == board[i][1] == board[i][2]:
            if board[i][0] != EMPTY and board[i][1] != EMPTY and board[i][2] != EMPTY:
                win=board[i][0]
        
        # vertical
        if board[0][i] == board[1][i] == board[2][i]:
            if board[0][i] != EMPTY and board[1][i] != EMPTY and board[2][i] != EMPTY:
                win=board[0][i]
        
    # diagonal1
    if board[0][0] == board[1][1] == board[2][2]:
        if board[0][0] != EMPTY and board[1][1] != EMPTY and board[2][2] != EMPTY:
            win=board[0][0]     
    
    # diagonal2
    if board[0][2] == board[1][1] == board[2][0]:
        if board[0][2] != EMPTY and board[1][1] != EMPTY and board[2][0] != EMPTY:
            win=board[0][2]      
    
    if win != "":
        return win
    else:
        return None
    
    #raise NotImplementedError


def terminal(board):
    """
    Returns True if game is over, False otherwise.
    """
    
    if winner(board) != None:
        return True
    
    # If there's no winner
    elif winner(board) == None:
        return False
    
    # If the board has no empty spaces left
    elif any (None in x for x in board)==False:
        return True
    
        #raise NotImplementedError


def utility(board):
    """
    Returns 1 if X has won the game, -1 if O has won, 0 otherwise.
    """
    
    if terminal(board) == True:
        if winner(board) == X:
            return 1
        elif winner(board) == O:
            return -1
        else:
            return 0
    
    #raise NotImplementedError


def minimax(board):
    """
    Returns the optimal action for the current player on the board.
    """
    
    # If the board is terminated
    if terminal(board) == True:
        return None
    
    current_player = player(board)
    
    # If it's X turn
    if current_player == X:
        value = -math.inf
        best_move = None
        
        for action in actions(board):
            max = min_value(result(board, action))
            if max > value:
                value = max
                best_move = action
        return best_move
    
    # If it's O turn
    else:
        value = math.inf
        best_move = None
        
        for action in actions(board):
            min = max_value(result(board, action))
            if min < value:
                value = min
                best_move=action
        return best_move

def max_value(board):
    if terminal(board):
        return utility(board)
    
    value = -math.inf
    
    for action in actions(board):
        value = max(value, min_value(result(board, action)))
    return value

def min_value(board):
    if terminal(board):
        return utility(board)
    
    value = math.inf
    
    for action in actions(board):
        value = min(value, max_value(result(board, action)))
    return value


```
