# Chess Question

## Prerequisite
* Java 14
* [Gadle 6.3+](https://gradle.org/install/)

## Abstract
You have been provided with a third-party library `ChessLib` which calculates the legal moves a knight can make given a position on an ![8\times8](https://latex.codecogs.com/svg.latex?8%5Ctimes8) board. 
提供了一个三方库用于 **骑士**的在一个8 × 8 = 64大小的棋盘上移动。 
换句话来说就是骑士不用你管。
The library has been used to create a program which moves a knight randomly around a board, given an initial starting position and a total number of moves to make.
给予一个初始化的值和一个总的移动次数， 然后骑士实际移动指定的次数。

## Problem
Extend this program to set up an 8 x 8 square game board containing several different pieces in predefined positions（预定义位置）.
要你拓展这个已经拥有简易版本的游戏，实现另外两种类型棋子的业务逻辑。
For each move of the game, the program will choose a piece at random, and move it to a randomly selected valid position.（每次移动，游戏会随机选择一个棋子，然后移动它到一个随机的、有效的位置。）
简单来说就是建立一个棋盘游戏，除了骑士之外还有主教和皇后。他们可以移动指定的位置。

You are not allowed to change any of the `ChessLib` code.
不允许修改库代码；

Extend the program as required. 

Use Object Oriented Design and Modeling appropriately for extensibility.
oop以及可拓展性。 这个我可不清楚。

可拓展： 国际象棋不止这几种棋子的类型，你要实现可拓展； 不知道是用接口还是抽象类
游戏规则是：
## Game Rules

* Only one piece can occupy any position on the board at a given time. 应该就是皇后
  只有一种棋子可以占据棋盘上面的任何位置。 -> 修正： 最终棋盘上应该只有一种棋子。 
* All pieces can “jump” any occupied position. 
所有的方式都可以“跳转到”已经被占领的位置。 -> 修正： 这个就是所谓的吃子。

*__Note__*: Although the game bears a striking resemblance to Chess, this is entirely coincidental. Do not assume other chess rules apply.
这是一个自定义游戏。 尽管他跟国际象棋很像。

## Game Pieces to support:
* Knight - Moves as implemented by ChessLib： 骑士的移动由第三方库实现。
* Bishop - Moves diagonally, any distance within board boundaries 主教在对角线上移动。
* Queen – Moves diagonally, horizontally or vertically, any distance within board boundaries。 皇后在任意方向移动。 

# *Important*
## What you should do?
* Implement your business logic into the file `src/main/java/chessGame/ComplexGame.java`. 在这个文件里面实现业务逻辑。 If you want to add some auxiliary classes, 如果要添加工具类 please insert into the package `chessGame` 放在这个包的里面.
* Write your unit tests into the folder `src/test/java/chessGame`. 需要写单元测试。
* If you want to test your application如果要测试你的应用, you can modify `src/main/java/chessGame/App.java` to print the output. 需要在app里面进行输出；

## About Gradle
Gradle is a build tool for compiling & testing the application. The following functions may be helpful to you.

**gradle check**: Run all unit tests.

**gradle run**: Run the main application.

**gradle tasks**: Print all the supported tasks.

上面介绍了一些关于gradle的用法。