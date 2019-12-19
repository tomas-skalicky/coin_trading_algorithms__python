# Summary

This script served to evaluate trading algorithm based on broken support bases.
The algorithm should buy cheap when other investors are in panic
and hence they are selling and the algorithm should sell higher, but still
below the broken support base.

The motivation to write this script was one of the videos of Quickfingers Luc
(such as https://www.youtube.com/watch?v=vgcFe_XO_LQ).

NOTE: This is a proof of concept, not a script used for a real trading.

# Environment preparation

Necessary python3 packages:
- jupyter
- pandas
- PyFunctional

You can install them via

`python3 -m pip install --user jupyter pandas PyFunctional`

# Execution

1. Execute `jupyter notebook`
1. Open `support_and_resistance__tomas-checkpoint.ipynb` in the started Jupyter
Notebook editor.
1. Click on "Run" for each code snippet, starting with the first code snippet
and following with the second one, third one, etc.

NOTE 1: The last code snippet contains the function call; either
`currency_pair_evaluator.evaluate_one_time_frame_and_draw_and_close(..)` or
`currency_pair_evaluator.evaluate_multiple_time_frames_and_draw_and_close(..)`.

NOTE 2: This script is not used for a real trading, hence no secrets are
necessary.

# Algorithm

## Dictionary

Assume that a strong support/resistance base is being broken
the first time. The graph of the coin is "healthy" if the price comes back
to the base price in a close future and this happens for every strong base.

NOTE: The price can start falling/rising again just after the price has come
back to the broken support/resistance base.

## Theory of the algorithm

When a strong support base is being broken in the health graph the first time,
the script waits till the price has fallen enough and then starts buying.
If the price continues falling, the script continues buying, but more than
before. Since the script operates on the health graph, the price needs to
come back to the broken support base in the close future. The script sells all
coins even below the base price to reduce risk caused by other bots.

## Challenges

The theory contains expressions which needs to precisely specified:
 
1. What means that a base is strong?
1. When should the script start buying?
1. How much should the script buy?
1. When should the script sell coins?
1. How long can the comeback to the price take?
1. What should the script do when the comeback seems to not come?

## Implementation

### What means that a base is strong?



TODO Picture

# Script Customization

TODO Picture
