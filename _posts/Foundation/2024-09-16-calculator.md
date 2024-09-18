---
toc: true
comments: false
layout: post
title: Calculator
description: Very cool calculator
type: hacks
courses: { csp: {week: 3} }
permalink: /games/calculator
image: /images/calculator_image.png
menu: nav/games_nav.html
---

<style>
  .calculator-output {
    grid-column: span 4;
    grid-row: span 1;
    padding: 0.25em;
    font-size: 20px;
    border: 5px solid black;
    background-color: #FAF9F6;
    color: black;
    display: flex;
    align-items: center;
  }
  .calculator-number {
    color: black;
    background-color: #FAF9F6;
  }
  .calculator-operation {
    color: white;
    background-color: #808080;
  }
  .calculator-equals {
    color: black;
    background-color: #AEC6CF;
  }
  .calculator-clear {
    color: white;
    background-color: #FF6961;
  }
  .history-container {
    grid-column: span 4;
    padding: 0.5em;
    font-size: 16px;
    border-bottom: 1px solid black;
    display: none; /* Hidden by default */
    background-color: #333333; /* Dark background */
    color: white; /* White text */
  }
  .calculator-container {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 10px;
  max-width: 300px;
  margin: auto;
}

.calculator-number, .calculator-operation, .calculator-equals, .calculator-clear {
  padding: 20px;
  text-align: center;
  font-size: 18px;
  border-radius: 5px;
  cursor: pointer;
}

.calculator-output {
  grid-column: span 4;
  padding: 10px;
  font-size: 24px;
  text-align: right;
  border-radius: 5px;
}

button {
  padding: 10px;
  font-size: 16px;
  cursor: pointer;
}
</style>

<!-- Add a container for the animation and image -->
<div id="animation">
  <div class="calculator-container">
    <!-- Button to toggle history -->
    <button id="toggle-history" style="grid-column: span 4; margin-bottom: 10px;">Show History</button>
    <!-- Operation history, hidden by default -->
    <div id="operation-history" class="history-container">
      History:
    </div>
    <!-- Result -->
    <div class="calculator-output" id="output">0</div>
    <!-- Buttons grid for the calculator -->
    <div class="calculator-number">1</div>
    <div class="calculator-number">2</div>
    <div class="calculator-number">3</div>
    <div class="calculator-operation">+</div>
    <div class="calculator-number">4</div>
    <div class="calculator-number">5</div>
    <div class="calculator-number">6</div>
    <div class="calculator-operation">-</div>
    <div class="calculator-number">7</div>
    <div class="calculator-number">8</div>
    <div class="calculator-number">9</div>
    <div class="calculator-operation">*</div>
    <div class="calculator-number">π</div>
    <div class="calculator-number">0</div>
    <div class="calculator-operation">√</div>
    <div class="calculator-operation">^</div>
    <div class="calculator-clear">A/C</div>
    <div class="calculator-number">.</div>
    <div class="calculator-operation">±</div>
    <div class="calculator-equals">=</div>
    <!-- Clear History Button -->
    <button id="clear-history" style="grid-column: span 4; margin-top: 10px;">Clear History</button>
  </div>
</div>

<!-- JavaScript (JS) implementation of the calculator. -->
<script>
  // Initialize important variables to manage calculations
  var firstNumber = null;
  var operator = null;
  var nextReady = true;
  
  // Build objects containing key elements
  const output = document.getElementById("output");
  const history = document.getElementById("operation-history"); // Get the history div
  const toggleHistoryButton = document.getElementById("toggle-history"); // Get the toggle button
  const numbers = document.querySelectorAll(".calculator-number");
  const operations = document.querySelectorAll(".calculator-operation");
  const clear = document.querySelectorAll(".calculator-clear");
  const equals = document.querySelectorAll(".calculator-equals"); 
  const clearHistoryButton = document.getElementById("clear-history"); // Get the clear history button

  // Toggle history visibility
  toggleHistoryButton.addEventListener("click", function() {
    if (history.style.display === "none") {
      history.style.display = "block";
      toggleHistoryButton.textContent = "Hide History";
    } else {
      history.style.display = "none";
      toggleHistoryButton.textContent = "Show History";
    }
  });

  // Number buttons listener
  numbers.forEach(button => {
    button.addEventListener("click", function() {
      number(button.textContent);
    });
  });
  
  // Number action
  function number(value) {
    if (value != "." && value != "π") {
      if (nextReady == true) {
        output.innerHTML = value;
        if (value != "0") {
          nextReady = false;
        }
      } else {
        output.innerHTML = output.innerHTML + value;
      }
    } else {
      if (value == "π") {
        output.innerHTML = Math.PI.toFixed(4); // Set π to 3.1415
        nextReady = true;
      } else {
        if (output.innerHTML.indexOf(".") == -1) {
          output.innerHTML = output.innerHTML + value;
          nextReady = false;
        }
      }
    }
  }
  
  // Operation buttons listener
  operations.forEach(button => {
    button.addEventListener("click", function() {
      operation(button.textContent);
    });
  });

  function operation(choice) {
    if (choice === "±") {
      output.innerHTML = (-parseFloat(output.innerHTML)).toString();
      return;
    }
    if (firstNumber == null) {
      firstNumber = parseFloat(output.innerHTML);
      nextReady = true;
      operator = choice;
      return;
    }
    const secondNumber = parseFloat(output.innerHTML);
    const result = calculate(firstNumber, secondNumber);
    updateHistory(firstNumber, operator, secondNumber, result); // Update history after calculation
    firstNumber = result;
    operator = choice;
    output.innerHTML = result.toString();
    nextReady = true;
}

  // Calculator
  function calculate (first, second) { // function to calculate the result of the equation
      let result = 0;
      switch (operator) {
          case "+":
              result = first + second;
              break;
          case "-":
              result = first - second;
              break;
          case "*":
              result = first * second;
              break;
          case "/":
              result = first / second;
              break;
          case "^":
              result = first ** second;
              break;
          case "√":
              result = first ** (1/second);
              break;
          default: 
              break;
      }
      return result;
  }
  
  // Equals button listener
  equals.forEach(button => {
    button.addEventListener("click", function() {
      equal();
    });
  });
  
  // Equal action
  function equal() { 
    const secondNumber = parseFloat(output.innerHTML);
    const result = calculate(firstNumber, secondNumber);
    updateHistory(firstNumber, operator, secondNumber, result); // Update history after calculation
    firstNumber = result;
    output.innerHTML = result.toString();
    nextReady = true;
}
  function updateHistory(first, operator, second, result) {
    // Create a new entry in the history
    let historyEntry = `${first} ${operator} ${second} = ${result}`;
    let historyDiv = document.createElement("div");
    historyDiv.textContent = historyEntry;
    history.appendChild(historyDiv); // Append the new entry to the history
  }

  // Clear button listener
  clear.forEach(button => {
    button.addEventListener("click", function() {
      clearCalc();
    });
  });
  
  // A/C action
  function clearCalc () { // Clears calculator output, but not history
      firstNumber = null;
      output.innerHTML = "0";
      nextReady = true;
  }

  // Clear history button listener
  clearHistoryButton.addEventListener("click", function() {
    clearHistory();
  });

  // Clear history function
  function clearHistory() {
    history.innerHTML = "History:"; // Clear the history content
  }

  // Listen for keyboard events
  document.addEventListener("keydown", function(event) {
    const key = event.key;
    // Handle numbers and decimal point
    if (/^[0-9]$/.test(key) || key === ".") {
      number(key);
    }
    // Backspace key for delete
    if (key === "Backspace") {
      deleteLastCharacter();
    }
  });

  // Function to delete the last character
  function deleteLastCharacter() {
    const currentOutput = output.innerHTML;
    if (currentOutput.length > 1) {
      output.innerHTML = currentOutput.slice(0, -1);
    } else {
      output.innerHTML = "0";
      nextReady = true;
    }
  }
</script>
