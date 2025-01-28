---
toc: false
comments: false
layout: post
type: hacks
courses: { csp: {week: 14} }
permalink: /games/snakegame
---

<style>
    canvas {
      border: 2px solid black;
      background-color: lightgray;
    }

    #gameInfo {
      display: flex;
      justify-content: space-between;
      font-family: 'Arial', sans-serif;
      color: #f0f0f0;
      background-color: #333;
      padding: 20px;
      margin-top: 20px;
      border-radius: 10px;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
      width: fit-content;
      margin: 20px auto;
      position: relative;
    }

    .infoSection {
      width: 30%;
    }

    .infoSection p {
      margin: 10px 0;
      font-size: 18px;
      line-height: 1.6;
      font-weight: 500;
    }

    .infoSection p#playerScore, 
    .infoSection p#playerHighScore, 
    .infoSection p#aiScore, 
    .infoSection p#aiHighScore {
      font-size: 20px;
      font-weight: 600;
    }

    .infoSection p#timer {
      font-size: 20px;
      font-weight: 600;
      color: #ff9800;
    }

    .infoSection p#winnerMessage {
      font-size: 22px;
      font-weight: 700;
      text-align: center;
      margin-top: 20px;
      color: #00c853;
    }

    /* Positioning sections */
    #aiInfo {
      text-align: left;
    }

    #timerSection {
      text-align: center;
    }

    #playerInfo {
      text-align: right;
    }

</style>

<!-- Canvas elements for AI and Player -->
<canvas id="aiCanvas" width="400" height="400"></canvas>
<canvas id="playerCanvas" width="400" height="400"></canvas>

<!-- Display information outside of the canvas -->
<div id="gameInfo">
  <!-- AI Information Section -->
  <div class="infoSection" id="aiInfo">
    <p id="aiScore">AI Score: 0</p>
    <p id="aiHighScore">AI High Score: 0</p>
  </div>

  <!-- Timer and Winner Information -->
  <div class="infoSection" id="timerSection">
    <p id="timer">Time Left: 60</p>
    <p id="winnerMessage"></p>
  </div>

  <!-- Player Information Section -->
  <div class="infoSection" id="playerInfo">
    <p id="playerScore">Player Score: 0</p>
    <p id="playerHighScore">Player High Score: 0</p>
  </div>
</div>


<script>
    const aiCanvas = document.getElementById("aiCanvas");
    const playerCanvas = document.getElementById("playerCanvas");
    const aiCtx = aiCanvas.getContext("2d");
    const playerCtx = playerCanvas.getContext("2d");

    const gridSize = 20;
    const tileCount = aiCanvas.width / gridSize;

    // Game state
    let gameRunning = false;
    let gameStarted = false;

    // Timer variables
    let timer = 60;
    let timerInterval;

    // AI Snake Game State
    let aiSnake = [{ x: 10, y: 10 }];
    let aiFood = { x: Math.floor(Math.random() * tileCount), y: Math.floor(Math.random() * tileCount) };
    let aiDirection = { x: 0, y: 0 };
    let aiScore = 0;
    let aiHighScore = 0;

    // Player Snake Game State
    let playerSnake = [{ x: 10, y: 10 }];
    let playerFood = { x: Math.floor(Math.random() * tileCount), y: Math.floor(Math.random() * tileCount) };
    let playerDirection = { x: 0, y: 0 };
    let playerScore = 0;
    let playerHighScore = 0;

    // Function to update the text outside the canvas
    function updateInfoText() {
      // Update player and AI score
      document.getElementById("playerScore").textContent = "Player Score: " + playerScore;
      document.getElementById("playerHighScore").textContent = "Player High Score: " + playerHighScore;
      document.getElementById("aiScore").textContent = "AI Score: " + aiScore;
      document.getElementById("aiHighScore").textContent = "AI High Score: " + aiHighScore;
      
      // Update the timer
      document.getElementById("timer").textContent = "Time Left: " + timer;

      // Update winner message
      if (timer === 0) {
        let winnerMessage;
        if (playerHighScore > aiHighScore) {
          winnerMessage = "Player Wins!";
        } else if (aiHighScore > playerHighScore) {
          winnerMessage = "ChatGPT Wins!";
        } else {
          winnerMessage = "It's a Tie!";
        }
        document.getElementById("winnerMessage").textContent = winnerMessage;
      }
    }

    // Function to update the timer
    function updateTimer() {
      if (timer > 0) {
        timer--;
        updateInfoText();
      } else {
        clearInterval(timerInterval);
        gameRunning = false;
        updateInfoText();
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
        if (aiScore > aiHighScore) aiHighScore = aiScore;
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
        aiCtx.fillStyle = "yellow";
        aiCtx.fillRect(part.x * gridSize, part.y * gridSize, gridSize, gridSize);
      });

      aiCtx.fillStyle = "blue";
      aiCtx.fillRect(aiFood.x * gridSize, aiFood.y * gridSize, gridSize, gridSize);
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
        if (playerScore > playerHighScore) playerHighScore = playerScore;
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
        playerCtx.fillStyle = "lime";
        playerCtx.fillRect(part.x * gridSize, part.y * gridSize, gridSize, gridSize);
      });

      playerCtx.fillStyle = "red";
      playerCtx.fillRect(playerFood.x * gridSize, playerFood.y * gridSize, gridSize, gridSize);
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
          gameRunning = true;
          gameStarted = true;
          resetAISnake();
          resetPlayerSnake();
          timer = 60;
          timerInterval = setInterval(updateTimer, 1000);
        }
      }
    });
make s
    function gameLoop() {
      if (!gameRunning) {
        if (!gameStarted) {
          document.getElementById("winnerMessage").textContent = "Press Space to Play Snake Against ChatGPT";
        }
        return;
      }

      updateAI();
      drawAI();
      updatePlayer();
      drawPlayer();
      updateInfoText();
    }

    setInterval(gameLoop, 100);
  </script>