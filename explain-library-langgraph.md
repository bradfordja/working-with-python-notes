# LangGraph Library and Core Methods

**LangGraph** is a Python framework for building **stateful AI agents and multi-step LLM workflows as graphs**. A graph contains:

* **State** — shared data passed through the workflow
* **Nodes** — functions that perform work
* **Edges** — rules that determine which node runs next

LangGraph is useful when an AI application requires branching, loops, tool calls, memory, persistence, human approval, retries, or multiple agents. ([Docs by LangChain][1])

```bash
pip install -U langgraph
```

```python
from langgraph.graph import StateGraph, START, END
```

---

## Basic LangGraph Example

```python
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, START, END


class AgentState(TypedDict):
    question: str
    answer: str


def generate_answer(state: AgentState):
    return {
        "answer": f"Answering: {state['question']}"
    }


builder = StateGraph(AgentState)

builder.add_node("generate_answer", generate_answer)
builder.add_edge(START, "generate_answer")
builder.add_edge("generate_answer", END)

graph = builder.compile()

result = graph.invoke({
    "question": "What is LangGraph?",
    "answer": ""
})

print(result)
```

---

# Core Classes, Methods, and Concepts

## 1. `StateGraph()`

Creates a graph whose nodes share and update a common state.

```python
builder = StateGraph(AgentState)
```

The state schema is commonly defined with:

* `TypedDict`
* Pydantic `BaseModel`
* `MessagesState`

Use `StateGraph` when the workflow has multiple steps, branches, loops, or shared information. ([Docs by LangChain][1])

---

## 2. State

State is the shared data structure passed between nodes.

```python
class AgentState(TypedDict):
    question: str
    answer: str
```

Each node receives the current state and returns only the fields it wants to update.

```python
def process(state: AgentState):
    return {"answer": "Completed"}
```

---

## 3. `MessagesState`

A built-in state type for conversational agents.

```python
from langgraph.graph import MessagesState
```

It contains a `messages` field and handles adding conversation messages to the state.

```python
def chatbot(state: MessagesState):
    return {
        "messages": [
            {"role": "assistant", "content": "Hello"}
        ]
    }
```

It is useful for chatbots, tool-calling agents, and conversational workflows. ([Docs by LangChain][2])

---

## 4. Node

A node is a Python function that performs one step of the workflow.

```python
def classify_request(state):
    return {"category": "technical"}
```

A node can:

* Call an LLM
* Call an API
* Query a database
* Execute a tool
* Validate input
* Update state
* Perform normal Python logic

Nodes do the work; edges control what runs next. ([Docs by LangChain][1])

---

## 5. `add_node()`

Adds a node function to the graph.

```python
builder.add_node(
    "classify",
    classify_request
)
```

You can also pass the function directly:

```python
builder.add_node(classify_request)
```

A node may also be configured with retry or cache policies.

```python
from langgraph.types import RetryPolicy

builder.add_node(
    "external_api",
    call_external_api,
    retry_policy=RetryPolicy(max_attempts=3)
)
```

([Docs by LangChain][3])

---

## 6. `START`

A special constant representing the graph’s entry point.

```python
builder.add_edge(
    START,
    "classify"
)
```

It identifies the first node or nodes to execute.

---

## 7. `END`

A special constant representing graph completion.

```python
builder.add_edge(
    "generate_answer",
    END
)
```

When execution reaches `END`, the graph returns its final state.

---

## 8. `add_edge()`

Creates a fixed transition between nodes.

```python
builder.add_edge(
    "classify",
    "generate_answer"
)
```

This means `generate_answer` always runs after `classify`.

A simple workflow:

```text
START
  ↓
classify
  ↓
generate_answer
  ↓
END
```

---

## 9. `add_conditional_edges()`

Creates dynamic routing based on the current state.

```python
from typing import Literal


def route_request(
    state: AgentState
) -> Literal["technical", "general"]:
    if state["category"] == "technical":
        return "technical"

    return "general"


builder.add_conditional_edges(
    "classify",
    route_request
)
```

Use it for:

* Decision-making
* Agent tool selection
* Error routing
* Approval workflows
* Retry or fallback paths
* Loop termination

([Docs by LangChain][3])

---

## 10. `compile()`

Validates and compiles the graph into an executable application.

```python
graph = builder.compile()
```

After compilation, the graph supports methods such as:

* `invoke()`
* `ainvoke()`
* `stream()`
* `astream()`

Compilation can also attach persistence through a checkpointer.

```python
graph = builder.compile(
    checkpointer=checkpointer
)
```

---

## 11. `invoke()`

Runs the graph synchronously and returns the final state.

```python
result = graph.invoke({
    "question": "Explain AI",
    "answer": ""
})
```

Use it for normal synchronous execution.

---

## 12. `ainvoke()`

Runs the graph asynchronously.

```python
result = await graph.ainvoke({
    "question": "Explain AI",
    "answer": ""
})
```

Use it when nodes perform asynchronous operations such as:

* LLM calls
* HTTP requests
* Database access
* External tool calls

---

## 13. `stream()`

Runs the graph and yields intermediate results while it executes.

```python
for chunk in graph.stream(
    {"question": "Explain AI", "answer": ""},
    stream_mode="updates"
):
    print(chunk)
```

Common stream modes include:

* `updates` — state changes from each node
* `values` — full state after each step
* `messages` — LLM message or token events
* `custom` — application-defined events
* `checkpoints` — persistence events
* `debug` — detailed execution information

Current LangGraph documentation also supports a unified version-two stream format using `version="v2"`. ([Docs by LangChain][4])

---

## 14. `astream()`

Asynchronous version of `stream()`.

```python
async for chunk in graph.astream(
    {"question": "Explain AI", "answer": ""},
    stream_mode="updates"
):
    print(chunk)
```

Useful for streaming responses to FastAPI, WebSocket, or frontend clients.

---

## 15. Reducers

Reducers define how state updates should be combined.

```python
import operator
from typing import Annotated
from typing_extensions import TypedDict


class State(TypedDict):
    results: Annotated[list[str], operator.add]
```

With `operator.add`, new values are appended instead of replacing the existing list.

```python
def node_one(state):
    return {"results": ["A"]}


def node_two(state):
    return {"results": ["B"]}
```

Final value:

```python
["A", "B"]
```

Reducers are particularly important when multiple nodes update the same state field. ([Docs by LangChain][3])

---

## 16. `add_messages`

A reducer designed for conversation messages.

```python
from typing import Annotated
from langgraph.graph.message import add_messages


class ChatState(TypedDict):
    messages: Annotated[list, add_messages]
```

It merges new messages into the existing conversation while handling message IDs correctly.

---

## 17. `Command`

Allows a node to update state and control routing in one return value.

```python
from langgraph.types import Command


def review_node(state):
    if state["approved"]:
        return Command(
            update={"status": "approved"},
            goto="publish"
        )

    return Command(
        update={"status": "rejected"},
        goto="revise"
    )
```

Use `Command` when state updates and routing decisions belong together.

---

## 18. `Send`

Dynamically sends different inputs to one or more nodes.

```python
from langgraph.types import Send


def create_tasks(state):
    return [
        Send("process_item", {"item": item})
        for item in state["items"]
    ]
```

It is commonly used for:

* Parallel processing
* Map-reduce workflows
* Processing multiple documents
* Running multiple specialized agents

([Docs by LangChain][3])

---

## 19. Loops

LangGraph supports cycles, allowing a node to run repeatedly until a condition is met.

```python
def route(state):
    if state["attempts"] >= 3:
        return END

    return "retry"
```

```python
builder.add_conditional_edges(
    "check_result",
    route
)

builder.add_edge(
    "retry",
    "check_result"
)
```

Always provide a clear termination condition to prevent infinite execution. ([Docs by LangChain][3])

---

## 20. `RetryPolicy`

Configures automatic retries for failed nodes.

```python
from langgraph.types import RetryPolicy


builder.add_node(
    "call_service",
    call_service,
    retry_policy=RetryPolicy(
        max_attempts=3
    )
)
```

Useful for transient failures such as:

* API timeouts
* Rate limits
* Temporary database errors
* Network failures

Retries should still be combined with idempotency and sensible timeout handling.

---

## 21. `CachePolicy`

Caches node results for a specified time.

```python
from langgraph.types import CachePolicy


builder.add_node(
    "retrieve_reference_data",
    retrieve_reference_data,
    cache_policy=CachePolicy(ttl=120)
)
```

Useful for expensive or repeated operations whose output does not change frequently. ([Docs by LangChain][3])

---

# Persistence and Memory

## 22. Checkpointer

A checkpointer saves graph state after execution steps.

```python
from langgraph.checkpoint.memory import InMemorySaver


checkpointer = InMemorySaver()

graph = builder.compile(
    checkpointer=checkpointer
)
```

A thread identifier associates executions with a saved conversation or workflow.

```python
config = {
    "configurable": {
        "thread_id": "user-123"
    }
}

result = graph.invoke(
    {"messages": []},
    config=config
)
```

Persistence supports:

* Conversation memory
* Workflow recovery
* Human approval
* Resuming failed processes
* Reviewing execution history

For production, a persistent database-backed checkpointer is preferable to in-memory storage.

---

## 23. `get_state()`

Retrieves the current saved state for a thread.

```python
snapshot = graph.get_state(config)
```

Useful for inspecting or displaying the current workflow status.

---

## 24. `get_state_history()`

Retrieves previous state snapshots.

```python
history = graph.get_state_history(config)

for snapshot in history:
    print(snapshot)
```

Useful for:

* Auditing
* Debugging
* Reviewing agent decisions
* Time-travel or recovery workflows

---

## 25. `update_state()`

Manually modifies persisted graph state.

```python
graph.update_state(
    config,
    {"status": "approved"}
)
```

Useful for:

* Human corrections
* Administrative actions
* Testing recovery paths
* Updating workflow decisions

---

# Human-in-the-Loop

## 26. `interrupt()`

Pauses graph execution and waits for external input.

```python
from langgraph.types import interrupt


def approval_node(state):
    approval = interrupt({
        "question": "Approve this transaction?",
        "details": state["transaction"]
    })

    return {"approved": approval}
```

LangGraph saves the state when interrupted so the workflow can later resume. ([Docs by LangChain][5])

---

## 27. Resume with `Command`

Resume an interrupted graph by passing a value through `Command`.

```python
from langgraph.types import Command


result = graph.invoke(
    Command(resume=True),
    config=config
)
```

This is commonly used for:

* Human approval
* Reviewing AI-generated content
* Confirming sensitive operations
* Correcting agent output

---

# Subgraphs

## 28. Subgraph

A compiled graph can be added as a node inside another graph.

```python
research_graph = research_builder.compile()

main_builder.add_node(
    "research",
    research_graph
)
```

Subgraphs are useful for:

* Multi-agent systems
* Reusable workflows
* Separating team responsibilities
* Encapsulating complex agent behavior

A wrapper function can be used when the parent graph and subgraph have different state schemas. ([Docs by LangChain][6])

---

# Graph Inspection

## 29. `get_graph()`

Returns a representation of the compiled graph.

```python
graph_definition = graph.get_graph()
```

Useful for inspection, documentation, and visualization.

---

## 30. `draw_mermaid()`

Generates a Mermaid diagram.

```python
print(
    graph.get_graph().draw_mermaid()
)
```

Example output:

```text
START --> classify
classify --> technical
classify --> general
technical --> END
general --> END
```

---

## 31. `draw_mermaid_png()`

Generates a PNG representation of the graph.

```python
image = graph.get_graph().draw_mermaid_png()
```

Useful in architecture documentation and notebooks. ([Docs by LangChain][3])

---

# Graph API vs Functional API

LangGraph supports two primary programming styles.

## Graph API

Uses explicit nodes, edges, and shared state.

```python
builder = StateGraph(State)
builder.add_node(...)
builder.add_edge(...)
```

Best for:

* Complex branching
* Visual workflow design
* Parallel execution
* Shared state
* Multi-agent systems

## Functional API

Defines workflows in a more function-oriented style.

Best for:

* Existing procedural code
* Simpler workflows
* Developers who do not need explicit graph construction

Both APIs use the same underlying runtime and may be combined. ([Docs by LangChain][7])

---

# Typical Agent Workflow

```text
User Request
     ↓
Classify Intent
     ↓
Should a Tool Be Used?
   ↙             ↘
 Yes              No
  ↓                ↓
Call Tool       Generate Answer
  ↓                ↓
Validate Result ←──┘
  ↓
Need Human Approval?
   ↙             ↘
 Yes              No
  ↓                ↓
Interrupt        Return Result
```

---

# Core Methods Cheat Sheet

| Method or concept         | Purpose                              |
| ------------------------- | ------------------------------------ |
| `StateGraph()`            | Creates a stateful graph             |
| `MessagesState`           | Built-in conversational state        |
| `add_node()`              | Adds processing logic                |
| `add_edge()`              | Adds a fixed transition              |
| `add_conditional_edges()` | Adds dynamic routing                 |
| `START`                   | Defines the entry point              |
| `END`                     | Defines graph completion             |
| `compile()`               | Produces an executable graph         |
| `invoke()`                | Executes synchronously               |
| `ainvoke()`               | Executes asynchronously              |
| `stream()`                | Streams intermediate results         |
| `astream()`               | Streams asynchronously               |
| `Command`                 | Updates state and controls routing   |
| `Send`                    | Dynamically dispatches parallel work |
| `RetryPolicy`             | Retries failed nodes                 |
| `CachePolicy`             | Caches node results                  |
| `interrupt()`             | Pauses for external input            |
| `get_state()`             | Reads persisted state                |
| `get_state_history()`     | Reads prior checkpoints              |
| `update_state()`          | Modifies saved state                 |
| `get_graph()`             | Inspects the graph                   |
| Subgraph                  | Embeds one graph in another          |

# Senior-Level Interview Answer

> LangGraph is a framework for building stateful and controllable AI agents as graphs. I define shared state, implement processing steps as nodes, and use edges or conditional edges to control execution. It is particularly useful when an agent requires branching, loops, parallel operations, tool calls, persistence, human approval, or recovery after failure. In production, I use checkpointers for durable state, idempotent nodes and retry policies for resilience, structured state schemas for maintainability, streaming for responsive user interfaces, and subgraphs to separate reusable or multi-agent workflows.

[1]: https://docs.langchain.com/oss/python/langgraph/graph-api?utm_source=chatgpt.com "Graph API overview - Docs by LangChain"
[2]: https://docs.langchain.com/oss/python/langgraph/overview?utm_source=chatgpt.com "LangGraph overview - Docs by LangChain"
[3]: https://docs.langchain.com/oss/python/langgraph/use-graph-api?utm_source=chatgpt.com "Use the graph API - Docs by LangChain"
[4]: https://docs.langchain.com/oss/python/langgraph/streaming?utm_source=chatgpt.com "Streaming - Docs by LangChain"
[5]: https://docs.langchain.com/oss/python/langgraph/interrupts?utm_source=chatgpt.com "Interrupts - Docs by LangChain"
[6]: https://docs.langchain.com/oss/python/langgraph/use-subgraphs?utm_source=chatgpt.com "Subgraphs - Docs by LangChain"
[7]: https://docs.langchain.com/oss/python/langgraph/quickstart?utm_source=chatgpt.com "Quickstart - Docs by LangChain"
