# Study Notes: How LLMs Behave, Models, Prompting, and the Technical Substrate

## 1. Tokens

- Claude reads **tokens**, not characters or words.
- Characters per token depends on the **model's tokenizer** and differs between model generations. Any chars-per-token rule of thumb is model-dependent, so confirm it at build time.
- Everything is counted in tokens: the prompt, conversation history, tool definitions, tool results, and the generated response.
- Tokens are the unit of **pricing** and **budget**. The API bills in tokens and the context window measures in tokens. Estimate cost and fit by counting tokens, not words.

## 2. The Context Window

- It is the **total number of tokens the model can take in for a single request**, a fixed budget.
- It holds everything at once: system prompt, full conversation so far, injected documents, every tool result, and the model output.
- **Two edge behaviors:**
  1. **Input already larger than the window:** the request is **rejected with a validation error before generation begins**.
  2. **Input fits but the ceiling is reached during generation:** current models **stop and return the output so far** with a `model_context_window_exceeded` stop reason. This is **not** an error.
- The application must **trim or summarize history before each call** to keep a long session running.
- **Dev vs. production:** the window rarely fills in development because test inputs are short. In production, longer inputs and more turns fill it faster. A session that worked in testing can fail once inputs grow. (Module 2 covers this in detail.)
- Common wrong beliefs, all false: the model silently drops old turns, the window expands automatically, and temperature is the budget at fault.

## 3. Sampling

- At each step the model produces a **probability distribution** over possible next tokens, then **samples** from it. It does not pick one fixed token.
- **Temperature** shapes the distribution:
  - **Lower:** probability concentrates on the most likely tokens, so output is more **repeatable**.
  - **Higher:** the distribution spreads out, so output is more **varied**.
- Because choice is sampled, the same prompt run twice can give different wording even when both answers are correct. This is a property of how the model generates.
- **Temperature 0** makes output more repeatable but does **not guarantee identical outputs** across calls.
- **Model-dependent controls:** the newest Claude models **do not accept non-default sampling parameters**. Setting `temperature`, `top_p`, or `top_k` returns a **400 error**, and behavior is steered through **prompting** instead. Confirm current support in the API reference.
- **Classifier example:** use low temperature for repeatable behavior. High temperature lets wording and even the chosen label vary. High temperature is not "more accurate," and temperature does not just affect length.

## 4. Non-determinism

- It is the **primary consequence of sampling**: identical inputs do not guarantee identical outputs.
- **Testing implications:**
  - Don't assert exact response text, since the model can phrase a correct answer many ways.
  - **Assert on properties that must hold:** a required field is present, a value is in range, the structure parses.
  - To judge **meaning** rather than structure, use an **eval with a model-graded judge**.
- Evals are the course's standard for knowing a feature is correct (Module 3).

## 5. The Claude Model Family

| Tier | Role |
|---|---|
| **Fable** | Most capable tier, for the most demanding reasoning, coding, and agentic work where maximum intelligence is the priority |
| **Opus** | Demanding work above the Sonnet envelope |
| **Sonnet** | **Balanced default** for most production workloads |
| **Haiku** | Speed and cost efficiency for tasks within its capability envelope |

- Each model is a different tradeoff across **cost, latency, and capability**.
- **Practical rule:**
  - Start with **Sonnet**.
  - Move **up** only when an **eval shows** the current tier misses your quality bar.
  - Move **down to Haiku** only when an **eval shows** the quality drop is acceptable.
- Confirm the current lineup and identifiers at platform.claude.com/docs, since the family evolves.

## 6. Reasoning Modes

- **Model choice and reasoning mode are separate decisions.** Model choice is which family member runs. Reasoning is a **per-call setting**.
- On current models the mode is **adaptive thinking**: the model decides when and how much to think. You tune depth with an **effort setting**, not a fixed token budget.
- The older **`budget_tokens`** control is **deprecated** and returns a **400 error** on the newest generations.
- **Thinking content is omitted from responses by default** on the newest models. Request **summarized display** if you need to show it.
- Reasoning **earns its cost on hard, multi-step problems** and is wasted on lookups and classification.
- Per-model defaults differ (some of the newest models think adaptively by default or always), so confirm the thinking defaults for your model.

**How the levers combine:**
- Capable model with reasoning off: fast and direct.
- Smaller model with reasoning on: spends more tokens thinking.
- Most demanding tasks: capable model with a higher effort setting.
- Module 2 covers enabling reasoning and handling thinking blocks. Module 4 covers choosing models against cost, latency, and quality.

## 7. Prompting Modes

| Mode | Definition |
|---|---|
| **Zero-shot** | Instruction only, no examples |
| **One-shot** | One input/desired-output example |
| **Multi-shot (few-shot)** | Several examples |

- Examples are **not training data**. They sit in the prompt and show the model the exact **shape** of the answer, which a description alone often fails to pin down. Sending examples does **not** retrain the model or persist across calls.
- **Cost and quality trade-off:**
  - Each example costs **tokens on every call** and uses context budget.
  - **Zero-shot** suits simple tasks with an obvious output shape.
  - Move to **one- or multi-shot** when the output has a specific structure, casing, or edge case that description keeps missing.
  - One or two correct examples usually fix the issue faster than another paragraph of instructions.
  - **Discipline:** add the smallest amount of prompt that produces a reliable result.
  - If zero-shot already works on a simple task, adding examples just adds cost for little or no gain.
  - Examples don't lower token cost, and temperature isn't the fix for a structure problem.
- **Interaction with model choice:** a more capable model often succeeds zero-shot where a smaller one needs examples. Adding examples can let a **cheaper model** do the job. Decide the two together by trying the simplest model and fewest examples that meet your eval, adding capability or examples only where the eval says you need them.

## 8. SDK vs. REST

- Claude is reached over an **HTTP REST API**: send a request with your API key and a JSON body, and read a JSON response.
- The **official SDKs** (Python, TypeScript, and others) are a **thin convenience layer over the same REST API**. They handle authentication, request construction, retries, and response parsing, so there is less boilerplate.
- The SDK and raw REST reach the **same API and same model**.
- Module 2 builds on the SDK and the **Messages API**.

## 9. Sync, Streaming, and Real-time

- **Synchronous:** send the request, wait for the **complete response in one piece**. Fine for short responses and backend jobs where no one is waiting.
- **Streaming:** the response arrives **in pieces as it's generated**. This is best for long responses or when a user is watching. Output appears immediately instead of after a blank-screen wait, and your code reassembles the pieces. It uses **server-sent events** over the same HTTP connection.
- Module 2 covers consuming a stream safely and recovering from interruptions.

## 10. Async for High-Volume Work

Two patterns solve **different problems**.

**A. Async client (concurrency)**
- **Python:** `AsyncAnthropic` uses non-blocking `async/await`.
- **TypeScript:** the standard `Anthropic` client is already **Promise-based**, so you `await` directly. There is **no separate async client class**.
- The request still returns in **real time**, but your app can do other work while waiting. Use it for concurrency without blocking.

**B. Message Batches API (bulk offline)**
- Submit a large set of requests in **one call**, receive an **identifier**, and **poll** for completion.
- Jobs can take **up to 24 hours**.
- It runs at a **lower per-token cost** in exchange for latency.
- It suits offline pipelines, eval runs, and bulk jobs where **no user is waiting** and cost matters more than turnaround.
- Example: 50,000 documents overnight is a batch job. A sync loop would hit rate limits and tie up the app, streaming buys nothing with no one watching, and a larger context window doesn't replace per-document calls.

## 11. Quiz and Exercise Answers

**Quiz**
1. **B.** The model samples from a distribution, so wording can vary even when both answers are correct.
2. **C.** Model choice picks the family member. Thinking is a per-call setting that any supporting model can run with on or off.
3. **B.** Extra examples add token cost on every call for little or no gain when zero-shot already works.
4. **C.** Batch submission with polling is the cheapest option for offline bulk work.

**Scenarios**
1. **Sampling is the lever (A).** Low temperature gives repeatability but never guaranteed determinism. High temperature allows the label to vary. Use low temperature for a classifier.
2. **Prompting mode is the lever (B).** A few correct examples show the output structure. The cost is tokens on every call, so use the fewest that work.
3. **Request shape is the lever (C).** Use the batch pattern.
4. **Context budget is the lever (B).** Oversized input is rejected before generation. Hitting the ceiling mid-generation returns truncated output with `model_context_window_exceeded`. Trim or summarize history.

## 12. Quick Recall Cheat Sheet

- Tokens are the unit of input, output, cost, and context. They are not words.
- Oversized input gives a **validation error**. Hitting the limit mid-generation gives **truncated output plus the stop reason**, with no error.
- Sampling means a probability distribution. **Temperature 0 is more repeatable, not deterministic.**
- Newest models: setting `temperature`, `top_p`, or `top_k` returns **400**. Steer by prompting.
- Non-determinism means testing by **properties** or **model-graded evals**, not exact strings.
- Tiers, cheapest and fastest to most capable: **Haiku, Sonnet (default), Opus, Fable**. Move tiers only on **eval evidence**.
- Model and reasoning are **independent**. Reasoning is **adaptive thinking** with an **effort** setting. `budget_tokens` is deprecated (400 on the newest models). Thinking is omitted by default, so request summarized display.
- Zero-shot, one-shot, and multi-shot examples live in the prompt and don't train the model. They cost tokens on every call. Use the fewest that work.
- SDK is a thin layer over REST, with the same API and model.
- Sync is for short or backend work. Streaming (server-sent events) is for users watching. Async client is for concurrency. **Batches** are for bulk offline work: up to 24 hours, cheaper, polled.
- Always **confirm current details** (tokenizer, parameters, lineup, thinking defaults) at build time.

I can also turn these into a doc you can edit and export, or make a practice quiz from them.