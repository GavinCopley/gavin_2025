---
toc: false
comments: false
layout: post
title: Tic Tac Toe
description: Play Tic Tac Toe!
type: hacks
courses: { csp: {week: 3} }
permalink: /games/tictactoe
menu: nav/games_nav.html
---

# Tic-Tac-Toe Game (Play Against AI or 2 Players)

Choose your game mode, then click the squares to play!

<div style="text-align: center;">
  <button onclick="setMode('ai')">Play Against AI</button>
  <button onclick="setMode('twoPlayer')">Play Two Player Mode</button>
  <br><br>

  <table id="ticTacToeBoard" style="margin: 0 auto; border: 2px solid black; border-collapse: collapse;">
    <tr>
      <td onclick="makeMove(this, 0)" style="width: 100px; height: 100px; text-align: center; font-size: 36px; border: 2px solid black;"></td>
      <td onclick="makeMove(this, 1)" style="width: 100px; height: 100px; text-align: center; font-size: 36px; border: 2px solid black;"></td>
      <td onclick="makeMove(this, 2)" style="width: 100px; height: 100px; text-align: center; font-size: 36px; border: 2px solid black;"></td>
    </tr>
    <tr>
      <td onclick="makeMove(this, 3)" style="width: 100px; height: 100px; text-align: center; font-size: 36px; border: 2px solid black;"></td>
      <td onclick="makeMove(this, 4)" style="width: 100px; height: 100px; text-align: center; font-size: 36px; border: 2px solid black;"></td>
      <td onclick="makeMove(this, 5)" style="width: 100px; height: 100px; text-align: center; font-size: 36px; border: 2px solid black;"></td>
    </tr>
    <tr>
      <td onclick="makeMove(this, 6)" style="width: 100px; height: 100px; text-align: center; font-size: 36px; border: 2px solid black;"></td>
      <td onclick="makeMove(this, 7)" style="width: 100px; height: 100px; text-align: center; font-size: 36px; border: 2px solid black;"></td>
      <td onclick="makeMove(this, 8)" style="width: 100px; height: 100px; text-align: center; font-size: 36px; border: 2px solid black;"></td>
    </tr>
  </table>

  <br>
  <button onclick="resetGame()">Reset Game</button>
  <p id="gameStatus"></p>
</div>

<script>
  let board = ["", "", "", "", "", "", "", "", ""];
  let currentPlayer = "X";
  let gameActive = true;
  let gameMode = "twoPlayer"; // Default mode

  const winningConditions = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6]
  ];

  function setMode(mode) {
    gameMode = mode;
    resetGame();
    document.getElementById("gameStatus").innerHTML = `Game mode: ${mode === 'ai' ? 'AI Mode' : 'Two Player Mode'}`;
  }

  function makeMove(cell, index) {
    if (board[index] !== "" || !gameActive) return;

    board[index] = currentPlayer;
    cell.innerHTML = currentPlayer;

    checkWinner();

    if (gameActive) {
      if (gameMode === "ai") {
        currentPlayer = "O"; // AI always plays "O"
        aiMove();
      } else {
        currentPlayer = currentPlayer === "X" ? "O" : "X";
      }
    }
  }

  function aiMove() {
    let bestMove = minimax(board, "O").index;

    if (bestMove !== undefined && gameActive) {
      board[bestMove] = "O";

      const cell = document.querySelectorAll("td")[bestMove];
      cell.innerHTML = "O";

      checkWinner();

      if (gameActive) {
        currentPlayer = "X"; // Return control to the player
      }
    }
  }

  function minimax(newBoard, player) {
    const availableMoves = newBoard
      .map((val, idx) => (val === "" ? idx : null))
      .filter(val => val !== null);

    // Terminal states: win, lose, or tie
    if (checkWin(newBoard, "X")) return { score: -10 };
    if (checkWin(newBoard, "O")) return { score: 10 };
    if (availableMoves.length === 0) return { score: 0 };

    let moves = [];

    // Loop through available moves
    for (let i = 0; i < availableMoves.length; i++) {
      let move = {};
      move.index = availableMoves[i];

      newBoard[availableMoves[i]] = player;

      if (player === "O") {
        let result = minimax(newBoard, "X");
        move.score = result.score;
      } else {
        let result = minimax(newBoard, "O");
        move.score = result.score;
      }

      newBoard[availableMoves[i]] = "";
      moves.push(move);
    }

    // Find the best move for AI
    let bestMove;
    if (player === "O") {
      let bestScore = -Infinity;
      for (let i = 0; i < moves.length; i++) {
        if (moves[i].score > bestScore) {
          bestScore = moves[i].score;
          bestMove = i;
        }
      }
    } else {
      let bestScore = Infinity;
      for (let i = 0; i < moves.length; i++) {
        if (moves[i].score < bestScore) {
          bestScore = moves[i].score;
          bestMove = i;
        }
      }
    }

    return moves[bestMove];
  }

  function checkWin(board, player) {
    for (let i = 0; i < winningConditions.length; i++) {
      const winCondition = winningConditions[i];
      if (winCondition.every(idx => board[idx] === player)) {
        return true;
      }
    }
    return false;
  }

  function checkWinner() {
    if (checkWin(board, currentPlayer)) {
      gameActive = false;
      document.getElementById("gameStatus").innerHTML = `Player ${currentPlayer} wins!`;
      return;
    }

    if (!board.includes("")) {
      gameActive = false;
      document.getElementById("gameStatus").innerHTML = `It's a draw!`;
    }
  }

  function resetGame() {
    board = ["", "", "", "", "", "", "", "", ""];
    gameActive = true;
    currentPlayer = "X";
    document.getElementById("gameStatus").innerHTML = "";

    const cells = document.querySelectorAll("td");
    cells.forEach(cell => cell.innerHTML = "");
  }
</script>