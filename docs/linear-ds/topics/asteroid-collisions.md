# Asteroid Collisions

## Problem Summary
You are given an array `asteroids` of integers representing asteroids in a row. For each asteroid, the absolute value represents its size, and the sign represents its direction (positive meaning right, negative meaning left). Each asteroid moves at the same speed.

If two asteroids meet, the smaller one will explode. If both are the same size, both will explode. Two asteroids moving in the same direction will never meet. The task is to find the state of the asteroids after all collisions.

## Intuition
Collisions only occur when a right-moving asteroid (`+`) is followed by a left-moving asteroid (`-`). This follows a Last-In-First-Out (LIFO) pattern because a single left-moving asteroid can collide with multiple right-moving asteroids that appeared before it, starting with the most recent one. A stack is ideal for keeping track of the asteroids moving to the right.

## Approach
1. Use a stack (or a list/vector to avoid reversing at the end) to store the asteroids that haven't exploded.
2. Iterate through each asteroid in the input array:
    - If the asteroid is moving to the **right** (`> 0`), push it onto the stack.
    - If the asteroid is moving to the **left** (`< 0`):
        - Check for collisions with the top of the stack (only if the top is moving right).
        - While the stack top is positive and smaller than the current asteroid's absolute value, the top asteroid explodes (pop the stack).
        - If the stack top is positive and equal to the current asteroid's absolute value, both explode (pop the stack and stop).
        - If the stack top is positive and larger than the current asteroid, the current asteroid explodes (do nothing and move to the next asteroid).
        - If the stack is empty or the top is negative (moving left), the current asteroid survives and is pushed onto the stack.
3. Return the elements in the stack as the final state.

## Complexity
- **Time Complexity**: $O(N)$, where $N$ is the number of asteroids. Each asteroid is pushed and popped at most once.
- **Space Complexity**: $O(N)$ in the worst case to store all asteroids in the stack.

## Pattern
Stack (Collision Simulation)

## Common Mistakes
- **Missing Equal Size Case**: Forgetting that both asteroids explode if they are the same size.
- **Incorrect Collision Condition**: Collisions only happen if the *stack top* is positive and the *current* asteroid is negative. Two negative asteroids or a negative followed by a positive do not collide.
- **Negative Asteroid Survival**: Forgetting that a negative asteroid can survive and be pushed into the stack if there are no positive asteroids left to collide with.

## Related Problems
- [Next Greater Element](./next-greater-element.md)
- [Daily Temperatures](../../04_stack.md)
