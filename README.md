# sudoku

[Visit project here](https://sarthakagarwal22.github.io/sudoku/index.html)

This is a simple project built in Vue.js . It has a hard coded sudoku puzzle, with all the standard rules. 
If you enter an incorrect number, the alert box tells you, whether its a row error, a column error, or a 3x3 Grid error.
There is a counter, that keeps track of your incorrect move and if you cross a limit, set by you, then it locks the puzzle down.

You can either reset maximum no. of error you wish to make, or reset the entire puzzle.
If you enter a correct number, it will be immediately flagged with a red background and once you complete the entire puzzle, 
correctly, it shows a well done message, along with the time taken.

There are two components, one the sudoku board being the parent, and the timer being the child. Once the puzzle is complete, we use the ```$ref```, to call a function of the child class, that gives us the time taken and resets the timer.

The program is highly modular and made using best coding practices, keeping in mind, readablitiy of code and optimization.

