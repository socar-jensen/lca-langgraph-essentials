# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a LangGraph educational course repository with Jupyter notebooks teaching core LangGraph concepts: State, Nodes, Edges (parallel and conditional), Memory, and Interrupts/Human-in-the-Loop patterns.

## Development Commands

```bash
# Install dependencies
uv sync

# Run Jupyter notebooks
uv run jupyter lab

# Alternative: activate venv then run jupyter
source .venv/bin/activate
jupyter lab

# Run LangGraph Studio (for visual debugging)
langgraph dev

# Lint with ruff
uv run ruff check .
uv run ruff format .
```

## Environment Setup

Copy `example.env` to `.env` and configure:
- `OPENAI_API_KEY` (required) - Used by ChatOpenAI in notebooks
- `LANGSMITH_API_KEY` (optional) - For tracing in LangSmith
- `LANGSMITH_TRACING=true` - Enable tracing

## Notebook Structure

- **L1_nodes.ipynb** - State definition (TypedDict), node functions, basic graph building with `StateGraph`
- **L2_edges.ipynb** - Parallel execution with `operator.add` reducer for merging state from parallel branches
- **L3-L4_cedges_memory.ipynb** - Conditional routing with `Command` and `add_conditional_edges`, memory persistence with `InMemorySaver` and `thread_id`
- **L5_interrupt.ipynb** - Human-in-the-loop using `interrupt()` function with checkpointer
- **L6_EmailAgent.ipynb** - Complete email workflow demonstrating all concepts: classification, parallel search/bug-tracking, conditional routing, human review with interrupt

## Key LangGraph Patterns

**State Management:**
- Use `TypedDict` for state schemas
- Use `Annotated[List[str], operator.add]` for reducer-based state merging in parallel execution

**Routing:**
- `Command(update={...}, goto="node_name")` for conditional routing from within nodes
- `add_conditional_edges(source, routing_function)` for external routing logic

**Human-in-the-Loop:**
- `interrupt(message)` pauses execution and returns message to caller
- Resume with `Command(resume=response)`
- Requires checkpointer (e.g., `InMemorySaver`) and `thread_id` in config

**Graph Compilation:**
```python
builder = StateGraph(StateClass)
builder.add_node("name", node_function)
builder.add_edge(START, "name")
builder.add_edge("name", END)
graph = builder.compile(checkpointer=memory)  # checkpointer optional
```

## Dependencies

Primary packages: `langgraph`, `langchain`, `langchain-openai`, `langchain-anthropic`
