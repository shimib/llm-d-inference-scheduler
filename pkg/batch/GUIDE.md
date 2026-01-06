# Batch Processor - User Guide

## Abstract:
There are certain usecases where the inference requests are not latency sensitive (i.e., cardinality of the required SLO >= minutes). For these usecases, an asynchonous, queue-based support is required.

The batch processor is a composable component that provides services in managing such requests.

The main design principles of the batch processor:
* Bring your own queue (BYOQ) - decouple all aspects of prioritization, routing, retries and scaling from the message persistence layer.
* Composability - the end-user doesn't interact directly with the batch processor (there is no API for it). Instead the batch processor only interacts with the message queue.

XXXCUJXXX
## Table of Contents

- [Request Messages and Consusmption](#request-messages-and-consomption)
- [Retries](#retries)
- [Results](#results)   
- [Implementations](#implementations)
    - [Redis Channels](#redis-channels)
    - [GCP Pub/Sub](#gcp-pub-sub)









### Request Messages and Consusmption

The batch processor expects request messages to have the following format:
```json
{
    "id" : "unique identifier for result mapping",
    "deadline" : "deadline in Unix seconds",
    "payload" : {regular inference payload}
}
```

Example:
```json
{
    "id" : "19933123533434",
    "deadline" : "1764045130",
    "payload": {"model":"food-review","prompt":"hi", "max_tokens":10,"temperature":0}
}
```

XXXXXX


### Retries

When a message processing has failed, either shedded or due to a server-side error, it will be scheduled for a retry (assuming the deadline has not passed).

The batch processor supports exponential-backoff and fixed-rate backoff (TBD).

### Results

Results will be written to the results queue and will have the following structure:

```json
{
    "id" : "id mapped to the request",
    "payload" : {/*inference payload*/} ,
    // or
    "error" : "error's reason"
}
```

## Implementations

### Redis Channels

An example implementation based on Redis channels is provided.

- Redis Channels as the request queues.
- Redis Sorted Set as the retry exponential backoff implementation.
- Redis Channel as the result queue.


![Batch Processor - Redis architecture](/docs/images/batch_processor_redis_architecture.png "BP - Redis")



### GCP Pub/Sub

TBD