---
layout: post
title: "Hog project"
excerpt: "A cs61a projects"
categories: articles
tags: [python, project]
comments: true
share: true
---
It's been a long time since I posted the last blog on here. It's been pretty busy this semester cause I selected three tough courses, which took me a lot of time.

One of them is cs61a. cs61a is a very interesting course and it's completely free to join online. You can find its webcast on youtube to learn more about programming. cs61a contains a lot of interesting and fun homeworks and projects, which are mainly coded in Python. Even I already have much experience in coding in python, it still took me some time to complete its homework and lab and projects.

One of those projects is the Hog. It's a tiny project but an interesting one and good for practice. It's mainly about coding a game and find your strategy to win this game.

## Introduction
In this project, you will develop a simulator and multiple strategies for the dice game Hog. You will need to use control statements and higher-order functions together, as described in Sections 1.2 through 1.6 of Composing Programs.

In Hog, two players alternate turns trying to be the first to end a turn with at least 100 total points. On each turn, the current player chooses some number of dice to roll, up to 10. That player's score for the turn is the sum of the dice outcomes.

To spice up the game, we will play with some special rules:

* **Pig Out**. If any of the dice outcomes is a 1, the current player's score for the turn is 0.
* **Piggy Back**. When the current player scores 0, the opposing player receives points equal to the number of dice rolled that turn.
 * Example: If the current player rolls 3 dice that come up 1, 5, and 1, then the current player scores 0 and the opponent scores 3.
* **Free Bacon**. A player who chooses to roll zero dice scores one more than the largest digit in the opponent's total score. 
 * Example 1: If the opponent has 42 points, the current player gains 1 + max(4, 2) = 5 points by rolling zero dice.
 * Example 2: If the opponent has has 48 points, the current player gains 1 + max(4, 8) = 9 points by rolling zero dice.
 * Example 3: If the opponent has has 7 points, the current player gains 1 + max(0, 7) = 8 points by rolling zero dice.
* **Hog Wild**. If the sum of both players' total scores is a multiple of seven (e.g., 14, 21, 35), then the current player rolls four-sided dice instead of the usual six-sided dice.
* Hogtimus Prime. If a player's score for the turn is a prime number, then the turn score is increased to the next largest prime number. For example, if the dice outcomes sum to 19, the current player scores 23 points for the turn. This boost only applies to the current player. Note: 1 is not a prime number!
* **Swine Swap**. After the turn score is added, if the last two digits of each player's score are the reverse of each other, the players swap total scores.
 * Example 1: The current player has a total score of 13 and the opponent has 91. The current player rolls two dice that total 6. The last two digits of the current player's new total score (19) are the reverse of the opponent's score (91). These scores are swapped! The current player now has 91 points and the opponent has 19. The turn ends.
 * Example 2: The current player has 66 and the opponent has 8. The current player rolls four dice that total 14, leaving the current player with 80. The reverse of 80 is 08, the opponent's score. After the swap, the current player has 8 and the opponent 80. The turn ends.
 * Example 3: Both players have 90. The current player rolls 7 dice that total 17, a prime that is boosted to 19 points for the turn. The current player has 109 and the opponent has 90. The last two digits 09 and 90 are the reverse of each other, so the scores are swapped. The opponent ends the turn with 109 and wins the game

## Download starter files
To get started, download all of the project code as a [zip file](https://github.com/ryanyuan42/python_code/blob/master/cs61a/hog.zip). You only have to make changes to hog.py.

```hog.py```: A starter implementation of Hog
```dice.py```: Functions for rolling dice
```hog_gui.py```: A graphical user interface for Hog
```ucb.py```: Utility functions for CS 61A
```ok```: CS 61A autograder
```tests```: A directory of tests used by ok
```images```: A directory of images used by hog_gui.py

 
 The above are breif introductions to the Hog project. If you want to find more details, go to [Hog](http://cs61a.org/proj/hog/). If you are not enrolled in cs61a, you are not able to use the ok file. But you can still try to complete the project and play the game in the end. The zip file provide you with a GUI python file.
 
 My code can be found in [here](https://github.com/ryanyuan42/python_code/tree/master/cs61a/projects/hog). Final strategy achieves 82.28% win rate. Try to defeat me!
