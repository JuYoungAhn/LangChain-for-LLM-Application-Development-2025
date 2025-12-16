# LangChain for LLM Application Development

Updated version of the [DeepLearning.AI course](https://www.deeplearning.ai/short-courses/langchain-for-llm-application-development/) by Harrison Chase and Andrew Ng, modernized for LangChain 1.2.0 and OpenAI v1.0+ (December 2024).

## What's Updated

- Updated to LangChain 1.2.0 (from deprecated 0.x versions)
- OpenAI v1.0+ client pattern
- Pydantic v2 output parsers
- Added LCEL (LangChain Expression Language) examples
- All 7 notebooks tested and working

## Installation

```bash
pip install langchain langchain-openai langchain-community langchain-experimental langchain-classic openai pydantic python-dotenv
```

Create `.env` file:
```
OPENAI_API_KEY=your-key-here
```

## Notebooks

- **L1** - Models, Prompts, Parsers (Pydantic v2, modern output parsing)
- **L2** - Memory (Classic chains - deprecated but included for reference)
- **L2b** - Memory with LCEL (Modern approach using pipe operators)
- **L3** - Chains (Sequential and router chains)
- **L4** - Q&A over Documents (RAG implementation)
- **L5** - Evaluation
- **L6** - Agents

## Key Changes

### Import Updates
```python
# Old
from langchain.chat_models import ChatOpenAI
from langchain.prompts import ChatPromptTemplate

# New
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
```

### OpenAI API
```python
# Old
import openai
openai.api_key = "..."
response = openai.ChatCompletion.create(...)

# New
from openai import OpenAI
client = OpenAI()
response = client.chat.completions.create(...)
```

### LCEL (Recommended)
```python
# Modern pattern with pipe operator
chain = prompt | llm | output_parser

# Session-based memory
chain_with_history = RunnableWithMessageHistory(chain, get_session_history, ...)
response = chain_with_history.invoke(
    {"input": "Hello"},
    config={"configurable": {"session_id": "abc"}}
)
```

## Package Versions

```
langchain       1.2.0
openai          2.12.0
pydantic        2.12.3
```

See `MIGRATION_SUMMARY.md` for complete version list.

## Credits

- Original course by Harrison Chase (LangChain) and Andrew Ng (DeepLearning.AI)
- Course link: https://www.deeplearning.ai/short-courses/langchain-for-llm-application-development/
- Updated for LangChain 1.2.0 compatibility

## Resources

- [LangChain Documentation](https://python.langchain.com/)
- [LCEL Guide](https://python.langchain.com/docs/expression_language/)
