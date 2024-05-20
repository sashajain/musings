# Concurrency

Key question: *I/O Limited* vs *Compute Limited* job?

- In I/O Limited situations resources are underutilised due to compute tasks sitting idle whilst waiting for slower I/O (network or disk) operations to complete
  - If there are many I/O ops, process can be made faster if mutiple I/O calls are waiting in parallel, rather than executing serially 
    - `multithreading` - switch between mutiple threads with pre-emptible switching - **less recommended**
    - `asyncio` - use an *event loop* to explicitly switch to another task when one is expected to "`await`"
      - This is fast single-threaded, and accordingly faster than multithreading
      - Requires compatible libraries eg. `aiohttp`, rather than `requests`
      - `async def` ... `await`
- In Compute Limited task situations, processes can be made faster by utilising more compute power
  - multiprocesser
    - *Slower* for I/O bound tasks
