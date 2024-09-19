---
layout: post
title: About
permalink: /about/
comments: true
---

### Where am I from?

 This is where I am from and where my parents are also from. California is a great place to grow up, with its amazing weather and diverse culture. My paternal grandparents are from San Diego and Oklahoma. And my maternal grandparents are from Guayana and England.

<style>
    /* CHATGPT HELPED WITH THIS */
    /* Style looks pretty compact, 
       - grid-container and grid-item are referenced the code 
    */
    .grid-container {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(150px, 1fr)); /* Dynamic columns */
        gap: 10px;
    }
    .grid-item {
        text-align: center;
    }
    .grid-item img {
        width: 100%;
        height: 100px; /* Fixed height for uniformity */
        object-fit: contain; /* Ensure the image fits within the fixed height */
    }
    .grid-item p {
        margin: 5px 0; /* Add some margin for spacing */
    }

    .image-gallery {
        display: flex;
        flex-wrap: nowrap;
        overflow-x: auto;
        gap: 10px;
        }

    .image-gallery img {
        max-height: 150px;
        object-fit: cover;
        border-radius: 5px;
    }
</style>

<!-- This grid_container class is used by CSS styling and the id is used by JavaScript connection -->
<div class="grid-container" id="grid_container">
    <!-- content will be added here by JavaScript -->
</div>

<script>
    // 1. Make a connection to the HTML container defined in the HTML div
    var container = document.getElementById("grid_container"); // This container connects to the HTML div

    // 2. Define a JavaScript object for our http source and our data rows for the place grid
    var http_source = "https://upload.wikimedia.org/wikipedia/commons/";
    var place = [
        {"flag": "0/01/Flag_of_California.svg", "description": "California"},
        {"flag": "1/19/Flag_of_San_Diego%2C_California.svg", "description": "San Diego"},
        {"flag": "thumb/6/6e/Flag_of_Oklahoma.svg/640px-Flag_of_Oklahoma.svg.png", "description": "Oklahoma"},
        {"flag": "thumb/b/bc/Flag_of_Guyana_%28fringed%29.svg/640px-Flag_of_Guyana_%28fringed%29.svg.png", "description": "Guyana"},
        {"flag": "thumb/b/be/Flag_of_England.svg/640px-Flag_of_England.svg.png", "description": "England"}
    ];

    // 3a. Consider how to update style count for size of container
    // The grid-template-columns has been defined as dynamic with auto-fill and minmax

    // 3b. Build grid items inside of our container for each row of data
    for (const location of place) {
        // Create a "div" with "class grid-item" for each row
        var gridItem = document.createElement("div");
        gridItem.className = "grid-item";  // This class name connects the gridItem to the CSS style elements
        // Add "img" HTML tag for the flag
        var img = document.createElement("img");
        img.src = http_source + location.flag; // concatenate the source and flag
        img.alt = location.flag + " Flag"; // add alt text for accessibility

        // Add "p" HTML tag for the description
        var description = document.createElement("p");
        description.textContent = location.description; // extract the description


        // Append img and p HTML tags to the grid item DIV
        gridItem.appendChild(img);
        gridItem.appendChild(description);

        // Append the grid item DIV to the container DIV
        container.appendChild(gridItem);
    }
</script>

### What's Happening This Year?
- 🏫 Attended Del Norte High School, currently a junior
- 🎮 Big fan of video games and enjoy playing with friends
- 💻 Interested in computer science, especially CSP class
- 📚 Taking APUSH, Calc AB, Marketing Econ, and Amlit classes this year

### Who am I?
<html>
    <body>
        <p>Hey, I'm Gavin Copley. I am a junior at <span style="color: blue;">Del </span><span style="color: green;">Norte</span>
        High School. I am 16. I like to play video games and spend time with friends. I also enjoy learning about new tech devices, apps, etc. I enjoy computer science and CSP is probably my favorite class this year. My classes are: CSP, APUSH, Calc AB, Marketing Econ, and Amlit. I am excited to start this new school year and am optimistic towards what this year holds.
        </p>
    </body>
</html>
<br>
<br>
<a href="{{site.baseurl}}/README4YML.html">Click for README</a>