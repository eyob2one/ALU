# 4-Bit Digital ALU Project - Display Controller Module

## Project Overview
This module is responsible for taking the outputs from the **Adder/Subtractor** and **Magnitude Comparator** units and decoding them for a single **7-segment display**. As per the project requirements, the display must handle arithmetic results (including negative magnitudes), comparison signs, and overflow errors using a priority-based selection method.

## Current Progress
The **Arithmetic Decoder** (Binary to 7-Segment) has been partially implemented. The logic gates and wiring for **Segments a, b, and c** are complete based on the truth table for inputs 0-8.

## To-Do: 1. Complete the Arithmetic Decoder
We need to implement the Boolean expressions for the remaining segments (d, e, f, and g). These expressions are minimized for the range **0000 (0) to 1000 (8)**.

### Expressions to Implement:
* **Segment d**: $D_3 + (\overline{D_2} \cdot \overline{D_0}) + (D_1 \cdot \overline{D_0}) + (\overline{D_2} \cdot D_1) + (D_2 \cdot \overline{D_1} \cdot D_0)$
* **Segment e**: $(\overline{D_2} \cdot \overline{D_0}) + (D_1 \cdot \overline{D_0})$
* **Segment f**: $D_3 + (\overline{D_1} \cdot \overline{D_0}) + (D_2 \cdot \overline{D_1}) + (D_2 \cdot \overline{D_0})$
* **Segment g**: $D_3 + (D_2 \cdot \overline{D_1}) + (\overline{D_2} \cdot D_1) + (D_1 \cdot \overline{D_0})$

## To-Do: 2. Build the Comparison & Overflow Patterns
Instead of a complex decoder, we use hardwired bit patterns for these modes:

### Comparison Mode ($S_1 = 1$)
Map these directly to the multiplexer inputs:
* **$A > B$** $\rightarrow$ Segment **a** (Top bar)
* **$A = B$** $\rightarrow$ Segment **g** (Middle bar)
* **$A < B$** $\rightarrow$ Segment **d** (Bottom bar)
* All other segments ($b, c, e, f$) must be grounded (0).

### Overflow Mode ("E" Indicator)
When the **Overflow ($V$)** signal is high, the display must show an **'E'**:
* **Segments a, d, e, f, g** $\rightarrow$ Set to High (1).
* **Segments b, c** $\rightarrow$ Set to Low (0).

## To-Do: 3. Final Selection Logic (The Multiplexer)
We are using a **7-bit 4-to-1 Multiplexer** strategy to manage the display priority.

### MUX Selection Mapping:
The selection lines are driven by **Overflow ($V$)** and **Mode ($S_1$)**.

| Selection ($V, S_1$) | Input Selected | Logic Branch |
| :--- | :--- | :--- |
| **00** | Input 0 | Arithmetic Result (Magnitude 0-8) |
| **01** | Input 1 | Comparison Signs (Top/Mid/Bot) |
| **10 / 11** | Input 2 / 3 | Overflow Error ('E' Override) |

### Integration Steps:
1.  **Magnitude Selection**: Before the decoder, use a 4-bit MUX controlled by the ALU result MSB ($R_3$). If $R_3=1$, select the output of the `TWOs_Complementer`; otherwise, pass the raw result.
2.  **Negative Indicator**: Wire the ALU result MSB ($R_3$) directly to a standalone LED next to the 7-segment display.
3.  **Final Output**: Connect the 7 outputs of the 4-to-1 MUX to the pins of the 7-segment display.

## Verification Checklist
* Check that $7 + 1$ displays **'E'**.
* Check that $2 - 5$ displays **'3'** and lights the **Negative LED**.
* Check that $S_1=1$ correctly shows the comparison bars based on $A$ and $B$ values.
