> [!question]- 1. In a given queue, with a size of 7, and values of 10,14,32,55 (from q-front) the following operations are performed. Determine the value of q-front and q-rear at the end.
> 1. Enqueue (36)
> 2. Enqueue (40)
> 3. Dequeue
> 4. Enqueue (30)
> 5. Enqueue (23)
> 6. Enqueue (39)
> 7. Enqueue (52)
> 8. Dequeue
> **Assumptions:** the queue is an array of size 7 (indices 0-6), and the initial values 10, 14, 32, 55 sit at indices 0-3. So front = 0 and rear = 3 at the start.

| Step  | Operation            | Queue contents (front → rear)                   | front | rear |
| ----- | -------------------- | ----------------------------------------------- | ----- | ---- |
| Start |                      | 10, 14, 32, 55                                  | 0     | 3    |
| 1     | Enqueue(36)          | 10, 14, 32, 55, 36                              | 0     | 4    |
| 2     | Enqueue(40)          | 10, 14, 32, 55, 36, 40                          | 0     | 5    |
| 3     | Dequeue (removes 10) | 14, 32, 55, 36, 40                              | 1     | 5    |
| 4     | Enqueue(30)          | 14, 32, 55, 36, 40, 30                          | 1     | 6    |
| 5     | Enqueue(23)          | **Overflow**: rear is at index 6, the last slot | 1     | 6    |
| 6     | Enqueue(39)          | **Overflow**                                    | 1     | 6    |
| 7     | Enqueue(52)          | **Overflow**                                    | 1     | 6    |
| 8     | Dequeue (removes 14) | 32, 55, 36, 40, 30                              | 2     | 6    |

**Final answer (linear queue): front = 2 and rear = 6** (0-indexed). The front element is 32 and the rear element is 30. In 1-indexed terms, that is front = 3 and rear = 7.

**If it is a circular queue:** the queue fills up when 23 is enqueued, since the rear wraps to index 0 and all 7 slots are in use. Enqueue(39) and Enqueue(52) then overflow, and the last Dequeue removes 14. The result is **front = 2 and rear = 0**.


