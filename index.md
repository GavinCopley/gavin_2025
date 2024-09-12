---
layout: base
title: Home
description: Website Home Page
hide: true
image: /images/white_mario.png
menu: nav/index_nav.html
---

<div class="typeAnimation">
  <h1>Welcome to My Website.........</h1>
</div>

<style>
  .typeAnimation h1 {
    overflow: hidden;
    font-family: 'Open Sans', sans-serif;
    font-weight: 700;
    border-right: .015em solid orange;
    white-space: nowrap;
    margin: 0 auto;
    letter-spacing: 0.015em;
    animation: typing 10s steps(30, end) forwards, blink-caret 1s step-end infinite;
    animation-delay: 0ms;
    animation-fill-mode: forwards;
    color: #000000;
    width: 30ch;
  }

  @keyframes typing {
    0% { width: 0; }
    25%, 50%, 75% { width: 100%; }
    100% { width: 100%; }
  }

  @keyframes blink-caret {
    from, to { border-color: transparent; }
    50% { border-color: white; }
  }
</style>

Click the image for another inspiring quote

<!-- Quote Image -->
<img id="imageDisplay" src="{{site.baseurl}}/images/index/quote.png" alt="Cycling Images" width="400" height="300" style="cursor:pointer;">

<script>
  const images = [
    '{{site.baseurl}}/images/index/quote.png',
    '{{site.baseurl}}/images/index/download.jpeg',
    '{{site.baseurl}}/images/index/quote2.jpeg',
  ];

  let currentIndex = 0;
  const imageElement = document.getElementById('imageDisplay');

  imageElement.addEventListener('click', function() {
    currentIndex = (currentIndex + 1) % images.length;
    imageElement.src = images[currentIndex];
  });
</script>
<!-- END OF QUOTE IMAGES -->

<script>
  document.addEventListener("DOMContentLoaded", function() {
    setTimeout(function() {
      document.querySelector("body").classList.add("loaded");
    }, 2000)
  });
</script>

<!-- SNAKE GAME START HERE -->
<canvas id="aiCanvas" width="400" height="400"></canvas>
<canvas id="playerCanvas" width="400" height="400"></canvas>

<style>
  canvas {
    border: 2px solid black; /* Add black border */
    background-color: lightgray; /* Set background color to light gray */
  }
</style>

<script>
  const aiCanvas = document.getElementById("aiCanvas");
  const playerCanvas = document.getElementById("playerCanvas");
  const aiCtx = aiCanvas.getContext("2d");
  const playerCtx = playerCanvas.getContext("2d");

  const gridSize = 20;
  const tileCount = aiCanvas.width / gridSize;

  // Game state
  let gameRunning = false; // Flag to control if the game is running
  let gameStarted = false; // Flag to control if the game has started

  // Timer variables
  let timer = 60; // Timer set for 60 seconds
  let timerInterval; // Interval ID for the timer

  // AI Snake Game State
  let aiSnake = [{ x: 10, y: 10 }];
  let aiFood = { x: Math.floor(Math.random() * tileCount), y: Math.floor(Math.random() * tileCount) };
  let aiDirection = { x: 0, y: 0 };
  let aiScore = 0;
  let aiHighScore = 0; // Track AI high score

  // Player Snake Game State
  let playerSnake = [{ x: 10, y: 10 }];
  let playerFood = { x: Math.floor(Math.random() * tileCount), y: Math.floor(Math.random() * tileCount) };
  let playerDirection = { x: 0, y: 0 };
  let playerScore = 0;
  let playerHighScore = 0; // Track player high score

  // Function to display the "Press Space" message on player canvas only
  function showStartMessage() {
    const message1 = "Press Space to Play Snake";
    const message2 = "Against ChatGPT";

    // Clear the player canvas before drawing the message
    playerCtx.clearRect(0, 0, playerCanvas.width, playerCanvas.height);

    // Draw the first part of the message on one line and the second on the next
    playerCtx.fillStyle = "white";
    playerCtx.font = "20px Arial";
    playerCtx.fillText(message1, playerCanvas.width / 2 - 120, playerCanvas.height / 2 - 10); // Adjust X and Y for centering
    playerCtx.fillText(message2, playerCanvas.width / 2 - 80, playerCanvas.height / 2 + 20); // Adjust X and Y for centering
  }

  // Function to display the winner message
  function showWinnerMessage() {
    let winnerMessage;
    if (playerHighScore > aiHighScore) {
      winnerMessage = "Player Wins!";
    } else if (aiHighScore > playerHighScore) {
      winnerMessage = "ChatGPT Wins!";
    } else {
      winnerMessage = "It's a Tie!";
    }

    // Clear the player canvas before drawing the message
    playerCtx.clearRect(0, 0, playerCanvas.width, playerCanvas.height);

    // Draw the winner message
    playerCtx.fillStyle = "white";
    playerCtx.font = "30px Arial";
    playerCtx.textAlign = "center";
    playerCtx.fillText(winnerMessage, playerCanvas.width / 2, playerCanvas.height / 2);
  }

  function updateTimer() {
    if (timer > 0) {
      timer--;
    } else {
      clearInterval(timerInterval);
      gameRunning = false;
      showWinnerMessage();
    }
  }

  // AI and Player Game Mechanics Functions
  function checkCollision(snake) {
    const head = snake[0];
    for (let i = 1; i < snake.length; i++) {
      if (snake[i].x === head.x && snake[i].y === head.y) {
        return true;
      }
    }
    return false;
  }

  function autoMoveAI() {
    const head = aiSnake[0];
    if (head.x < aiFood.x) aiDirection = { x: 1, y: 0 };
    else if (head.x > aiFood.x) aiDirection = { x: -1, y: 0 };
    else if (head.y < aiFood.y) aiDirection = { x: 0, y: 1 };
    else if (head.y > aiFood.y) aiDirection = { x: 0, y: -1 };
  }

  function updateAI() {
    autoMoveAI();
    const newHead = { x: aiSnake[0].x + aiDirection.x, y: aiSnake[0].y + aiDirection.y };

    if (newHead.x < 0 || newHead.x >= tileCount || newHead.y < 0 || newHead.y >= tileCount || checkCollision(aiSnake)) {
      if (aiScore > aiHighScore) aiHighScore = aiScore; // Update high score if needed
      resetAISnake();
    } else {
      aiSnake.unshift(newHead);

      if (newHead.x === aiFood.x && newHead.y === aiFood.y) {
        aiScore++;
        placeAIFood();
      } else {
        aiSnake.pop();
      }
    }
  }

  function drawAI() {
    aiCtx.clearRect(0, 0, aiCanvas.width, aiCanvas.height);
    aiSnake.forEach(part => {
      aiCtx.fillStyle = "lime";
      aiCtx.fillRect(part.x * gridSize, part.y * gridSize, gridSize, gridSize);
    });

    aiCtx.fillStyle = "red";
    aiCtx.fillRect(aiFood.x * gridSize, aiFood.y * gridSize, gridSize, gridSize);

    aiCtx.fillStyle = "white";
    aiCtx.font = "20px Arial";
    aiCtx.fillText("AI Score: " + aiScore, 10, aiCanvas.height - 30);
    aiCtx.fillText("AI High Score: " + aiHighScore, 10, aiCanvas.height - 10);
  }

  function placeAIFood() {
    aiFood = { x: Math.floor(Math.random() * tileCount), y: Math.floor(Math.random() * tileCount) };
  }

  function resetAISnake() {
    aiSnake = [{ x: 10, y: 10 }];
    aiDirection = { x: 0, y: 0 };
    aiScore = 0;
    placeAIFood();
  }

  document.addEventListener("keydown", (event) => {
    const arrowKeys = ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"];
    if (arrowKeys.includes(event.key)) {
      event.preventDefault();
    }

    switch (event.key) {
      case "w": case "ArrowUp":
        if (playerDirection.y === 0) playerDirection = { x: 0, y: -1 };
        break;
      case "a": case "ArrowLeft":
        if (playerDirection.x === 0) playerDirection = { x: -1, y: 0 };
        break;
      case "s": case "ArrowDown":
        if (playerDirection.y === 0) playerDirection = { x: 0, y: 1 };
        break;
      case "d": case "ArrowRight":
        if (playerDirection.x === 0) playerDirection = { x: 1, y: 0 };
        break;
    }
  });

  function updatePlayer() {
    const newHead = { x: playerSnake[0].x + playerDirection.x, y: playerSnake[0].y + playerDirection.y };

    if (newHead.x < 0 || newHead.x >= tileCount || newHead.y < 0 || newHead.y >= tileCount || checkCollision(playerSnake)) {
      if (playerScore > playerHighScore) playerHighScore = playerScore; // Update high score if needed
      resetPlayerSnake();
    } else {
      playerSnake.unshift(newHead);

      if (newHead.x === playerFood.x && newHead.y === playerFood.y) {
        playerScore++;
        placePlayerFood();
      } else {
        playerSnake.pop();
      }
    }
  }

  function drawPlayer() {
    playerCtx.clearRect(0, 0, playerCanvas.width, playerCanvas.height);
    playerSnake.forEach(part => {
      playerCtx.fillStyle = "blue";
      playerCtx.fillRect(part.x * gridSize, part.y * gridSize, gridSize, gridSize);
    });

    playerCtx.fillStyle = "yellow";
    playerCtx.fillRect(playerFood.x * gridSize, playerFood.y * gridSize, gridSize, gridSize);

    playerCtx.fillStyle = "white";
    playerCtx.font = "20px Arial";
    playerCtx.fillText("Player Score: " + playerScore, 76, playerCanvas.height - 30);
    playerCtx.fillText("Player High Score: " + playerHighScore, 100, playerCanvas.height - 10);

    // Display the timer on the player canvas
    playerCtx.font = "30px Arial";
    playerCtx.textAlign = "center";
    playerCtx.fillText("Time Left: " + timer, playerCanvas.width / 2, 30);
  }

  function placePlayerFood() {
    playerFood = { x: Math.floor(Math.random() * tileCount), y: Math.floor(Math.random() * tileCount) };
  }

  function resetPlayerSnake() {
    playerSnake = [{ x: 10, y: 10 }];
    playerDirection = { x: 0, y: 0 };
    playerScore = 0;
    placePlayerFood();
  }

  document.addEventListener("keydown", (event) => {
    if (event.code === "Space") {
      if (!gameRunning) {
        gameRunning = true; // Start the game when space is pressed
        gameStarted = true; // Indicate that the game has started
        resetAISnake();
        resetPlayerSnake();
        timer = 60; // Reset the timer
        timerInterval = setInterval(updateTimer, 1000); // Start the timer
      }
    }
  });

  function gameLoop() {
    if (!gameRunning) {
      if (!gameStarted) {
        showStartMessage(); // Display the start message if the game isn't running
      }
      return;
    }

    updateAI();
    drawAI();

    updatePlayer();
    drawPlayer();
  }

  setInterval(gameLoop, 100);
</script>
