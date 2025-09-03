# Tasks Delegated to the Queue

Normally, the tasks delegated to the queue:

- Take extended periods of time to execute and may be interrupted by PHP limitations (like the PHP `max_execution_time` parameter).
- Are external which introduces delays from authentication, awaiting responses from potentially multiple interrogations, or may even fail completely because the server is offline.
- Are not part of the PHP response (sending emails, processing videos).

Here are some example tasks meant for the queue:

- **Data Processing** like big data analytics, scientific simulations, or mathematical computations.
- **File Handling & Media Processing** like video and image processing, or compression/decompression of large files.
- **Networking** like uploading/downloading large files.
- **Database Operations** like imports/exports or migrations.
- **System & Infrastructure Tasks** like OS updates, software compilation, CI pipelines.

The long execution times are caused by:

- Data size - gigabytes, terabytes etc.
- Complex algorithms.
- Hardware limitations - CPU speed, memory, storage, network bandwidth.
- External dependencies - waiting on APIs or human input.

# How the Queue Works

- The queue system has an active **daemon** that listens for TPC connections on a specific port and **stores incoming messages** into Redis.
This method supports a **large number of requests per second** without overloading.
- Operations are then **scheduled for execution** when resources are available.
The order of the execution uses the **FIFO** (First-In, First-Out) method where the oldest request is processed first, followed by newer requests.

# Main Features

- **Logging** - Ensures maintainability and allows debugging.
- **Security** - The **firewall** allows requests only from whitelisted IPs for fast response times.
- **Retry Mechanism** - Retries failing tasks a certain number of times before removing a task from the queue.
- **Reporting** - The logs enable developers to investigate various metrics via console commands like:
    - Queue length - How many jobs are in the to-do list.
    - Processing time per job.
    - Error rates - How many messages failed.
    - Throughput - Jobs/sec processed.
