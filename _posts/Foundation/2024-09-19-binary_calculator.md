---
layout: post
title: Binary Calculator
description: Binary Calc
permalink: /games/binary_calculator
type: hacks
courses: { csp: {week: 3} }
menu: nav/games_nav.html
---

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Binary Calculator with RGB Conversion</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 20px;
        }
        .section {
            margin-bottom: 30px;
        }
        input {
            width: 200px;
            text-align: center;
            margin-right: 10px;
        }
        .output {
            font-size: 18px;
            margin-top: 10px;
        }
        .color-box {
            width: 150px;
            height: 150px;
            margin: 20px auto;
            border: 1px solid #000;
        }
    </style>
</head>
<body>
    <h1>Binary Calculator with RGB Conversion</h1>
    <!-- Binary Section 1 -->
    <div class="section">
        <h3>Binary 1 (Red)</h3>
        <input type="text" id="binary1" value="00000000">
        <button onclick="addOne('binary1')">+1</button>
        <button onclick="subtractOne('binary1')">-1</button>
        <div class="output" id="decimal1">Decimal: 0</div>
    </div>
    <!-- Binary Section 2 -->
    <div class="section">
        <h3>Binary 2 (Green)</h3>
        <input type="text" id="binary2" value="00000000">
        <button onclick="addOne('binary2')">+1</button>
        <button onclick="subtractOne('binary2')">-1</button>
        <div class="output" id="decimal2">Decimal: 0</div>
    </div>
    <!-- Binary Section 3 -->
    <div class="section">
        <h3>Binary 3 (Blue)</h3>
        <input type="text" id="binary3" value="00000000">
        <button onclick="addOne('binary3')">+1</button>
        <button onclick="subtractOne('binary3')">-1</button>
        <div class="output" id="decimal3">Decimal: 0</div>
    </div>
    <!-- RGB Color Display -->
    <h3>RGB Color</h3>
    <div class="color-box" id="colorBox"></div>
    <script>
        function addOne(id) {
            let binVal = document.getElementById(id).value;
            let decimal = parseInt(binVal, 2);
            if (decimal < 255) {
                decimal += 1;
            } else {
                decimal = 0; // Handle overflow
            }
            updateBinary(id, decimal);
        }
        function subtractOne(id) {
            let binVal = document.getElementById(id).value;
            let decimal = parseInt(binVal, 2);
            if (decimal > 0) {
                decimal -= 1;
            } else {
                decimal = 255; // Handle underflow
            }
            updateBinary(id, decimal);
        }
        function updateBinary(id, decimal) {
            let binaryValue = decimal.toString(2).padStart(8, '0');
            document.getElementById(id).value = binaryValue;
            // Update corresponding decimal display
            let decimalId = 'decimal' + id.charAt(id.length - 1);
            document.getElementById(decimalId).innerText = "Decimal: " + decimal;
            updateColor();
        }
        function updateColor() {
            // Get decimal values for Red, Green, and Blue
            let red = parseInt(document.getElementById('binary1').value, 2);
            let green = parseInt(document.getElementById('binary2').value, 2);
            let blue = parseInt(document.getElementById('binary3').value, 2);
            // Display color
            let rgb = `rgb(${red}, ${green}, ${blue})`;
            document.getElementById('colorBox').style.backgroundColor = rgb;
        }
        // Initialize color on page load
        updateColor();
    </script>
</body>