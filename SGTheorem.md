# The Waiting Move
## The Simplest One
What is the waiting move? Let me show you by a simple example:

There are $n$ numbers, and they are $1, 2, 3, ..., n$ from left to right. You can your friend take turn to remove one number and remove all the divisors. For example, if you remove $6$, the $1, 2 ,3$ will be removed too. The one who can not move will lose.

In this example, the first player will always win. We can prove this by the proof by contradiction, suppose there is a $n$ that is a losing state, which means removing any number will be winning state. Now we remove the number $1$. And now its the second player's turn, because current state is winning state, the second player can remove one number and reach a losing statement. But we can note that no matter which number the second player remove, the state is same with the first playing removing that number at beginning. And the state is winning and losing state at same time, which is contradictory. So the supposition is not correct. Therefore the first player will always win.

In this case, removing number $1$ is the waiting move.

## The Tree
There is a tree with $n$ nodes. You and your friend take turn to choose a node and mark all the nodes from the chosen nodes to the root. When all the nodes are marked the game is over. The one who can not move will lose.

In this case marking the root is the waiting move, so the first player will always win.

## The Chocolate
There is a chocolate whose shape is a $n \times m$ rectangle. You and your friend take turn to chose a piece of chocolate, and eat all the pieces that are in the rectangle whose right top corner is the chosen piece, and the bottom left corner is the bottom left corner of the chocolate. The one who eat the last piece will lose.

In this case eating the left bottom piece is the waiting move, so the first player will always win.

I've give you three examples of games with a waiting move which can be took advantages by the first player to win the game. But you may ask how the first player can win? What is the concrete strategy? Actually, only the smartest one in playing games can find the strategy, obvious is the fact that I am not the one.

# The SG Theorem
## The Mex
The `Mex` is a function whose domain of definition are sets consists of natural numbers. The `Mex` of a set is the smallest natural number that is not in the set. For example, the `Mex` of $\{0, 1, 2, 4\}$ is $3$.

With this we can converting any game to a `DAG` and solve the game by calculation the `SG` value.

## The Conversion
Actually, any game are a state machine. We can find all the states that a game contains, and find all the states that one states can reach, and then we can get a `DAG` which is the state machine of the game.

With this graph, we define the `SG` value of a state as the `Mex` of the `SG` values of all the states that can be reached from the current state. And the `SG` value of the final state is $0$. We have the statement that the first player will win if the `SG` value of the initial state is not $0$. The proof of this is very simple, we just need prove the following claims:
* claim 1: If the `SG` value of a state is $0$, we can not reach a state whose `SG` value is $0$.
* claim 2: If the `SG` value of a state is not $0$, we can reach a state whose `SG` value is $0$.

The proofs of the two claims are simple, so I'm not going to prove them. But I recommend you prove them by yourself.

## The SG Theorem
The `SG` theorem is for solving the game with multiple sub-games. For this game, when the last sub-game is over, the game is over, and the one who can not move will lose.

The `SG` theorem gives the statement below:
> The `SG` value of a state is the `XOR` of the `SG` values of all the sub-games.

Let me explain why this is true. We must know that for every state whose `SG` value is $k$, we can reach $k$ states whose `SG` value are $0, 1, 2, ..., k-1$ separately. For every move, we actually find one sub-game and change the `SG` value of the chosen sub-game. This is same with remove some stones from a pile. But you may ask how about a move make the `SG` value of the sub-game greater than $k$? Yes, this is possible, but this really does not matter. Let explain this for you by a simple way:

Consider that one in a winning state, he will not make the `SG` value of a sub-game greater than $k$, because he or she know that there is a move that can make the `Nim Sum` 0, so he will try this one. For the one in a losing state, he or she may consider take a move that make the `SG` value of a sub-game greater than $k$. But for this, the opposite can make the `SG` value be $k$ actually. Therefore when this is no move make the `SG` value greater, the one has to move like removing stones from a pile. You may prove this by a formal one if you like. Bur for most, this simple one is enough.

## The Chain Game
### Description
There is a chain with $n$ nodes. You and your friend take turn to remove a node connected to any one having been removed or remove two connected nodes meeting any of them connected to a removed one. The one who can not move will lose.

For example, if the chain's length is $5$, at first, there is no node removed, the first player can remove any one. Let the first player remove the third node. Now the chain is $1, 2, 3(removed), 4, 5$, then the second player can remove the one connected to the moved one, so $2$ or $4$ can be removed, or remove two connected nodes meeting any of them connected to a removed one, so $(1, 2)$ ($2$ is connected with $3$) or $(4, 5)$ ($4$ is connected with $3$) can be removed at same time.

### Solution
Try to prove this by the `SG` theorem. The proof will come soon.
