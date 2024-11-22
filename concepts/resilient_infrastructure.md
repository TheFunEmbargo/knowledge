### Queues

* FIFO Queue - ensuring the events are replayed in the sequence they occurred
* Indefinite Time to Live - messages are to be considered until they succeed or end up in the dead letter
* Service health check - pause trying if there's a known outage
* Stop if 4XX Client Error
* Retry on 5XX Server Error
* N retries & Exponential back-off - avoids overwhelming the system
