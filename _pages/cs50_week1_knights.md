---
title: "CS50-AI Week 1. Project 1: Knights"
permalink: /_pages/cs50/week1-knights
tags:
  - cs50
  - Python
toc: true
---

```python
from logic import *

AKnight = Symbol("A is a Knight")
AKnave = Symbol("A is a Knave")

BKnight = Symbol("B is a Knight")
BKnave = Symbol("B is a Knave")

CKnight = Symbol("C is a Knight")
CKnave = Symbol("C is a Knave")

# Puzzle 0
# A says "I am both a knight and a knave."
knowledge0 = And(
    # TODO
    
    Or(AKnave, AKnight),
    Not(And(AKnave, AKnight)),
    Or(BKnave, BKnight),
    Not(And(BKnave, BKnight)),
    Or(CKnave, CKnight),
    Not(And(CKnave, CKnight)),    
    
    # A is a knave
    Implication(AKnave, Not(And(AKnight, AKnave))),
    Implication(AKnight, And(AKnight, AKnave)),

)

# Puzzle 1
knowledge1 = And(
    # TODO
    
    Or(AKnave, AKnight),
    Not(And(AKnave, AKnight)),
    Or(BKnave, BKnight),
    Not(And(BKnave, BKnight)),
    Or(CKnave, CKnight),
    Not(And(CKnave, CKnight)),
    
    # A says "We are both knaves." -> A is a Knave
    # B says nothing -> B is a knight
    Biconditional(AKnight, And(AKnave, BKnave)),
    

)

# Puzzle 2
knowledge2 = And(
    # TODO
    Or(AKnave, AKnight),
    Not(And(AKnave, AKnight)),
    Or(BKnave, BKnight),
    Not(And(BKnave, BKnight)),
    Or(CKnave, CKnight),
    Not(And(CKnave, CKnight)),
    
    # A says "We are the same kind." -> A is a Knave
    # B says "We are of different kinds." -> B is a Knight
    
    # A says A is knight -> BOTH A and B are knights OR both A and B are knaves
    Implication(AKnight, Or(And(AKnight, BKnight), And(AKnave, BKnave))),
    
    # A says A is a knave
    Implication(AKnave, Not(Or(And(AKnight, BKnight), And(AKnave, BKnave)))),
    
    # B says B is a knight -> either A is a knight and B a knave or A is a knave and B a knight
    Implication(BKnight, Or(And(AKnight, BKnave), And(AKnave, BKnight))),
    
    # B says B is a knave
    Implication(BKnave, Not(Or(And(AKnight, BKnave), And(AKnave, BKnight)))),
    
)

# Puzzle 3
knowledge3 = And(
    # TODO
    Or(AKnave, AKnight),
    Not(And(AKnave, AKnight)),
    Or(BKnave, BKnight),
    Not(And(BKnave, BKnight)),
    Or(CKnave, CKnight),
    Not(And(CKnave, CKnight)),
    
    # A says either "I am a knight." or "I am a knave.", but you don't know which.
    # If A says "I am a knight"
    Implication(AKnight, Or(AKnight, AKnave)),
    # If A says "I am a knave"
    Implication(AKnave, Not(Or(AKnight, AKnave))),
    
    # B says "A said 'I am a knave'."
    # If B is a knight
    Implication(BKnight, Implication(AKnight, BKnave)),
    # If B is a knave
    Implication(BKnave, Implication(AKnave, Not(BKnave))),
    
    # B says "C is a knave."
    Implication(BKnight, CKnave),
    Implication(BKnave, Not(CKnave)),
    
    # C says "A is a knight."    
    Implication(CKnight, AKnight),
    Implication(CKnave, Not(AKnight)),
    
)


def main():
    symbols = [AKnight, AKnave, BKnight, BKnave, CKnight, CKnave]
    puzzles = [
        ("Puzzle 0", knowledge0),
        ("Puzzle 1", knowledge1),
        ("Puzzle 2", knowledge2),
        ("Puzzle 3", knowledge3)
    ]
    for puzzle, knowledge in puzzles:
        print(puzzle)
        if len(knowledge.conjuncts) == 0:
            print("    Not yet implemented.")
        else:
            for symbol in symbols:
                if model_check(knowledge, symbol):
                    print(f"    {symbol}")


if __name__ == "__main__":
    main()
```
