# Tooling
* [rate limiting](https://loadforge.com/guides/implementing-rate-limits-in-fastapi-a-step-by-step-guide)
* 
# Network interfaces
1. In a docker container bind fastapi to 0.0.0.0 so it can be accessed from outside the container.
2. Outside a docker container bind to 127.0.0.1 so its only accessible to the host machines localhost eg 
```  
fastapi:
    image: fastapi-image
    ports:
      - "127.0.0.1:8000:8000"
```
3. Use nginx to forward requests to 127.0.0.1:8000

