---
toc: true
comments: false
layout: post
title: Cookie Clicker
type: hacks
courses: { csp: {week: 3} }
permalink: /games/cookieclicker
menu: nav/games_nav.html
---
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        body {
            text-align: center;
            font-family: Arial, sans-serif;
        }
        #cookie {
            width: 200px;
            cursor: pointer;
        }
        #shop {
            margin-top: 20px;
        }
        button {
            margin: 10px;
            padding: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .icon {
            width: 20px;
            height: 20px;
            margin-right: 10px;
        }
        #save-load {
            margin-top: 20px;
        }
        #save-load input {
            width: 300px;
            margin: 10px;
        }
        #messages {
            margin-top: 20px;
            color: green;
        }
    </style>
</head>
<body>
    <div id="game">
        <div id="cookie-section">
            <img id="cookie" src="../images/cookie_icon.png" alt="Cookie">
            <audio id="click-sound" src="../sounds/sound1.mp3"></audio>
        </div>
        <p id="cookie-count">Cookies: 0</p>
        <p id="cookies-per-second">Cookies per second: 0</p>
        <div id="shop">
            <button id="buy-granny">
                <img src="../images/grandma-cookie-removebg-preview.png" class="icon" alt="Granny Icon">
                Buy Granny (Cost: <span id="granny-cost">100</span> cookies)
            </button>
            <p id="granny-count">Grannies: 0 (each makes 1 cookie per second)</p>
            <button id="buy-factory">
                <img src="../images/factory-cookie-removebg-preview.png" class="icon" alt="Factory Icon">
                Buy Factory (Cost: <span id="factory-cost">500</span> cookies)
            </button>
            <p id="factory-count">Factories: 0 (each makes 5 cookies per second)</p>
            <button id="buy-plane">
                <img src="../images/plane-icon-f.png" class="icon" alt="Plane Icon">
                Buy Plane (Cost: <span id="plane-cost">2000</span> cookies)
            </button>
            <p id="plane-count">Planes: 0 (each makes 10 cookies per second)</p>
            <button id="buy-worldwide-factory">
                <img src="../images/factories-cookie-removebg-preview.png" class="icon" alt="Worldwide Factory Icon">
                Buy Worldwide Factory Network (Cost: <span id="worldwide-factory-cost">100,000</span> cookies)
            </button>
            <p id="worldwide-factory-count">Worldwide Factories: 0 (each makes 300 cookies per second)</p>
        </div>
        <div id="messages"></div>
        <div id="save-load">
            <button id="save-game">Save Game</button>
            <button id="load-game">Load Game</button>
            <input type="text" id="save-code" placeholder="Paste your save code here">
        </div>
    </div>
    <script>
        let cookies = 0;
        let totalCookies = 0; // Tracks total cookies made
        let cookiesPerSecond = 0;
        let grannyCost = 100;
        let factoryCost = 500;
        let planeCost = 2000;
        let worldwideFactoryCost = 100000;
        let grannyCount = 0;
        let factoryCount = 0;
        let planeCount = 0;
        let worldwideFactoryCount = 0;
        const cookieCountDisplay = document.getElementById("cookie-count");
        const cookiesPerSecondDisplay = document.getElementById("cookies-per-second");
        const cookie = document.getElementById("cookie");
        const clickSound = document.getElementById("click-sound");
        const grannyCountDisplay = document.getElementById("granny-count");
        const factoryCountDisplay = document.getElementById("factory-count");
        const planeCountDisplay = document.getElementById("plane-count");
        const worldwideFactoryCountDisplay = document.getElementById("worldwide-factory-count");
        const grannyCostDisplay = document.getElementById("granny-cost");
        const factoryCostDisplay = document.getElementById("factory-cost");
        const planeCostDisplay = document.getElementById("plane-cost");
        const worldwideFactoryCostDisplay = document.getElementById("worldwide-factory-cost");
        const messageDisplay = document.getElementById("messages");
        // Milestones for total cookies produced
        let milestones = [100, 500, 1000, 5000, 100000, 1000000, 10000000];
        let messages = [
            "You've produced your first 100 cookies!",
            "People are starting to buy them!",
            "People like your cookies!",
            "Your cookies are being sold in several countries!",
            "A renowned chef declared that you make the best cookies in the world",
            "Your cookies are known worldwide!",
            "You have the most successful cookie brand in the world",
        ];
        let milestoneIndex = 0;
        // Function to update the displayed cookie count and cookies per second
        function updateDisplay() {
            cookieCountDisplay.innerText = `Cookies: ${cookies}`;
            cookiesPerSecondDisplay.innerText = `Cookies per second: ${cookiesPerSecond}`;
            grannyCountDisplay.innerText = `Grannies: ${grannyCount} (each makes 1 cookie per second)`;
            factoryCountDisplay.innerText = `Factories: ${factoryCount} (each makes 5 cookies per second)`;
            planeCountDisplay.innerText = `Planes: ${planeCount} (each makes 10 cookies per second)`;
            worldwideFactoryCountDisplay.innerText = `Worldwide Factories: ${worldwideFactoryCount} (each makes 300 cookies per second)`;
            grannyCostDisplay.innerText = grannyCost;
            factoryCostDisplay.innerText = factoryCost;
            planeCostDisplay.innerText = planeCost;
            worldwideFactoryCostDisplay.innerText = worldwideFactoryCost;
        }
        // Function to show milestone messages based on total cookies produced
        function showMilestoneMessage() {
            if (milestoneIndex < milestones.length && totalCookies >= milestones[milestoneIndex]) {
                messageDisplay.innerText = messages[milestoneIndex];
                milestoneIndex++;
                setTimeout(() => { messageDisplay.innerText = ""; }, 3000);
            }
        }
        // Save game progress
        function saveGame() {
            const gameState = {
                cookies,
                totalCookies,
                cookiesPerSecond,
                grannyCost,
                factoryCost,
                planeCost,
                worldwideFactoryCost,
                grannyCount,
                factoryCount,
                planeCount,
                worldwideFactoryCount,
                milestoneIndex
            };
            const saveCode = btoa(JSON.stringify(gameState)); // Convert object to a base64 string
            document.getElementById("save-code").value = saveCode; // Display save code in the input field
        }
        // Load game progress
        function loadGame() {
            const saveCode = document.getElementById("save-code").value;
            if (saveCode) {
                try {
                    const gameState = JSON.parse(atob(saveCode)); // Decode the base64 string and parse JSON
                    cookies = gameState.cookies;
                    totalCookies = gameState.totalCookies;
                    cookiesPerSecond = gameState.cookiesPerSecond;
                    grannyCost = gameState.grannyCost;
                    factoryCost = gameState.factoryCost;
                    planeCost = gameState.planeCost;
                    worldwideFactoryCost = gameState.worldwideFactoryCost;
                    grannyCount = gameState.grannyCount;
                    factoryCount = gameState.factoryCount;
                    planeCount = gameState.planeCount;
                    worldwideFactoryCount = gameState.worldwideFactoryCount;
                    milestoneIndex = gameState.milestoneIndex;
                    updateDisplay(); // Update UI with loaded data
                } catch (error) {
                    console.error("Invalid save code", error);
                    alert("Invalid save code. Please try again.");
                }
            }
        }
        // Event listeners for save/load buttons
        document.getElementById("save-game").addEventListener("click", saveGame);
        document.getElementById("load-game").addEventListener("click", loadGame);
        // Click event to add 1 cookie on each click
        cookie.addEventListener("click", () => {
            cookies += 1;
            totalCookies += 1; // Increases total cookies count
            clickSound.play();
            updateDisplay();
            showMilestoneMessage();
        });
        // Buy granny event
        document.getElementById("buy-granny").addEventListener("click", () => {
            if (cookies >= grannyCost) {
                cookies -= grannyCost;
                grannyCount++;
                cookiesPerSecond += 1; // each granny adds 1 cookie per second
                grannyCost = Math.floor(grannyCost * 1.15); // increase granny cost slightly
                updateDisplay();
            }
        });
        // Buy factory event
        document.getElementById("buy-factory").addEventListener("click", () => {
            if (cookies >= factoryCost) {
                cookies -= factoryCost;
                factoryCount++;
                cookiesPerSecond += 5; // each factory adds 5 cookies per second
                factoryCost = Math.floor(factoryCost * 1.15); // increase factory cost slightly
                updateDisplay();
            }
        });
        // Buy plane event
        document.getElementById("buy-plane").addEventListener("click", () => {
            if (cookies >= planeCost) {
                cookies -= planeCost;
                planeCount++;
                cookiesPerSecond += 10; // each plane adds 10 cookies per second
                planeCost = Math.floor(planeCost * 1.2); // increase plane cost more significantly
                updateDisplay();
            }
        });
        // Buy worldwide factory event
        document.getElementById("buy-worldwide-factory").addEventListener("click", () => {
            if (cookies >= worldwideFactoryCost) {
                cookies -= worldwideFactoryCost;
                worldwideFactoryCount++;
                cookiesPerSecond += 300; // each worldwide factory adds 300 cookies per second
                worldwideFactoryCost = Math.floor(worldwideFactoryCost * 1.25); // increase worldwide factory cost more significantly
                updateDisplay();
            }
        });
        // Add cookies per second from grannies, factories, planes, and worldwide factories
        setInterval(() => {
            cookies += cookiesPerSecond;
            totalCookies += cookiesPerSecond; // Increment total cookies
            updateDisplay();
            showMilestoneMessage();
        }, 1000);
    </script>
</body>