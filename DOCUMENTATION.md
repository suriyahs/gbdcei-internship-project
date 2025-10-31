GBDCEI Build-A-Bot: Handover & Technical Documentation

Author: Suriyah Saravanan (Fall 2025 Intern)
Date: October 31, 2025
Project: GBDCEI Build-A-Bot: Python Introduction Game

1. Project Overview

"GBDCEI Build-A-Bot" is a single-page web application designed to teach the absolute fundamentals of Python programming to kids, beginners, and anyone new to coding.

The game is structured as a series of 8 "missions." In each mission, the user is given a simple Python task (like printing a string or creating a variable). When they write and run correct Python code, they are rewarded with a visual animation of a new part being added to their robot, "GBot."

The entire application is contained in a single index.html file. It has no external dependencies other than the Skulpt.js library (for running Python) and a font, which are both loaded via a CDN.

2. Access & Deployment

Live URL: The game is hosted live on GitHub Pages at:
https://suriyahs.github.io/gbdcei-internship-project/

Source Code: The repository is:
https://github.com/suriyahs/gbdcei-internship-project/

Deployment: The site is deployed automatically by GitHub Pages. Any commit pushed to the main branch will be live at the above URL within 1-2 minutes. The index.html file in the root of the repository is the file being served.

3. Core Technologies

HTML5: Used for the core structure of the page (the workbench, text boxes, buttons, etc.)

CSS3: Used for all styling, the retro-tech theme, and all animations (@keyframes for text flicker, robot animations, error shakes, etc.).

JavaScript (ES6+): This is the "brain" of the game. All game logic, level progression, validation, and DOM manipulation are handled in the main <script> block at the bottom of the index.html file.

Skulpt.js: This is the most critical component. It is an in-browser Python interpreter that runs the user's code. It's loaded via a CDN:

<script src="https://cdn.jsdelivr.net/npm/skulpt@1.2.0/dist/skulpt.min.js"></script>

<script src="https://cdn.jsdelivr.net/npm/skulpt@1.2.0/dist/skulpt-stdlib.js"></script>

4. Key Feature Breakdown

The game's logic is primarily contained in the levelsData array and the runCode() function.

The Game Loop: levelsData Array

This is the "config" for the entire game. It's an array of objects, where each object represents one level. This design makes it very easy to add, remove, or modify levels.

A level object has four properties:

story: The HTML string for the mission text.

validate: A JavaScript function that takes the Python output (out) as an argument and must return true or false. This is the core validation logic for each level.

success: The success message to display when the validate function returns true.

visualAction: A JavaScript function that runs when the level is passed (e.g., document.getElementById('robot-body').classList.remove('part-hidden')).

The Code Runner: runCode() Function

This function is called when the "RUN CODE" button is clicked. Here is its order of operations:

Anti-Crash Check (Pre-Validation):

It first checks the user's code against a regular expression (largeNumberRegex) to find dangerously large numbers (e.g., range(1000000)).

If a large number is found, it shows an error and stops, preventing the browser from crashing.

Skulpt Configuration:

It configures Skulpt to set an execution time limit (execLimit: 2000) to catch infinite loops.

It sets the output function to pipe any Python print() statements directly into the #output-area div.

Skulpt Execution:

It runs the user's code using Sk.misceval.asyncToPromise().

Success Callback (Validation):

If the code runs without a Python error, the first .then(function()...) block is executed.

It gets the output, trims it, and checks it against levelsData[currentLevel].validate(output).

On Success: It plays the sound-success sound, runs the visualAction (to build the robot), and increments currentLevel.

On Game Complete: If currentLevel now equals levelsData.length, it plays the sound-final-victory, shows the congratulations message, and disables the controls.

On Wrong Answer: If validate returns false, it plays the sound-error, flashes the UI red, and shows the "That's not quite right" message.

Error Callback (Syntax Errors):

If the code fails to run (e.g., a SyntaxError), the second function(err)... block is executed.

It plays the sound-error and flashes the UI red.

It runs the error string through a series of "kid-friendly" checks (e.g., if (errorString.includes("is not defined"))). This is how we show helpful hints instead of complex technical errors.

Visual & Audio Feedback

Color Flash: When an error occurs, the error-flash class is added to the <body>. When success occurs, success-flash is added. CSS transition properties on the elements make this a smooth fade-to-color. The classes are removed after a short setTimeout.

Audio: Three <audio> tags are in the HTML (sound-success, sound-error, sound-final-victory). Their src attributes contain entire MP3s encoded in Base64, so no external audio files are needed. The .play() method is called on these elements in the JavaScript.

5. How to Manage & Update the Game

This project is very easy to maintain.

How to Add a New Level

Create the Robot Part (HTML/CSS):

In the HTML, add a new element inside #robot-workbench (e.g., <div id="robot-antenna" class="part-hidden"></div>).

In the CSS, add styling for #robot-antenna.

Add the Level to levelsData:

Go to the <script> block and add a new object {...} to the end of the levelsData array.

Fill in the four properties:

story: Write the new mission text (e.g., "Let's add an antenna...").

validate: Write the function (e.g., out => out.trim() === "ANTENNA").

success: Write the success message (e.g., "Antenna attached!").

visualAction: Write the function to show the part (e.g., () => document.getElementById('robot-antenna').classList.remove('part-hidden')).

That's it. The game will automatically pick up the new level.

How to Change Text or Missions:

Simply edit the story or success strings for the corresponding level in the levelsData array.

How to Change the Sounds:

Find a new .mp3 file you want to use.

Use an online "MP3 to Base64" converter.

Copy the entire Base64 string.

In the HTML, find the <audio> tag you want to replace (e.g., id="sound-success").

Paste the new Base64 string into its src attribute, making sure it still starts with data:audio/mpeg;base64,.

How to Update the Live Site:

Just edit the index.html file, commit the changes, and push them to the main branch on GitHub. The live site will update in about 1-2 minutes.