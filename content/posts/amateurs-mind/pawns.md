---
weight: 3
title: "Pawn Structure"
date: 2021-01-24T23:04:49-08:00
draft: false
categories: ["bookstudy"]
tags: ["positional", "pawn"]
description: "This is about the chapter the confusing subject of pawn structure."
books: ["amateurs-mind"]
---

Knowning which pawn structure is "weak" is not enough: one need to know how to attack them, or making use 
of the weaknesses.

In general, the formular to attack weak pawns(isolated, backward) is to control the square in front of 
the pawn so it can't advance. (the squares are usually weak since no pawn can defend it). Then pile up 
on the weakness.

Every structure has its pros and cons.

## General Rules

### Double pawns

{{< admonition type=success title="Positive" open=true >}}
Extra open files, and increased square control.(especially on center files.)
{{< /admonition >}}

{{< admonition type=danger title="Negative" open=true >}}
Reduced flexibility, and vulnerable to attack. (the leading pawn is usually weaker)
{{< /admonition >}}

Example: In the following position, white is happy for a Bishop trade and double the pawn on e-file. Black should play Bb6, welcoming exchange and open a-file as well.

{{< fen-diag fen="r1bqk2r/ppp2ppp/2np1n2/2b1p3/2B1P3/2NPBN2/PPP2PPP/R2Q1RK1 b" flip="" caption="After Be3, white invities Black to trade to open the f-file and double pawns on e-file." >}}

### Isolated Pawns


{{< admonition type=success title="Positive" open=true >}}
* The creation of such pawn provides half-open file to use.
* Central isolated pawns provide central control and open file for Rooks. Owner of such pawns should play dynamically.
{{< /admonition >}}

{{< admonition type=danger title="Negative" open=true >}}
* Cannot be protected by other pawns, vulnerable if on an open file.
* The formular to attack isolated pawns is to **fixate** it, by controlling the weak square in front of it. Trade all minor pieces, pile up Rooks and Queens on it to pin the pawn, then attack with a friendly pawn.
{{< /admonition >}}


Example on how to attack isolated pawn with R+Q
{{< fen-diag fen="6k1/p2q1p2/1p2p1p1/3r3p/3P3P/P1Q3P1/1P3P2/3R2K1 b" flip="" caption="Black can win the fixed and isolated d-pawn by 1...e5." >}}

If you have an isolated pawn, try to exchange Rooks! Then the enemey won't have enough forces to win the pawn.


### Backward pawns

{{< admonition type=success title="Positive" open=true >}}
* It guards other pawns. The advanced pawn can block enemy pieces and control squares. 
* The backward pawn is not weak if the square in front of it is well-defended.
{{< /admonition >}}

{{< admonition type=danger title="Negative" open=true >}}
* It's weak if it's sitting on a open file and cannot advance. Attack should control the square directly in
front of the pawn, turning the pawn into an immobile target. One way is to exchange its defenders.
{{< /admonition >}}

Example: Square in front of the backward pawn as well as the pawn is **well-defended**, so the pawn is 
not a weakness.

{{< fen-diag fen="2rr2k1/1bqnbppp/p2p1n2/1p2p3/4P3/1PN2N2/PBPQ1PPP/2RR1BK1 w" flip="" caption="White to play and has nothing. d6 is not weak because d5 is controlled well by Black." >}}

### Hanging pawns

Haning pawns is a pair of pawns on same rank on c/d files(or e-f files). 

It can be weak if pawns are immobile and attacked. It can be strong since it provides control of 
central squares and space.

The side has hainging pawns should play dynamically and protect them.

### Passed pawns

A passed pawn is very strong **if the owner has play elsewhere**: it can be used as endgame insurance.

The square *in front of the passed pawn* is the most important square on the board. Whoever controls the square dominates the game.


## Games

### 1800-Silman: Black uses hanging pawns for dynamic potential.

{{< admonition type=note title="Tips" open=true >}}
* Any kind of weak pawn must first be contained(blocked) before it's attacked
* Don't just react to opponent's plans. Find an idea and follow it yourself.
{{< /admonition >}}

{{< lichess-embed src="https://lichess.org/study/noJstzHr/0YPuWqa7#26" >}}

### Triple pawn gives victory

In this example, White has triple pawn but it gives control of squares, also provides spaces and open 
files that white can use. 

{{< lichess-embed src="https://lichess.org/study/noJstzHr/ZSq4g54E#0" >}}

### Backward pawn: fight for square in front of it!

In this example, black has a backward pawn on d6, and he should fight for d5(in front of the pawn). 
However, he traded away his light square bishop, losing an important defender of that square, and 
allowed white to dominate d5.

{{< admonition type=note title="Tips" open=true >}}
* The weak square in front of a backward pawn is often a greater problem than the pawn itself.
* You can play to win a square by trading off its defenders.
{{< /admonition >}}

{{< lichess-embed src="https://lichess.org/study/noJstzHr/OY2qEsrh#1" >}}

### 1650-Silman: Pawn structure and close/open position choice.


In this example, white has choice to close or open the position, and he closed it incorrectly, just to kick
the black knight (to a not-so-bad position). Then with center closed, white should play on the wing, but he didn't.

{{< admonition type=note title="Tips" open=true >}}
* In **closed positions** you play where your pawns point to. (less important in open positions)
* Identify a target, build up your pieces to attack it. Don't just react to your opponent's plan.
* Don't attack something for no reason. Your opponent always sees it. Only attack if the resulted 
position is better for you.
{{< /admonition >}}

{{< lichess-embed src="https://lichess.org/study/noJstzHr/qdS2O6iw#18" >}}