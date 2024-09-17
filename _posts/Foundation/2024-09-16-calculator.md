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
    justify-content: right; /* Align the equation to the right */
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
    color: white; /* Set text color to white */
    background-color: #333; /* Optional: set background color for better contrast */
  }
</style>

<!-- Add a container for the animation -->
<div id="animation">
  <div class="calculator-container">
    <!-- Button to toggle history -->
    <button id="toggle-history" style="grid-column: span 4; margin-bottom: 10px;">Show History</button>
    <!-- Operation history, hidden by default -->
    <div id="operation-history" class="history-container">
      History:
    </div>
    <!-- Result (Output) -->
    <div class="calculator-output" id="output">0</div>
    <!-- Row 1 -->
    <div class="calculator-number">1</div>
    <div class="calculator-number">2</div>
    <div class="calculator-number">3</div>
    <div class="calculator-operation">+</div>
    <!-- Row 2 -->
    <div class="calculator-number">4</div>
    <div class="calculator-number">5</div>
    <div class="calculator-number">6</div>
    <div class="calculator-operation">-</div>
    <!-- Row 3 -->
    <div class="calculator-number">7</div>
    <div class="calculator-number">8</div>
    <div class="calculator-number">9</div>
    <div class="calculator-operation">*</div>
    <!-- Row 4 -->
    <div class="calculator-number">π</div>
    <div class="calculator-number">0</div>
    <div class="calculator-operation">√</div>
    <div class="calculator-operation">^</div>
    <!-- Row 5 -->
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
var equation = ''; // Variable to keep track of the full equation

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
    if (nextReady) {
      output.innerHTML = value;
      nextReady = false;
    } else {
      output.innerHTML += value;
    }
    equation += value; // Build the equation string
  } else {
    if (value == "π") {
      output.innerHTML = Math.PI.toFixed(4); // Set π to 3.1415
      equation += Math.PI.toFixed(4); // Add π value to the equation
      nextReady = true;
    } else {
      if (!output.innerHTML.includes(".")) {
        output.innerHTML += value;
        equation += value; // Append the decimal point
      }
    }
  }
  displayEquation(); // Display the equation as it builds
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
    equation = equation.slice(0, -output.innerHTML.length) + output.innerHTML; // Replace current number with its negation
    displayEquation(); // Display updated equation
    return;
  }
  if (firstNumber == null) {
    firstNumber = parseFloat(output.innerHTML);
    operator = choice;
    equation += " " + operator + " "; // Append operator to the equation
    nextReady = true;
    displayEquation(); // Display updated equation
    return;
  }
  firstNumber = calculate(firstNumber, parseFloat(output.innerHTML));
  operator = choice;
  equation += " " + operator + " "; // Append operator after result
  output.innerHTML = firstNumber.toString();
  nextReady = true;
  displayEquation(); // Display updated equation
}

// Calculator
function calculate(first, second) { // Function to calculate the result of the equation
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
function equal () { // Function used when the equals button is clicked; calculates equation and displays it
    const secondNumber = parseFloat(output.innerHTML);
    const result = calculate(firstNumber, secondNumber);
    updateHistory(firstNumber, operator, secondNumber); // Add to history before calculation
    firstNumber = result;
    equation += " = " + result; // Add result to the equation string
    output.innerHTML = result.toString();
    nextReady = true;
    displayEquation(); // Display the final result equation
}

// Display the equation as it builds in the output
function displayEquation() {
  output.innerHTML = equation;
}

// Update history
function updateHistory(first, operator, second) {
  // Create a new entry in the history
  let historyEntry = `${first} ${operator} ${second} = ${output.innerHTML}`;
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
    equation = ''; // Clear the equation string
    nextReady = true;
    displayEquation(); // Display cleared output
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

// Function to delete the last character from the equation
function deleteLastCharacter() {
  const currentOutput = output.innerHTML;
  if (currentOutput.length > 1) {
    output.innerHTML = currentOutput.slice(0, -1);
    equation = equation.slice(0, -1); // Remove the last character from the equation
  } else {
    output.innerHTML = "0";
    equation = '';
    nextReady = true;
    displayEquation(); // Display cleared output
  }
}
</script>

<!--Vanta animations just for fun, load JS onto the page-->
<script src="{{site.baseurl}}/assets/js/three.r119.min.js"></script>
<script src="{{site.baseurl}}/assets/js/vanta.halo.min.js"></script>
<script src="{{site.baseurl}}/assets/js/vanta.birds.min.js"></script>
<script src="{{site.baseurl}}/assets/js/vanta.net.min.js"></script>
<script src="{{site.baseurl}}/assets/js/vanta.rings.min.js"></script>

<script>
// setup vanta scripts as functions
var vantaInstances = {
  halo: VANTA.HALO,
  birds: VANTA.BIRDS,
  net: VANTA.NET,
  rings: VANTA.RINGS
};

// obtain a random vanta function
var vantaInstance = vantaInstances[Object.keys(vantaInstances)[Math.floor(Math.random() * Object.keys(vantaInstances).length)]];

// run the animation
vantaInstance({
  el: "#animation",
  mouseControls: true,
  touchControls: true,
  gyroControls: false
});
</script>
