# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Start the MCP server
uv run main.py

# Run all tests
uv run pytest

# Run a single test file
uv run pytest tests/test_document.py

# Run a single test by name
uv run pytest tests/test_document.py::TestBinaryDocumentToMarkdown::test_binary_document_to_markdown_with_docx
```

## Architecture

This is a Python MCP (Model Context Protocol) server that exposes document-processing tools to AI assistants. The server is built with `FastMCP` from the `mcp` package.

**Entry point:** `main.py` — creates a `FastMCP` instance named `"docs"`, registers tools, and calls `mcp.run()`.

**Tool modules** live in `tools/`. Each module defines functions that are registered with the server in `main.py` via:
```python
mcp.tool()(function_name)
```

**Tests** live in `tests/` and use pytest. Binary fixture files (`.docx`, `.pdf`) are in `tests/fixtures/`.

## Defining MCP Tools

Tools are plain Python functions registered with the `FastMCP` instance. Follow this pattern:

```python
from pydantic import Field

def my_tool(
    param1: str = Field(description="Detailed description of this parameter"),
    param2: int = Field(description="Explain what this parameter does")
) -> ReturnType:
    """One-line summary.

    Detailed explanation of what this tool does.

    When to use: describe the appropriate use case.
    When NOT to use: describe cases where this tool is not appropriate.

    Example:
        Input: param1="foo", param2=42
        Output: ...expected result...
    """
    # implementation
```

Then register in `main.py`:
```python
from tools.my_module import my_tool
mcp.tool()(my_tool)
```

Tool docstrings are surfaced directly to AI assistants as the tool description, so they should be comprehensive and include examples with expected input/output.
