
# Wonderful Life

A playground for Conway's Life implementations.

## Basics

In case you do not remember, here are the rules:

1. On a rectangular grid we place some living cells.
2. We start iterations, building a new grid such that:
  - if the cell is alive and the sum of the adjacent cells is 1 or 0, the cell dies of loneliness.
  - if the cell is alive and the sum of the adjacent cells is more than 3, the cell dies of overcrowding.
  - if the cell is alive and has 2 or 3 neighbors, it stays alive.
  - if an empty cell has exctly 3 neighbors, a new living cell is created there.

## Beyond the basics.

One of the far-reaching goals is to have a fully-automatic selection of configurations that exhibit
some artistically-rich behaviours. For example, a travelling patterns, or cyclical non-trivial objects, etc.

Another modification of the classic rules is to have 1D or 3D cellular automata.

## Data Formats

### Canonical form 3.0

The different implementations I wrote had different quick-and-dirty ways of defining the grid.
It is very useful to have some canonical form that would help identifying the similar configurations.

Hereby I am defining a new one, version 3.0. Here is how it is constructed:

Let's take a field of width W and height H:

```
   ^ ...........
   | ..X..X.....
H  | ..XX.......      Figure (1)
   | ..XX.......
   v ...........
     <--- W -->
```

Step 1. Disregard the all-zero columns on the left and on the bottom:

We removed DeltaW columns on the left and DeltaH rows on the bottom.
New grid size is W1 x H1 :


```
   ^ X..X
   | XX..         Figure (2)
H1 v XX..
   <- W1 ->
```

Step 2. Determine the center of masses, make it as stable as possible.

Compare if the formation is more 'vertical' or more 'horizontal'.
Make it so its center of masses is on the bottom left octogrant, rotating or flipping if necessary:

Rotate Left 90 degrees

```
   ^ X..
   | ...
   | .XX         Figure (3)
H2 v XXX
   <- W2 ->
```

No flipping needed, because the center of mass is at (2, 11/6) :

6 * Cx = 1 + 1 + 2 + 2 + 3 + 3
Cx = 2

6 * Cy = 1 + 1 + 1 + 2 + 2 + 4
Cx = 11/6

So, Cx >= Cy, and Cx - 1/2 <= W2/2


Step 3. Traverse into a bitmap

And traverse in a zig-zag manner from the left-top corner, collecting bits 1 or 0 depending on the cell alive or dead:

```
| 11 
| |  \
| 10  12 
|   \    \
| 4   9   13             Figure (4)
| | \   \   \
| 3   5   8   14
|   \   \   \   \ 
v 1 - 2   6 - 7  15-...
  <------- W --------->
```

So, from the least significant bit, the bitmap would be 1 1 0 0 1 1 0 1 0 1 ...

That is number 1010110011, that is 0x2b3


Step 4. Combining all the transformations together.

So, the configuration on Figure(1) is identifies as {(0x2b3, (R,1), (2,1) }



