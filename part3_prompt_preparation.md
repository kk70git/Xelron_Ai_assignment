# Part 3: Prompt Preparation - Task 3.1

## 3.1.1 Repository Context
`aiokafka` is an essential tool in the Python data engineering ecosystem. It provides the asynchronous "plumbing" required for applications to communicate with Apache Kafka without blocking. Its target users are software architects and data engineers building real-time pipelines. The domain is **Distributed Systems**, specifically focusing on high-throughput messaging.

## 3.1.2 Pull Request Description
This PR implements **Idempotency** for the startup sequence. The current behavior allows the `start()` coroutine to be re-entered while already running, which is a dangerous anti-pattern in asynchronous networking. The new behavior will guard the startup logic, ensuring it only executes once per object lifecycle.

## 3.1.3 Acceptance Criteria
✓ `AIOKafkaProducer.start()` must return immediately if the producer is already active.
✓ If `start()` is called concurrently, only one task should execute the initialization.
✓ The internal state must be tracked via a private variable (e.g., `_started`).
✓ All existing tests must pass without modification to the public API.
✓ Implementation must handle partial failures (resetting the state if initialization fails).

## 3.1.4 Edge Cases
1. **Concurrent Race Conditions:** Multiple `asyncio` tasks triggering `start()` at the same time.
2. **Re-starting after Close:** Attempting to start a producer that has been definitively closed.
3. **Internal Error Persistence:** Ensuring the producer doesn't get stuck in a "STARTING" state if an exception occurs during connection.

## 3.1.5 Initial Prompt
"Task: Implement an idempotent startup guard for the `AIOKafkaProducer` class in `aiokafka/producer/producer.py`. 

Your goal is to ensure that the `start()` coroutine can be called multiple times without side effects. Currently, the method performs heavy initialization (network handshakes and metadata retrieval) every time it is called. 

**Instructions:**
1. Use an `asyncio.Lock` to wrap the initialization logic to prevent race conditions from concurrent tasks.
2. Introduce a state variable to track the producer's lifecycle. 
3. Refer to the Acceptance Criteria in Section 3.1.3 and ensure you handle the 'Partial Failure' edge case where the state should be reset if the initial connection attempt throws an exception.
4. Provide a `pytest-asyncio` test case that demonstrates calling `start()` three times concurrently and asserts that the connection logic only executes once."
