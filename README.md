# Tic_Tac_Toe
from random import choice
from math import inf

board = [[0, 0, 0],
         [0, 0, 0],
         [0, 0, 0]]

def print_board(board):
    chars = {1: 'X', -1: 'O', 0: ' '}
    for row in board:
        for cell in row:
            ch = chars[cell]
            print(f'| {ch} |', end='')
        print('\n' + '---------------')
    print('===============')

def clear_board(board):
    for row in range(3):
        for col in range(3):
            board[row][col] = 0

def is_winning_player(board, player):
    conditions = [[board[0][0], board[0][1], board[0][2]],
                  [board[1][0], board[1][1], board[1][2]],
                  [board[2][0], board[2][1], board[2][2]],
                  [board[0][0], board[1][0], board[2][0]],
                  [board[0][1], board[1][1], board[2][1]],
                  [board[0][2], board[1][2], board[2][2]],
                  [board[0][0], board[1][1], board[2][2]],
                  [board[0][2], board[1][1], board[2][0]]]

    if [player, player, player] in conditions:
        return True

    return False

def is_game_won(board):
    return is_winning_player(board, 1) or is_winning_player(board, -1)

def print_result(board):
    if is_winning_player(board, 1):
        print('X has won!\n')
    elif is_winning_player(board, -1):
        print('O\'s have won!\n')
    else:
        print('Draw\n')

def get_blanks(board):
    blanks = []
    for row in range(3):
        for col in range(3):
            if board[row][col] == 0:
                blanks.append([row, col])
    return blanks

def is_board_full(board):
    return len(get_blanks(board)) == 0

def set_move(board, row, col, player):
    board[row][col] = player

def player_move(board):
    valid_moves = {1: [0, 0], 2: [0, 1], 3: [0, 2],
                   4: [1, 0], 5: [1, 1], 6: [1, 2],
                   7: [2, 0], 8: [2, 1], 9: [2, 2]}
    while True:
        try:
            move = int(input('Enter a number between 1-9: '))
            if move < 1 or move > 9:
                print('Invalid move! Try again!')
            elif valid_moves[move] not in get_blanks(board):
                print('Invalid move! Try again!')
            else:
                row, col = valid_moves[move]
                set_move(board, row, col, 1)
                print_board(board)
                break
        except (KeyError, ValueError):
            print('Enter a number!')

def get_best_move(board, player):
    best_score = -inf
    best_move = None
    alpha = -inf
    beta = inf
    blanks = get_blanks(board)
    for blank in blanks:
        row, col = blank
        board[row][col] = player
        score = minimax(board, 0, False, alpha, beta)
        board[row][col] = 0
        if score > best_score:
            best_score = score
            best_move = blank
        alpha = max(alpha, best_score)
        if alpha >= beta:
            break
    return best_move

def heuristic_evaluation(board):
    if is_winning_player(board, 1):
        return 1
    elif is_winning_player(board, -1):
        return -1
    else:
        return 0

def minimax(board, depth, is_maximizing, alpha, beta):
    scores = {1: -1, -1: 1, 0: 0}
    if is_winning_player(board, 1):
        return scores[1]
    elif is_winning_player(board, -1):
        return scores[-1]
    elif is_board_full(board):
        return scores[0]

    if is_maximizing:
        best_score = -inf
        blanks = get_blanks(board)
        for blank in blanks:
            row, col = blank
            board[row][col] = 1
            score = minimax(board, depth + 1, False, alpha, beta)
            board[row][col] = 0
            best_score = max(score, best_score)
            alpha = max(alpha, best_score)
            if alpha >= beta:
                break
        return best_score
    else:
        best_score = inf
        blanks = get_blanks(board)
        for blank in blanks:
            row, col = blank
            board[row][col] = -1
            score = minimax(board, depth + 1, True, alpha, beta)
            board[row][col] = 0
            best_score = min(score, best_score)
            beta = min(beta, best_score)
            if alpha >= beta:
                break
        return best_score

def computer_move(board):
    print('Computer\'s move:')
    move = get_best_move(board, -1)
    if move:
        row, col = move
        set_move(board, row, col, -1)
    print_board(board)

def play_game():
    print('Welcome to Tic Tac Toe!')
    while True:
        clear_board(board)
        print_board(board)
        
        while True:
            player_role = input('Do you want to play as 1 or 2? (1/2): ')
            if player_role.upper() not in ['1', '2']:
                print('Invalid choice! Please enter 1 or 2.')
            else:
                break

        if player_role.upper() == '1':
            while True:
                player_move(board)
                if is_game_won(board) or is_board_full(board):
                    break
                computer_move(board)
                if is_game_won(board) or is_board_full(board):
                    break
        else:
            while True:
                computer_move(board)
                if is_game_won(board) or is_board_full(board):
                    break
                player_move(board)
                if is_game_won(board) or is_board_full(board):
                    break

        print_result(board)
        play_again = input('Do you want to play again? (yes/no): ')
        if play_again.lower() != 'yes':
            break

play_game()
