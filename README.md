# first-llm-call

Code for **Java to AI #3: [Your First LLM Call From Java, Written Like Production Code](https://medium.com/@abhinav.neo/list/java-to-ai-70943ef97ee9)**.

One LLM call, with no AI framework: Java 21's built-in `HttpClient`, Jackson, and a local model through Ollama. The point is that everything that makes the call production-grade (timeouts, retries, error classification, token tracking) is ordinary engineering.

## What's inside

| Class | Job |
|---|---|
| `LlmClient` | The interface the rest of your app depends on. Swap providers without touching callers. |
| `ChatRequest` / `ChatResponse` | Records for what goes in and what comes out, including token usage, latency, stop reason and attempts. |
| `OllamaClient` | The HTTP call: connect and request timeouts, bounded retries with jittered backoff, typed errors. |
| `RetryPolicy` | Exponential backoff with full jitter. |
| `LlmException` | Carries a `retryable` flag: 408, 429 and 5xx are retried; other 4xx are not. |
| `LlmConfig` | All environment-specific settings, read from environment variables. |

## Run it

1. Install [Ollama](https://ollama.com), then:
   ```bash
   ollama pull llama3.2
   ollama serve        # if it isn't already running
   ```
2. Run the app:
   ```bash
   mvn -q compile exec:java -Dexec.args="Explain a circuit breaker in two sentences."
   ```

## Run the tests

```bash
mvn test
```

The tests use a fake model server built on the JDK's `HttpServer`, so they need no Ollama, no network and no API key. They cover parsing, retries on 429/503, no retries on 404, giving up after the attempt limit, timeouts, truncated replies and malformed responses.

## Configuration

| Variable | Default |
|---|---|
| `LLM_BASE_URL` | `http://localhost:11434` |
| `LLM_MODEL` | `llama3.2` |
| `LLM_CONNECT_TIMEOUT_SECONDS` | `5` |
| `LLM_REQUEST_TIMEOUT_SECONDS` | `60` |
| `LLM_MAX_ATTEMPTS` | `3` |

Part of [JavaToAILab](https://github.com/JavaToAILab).
