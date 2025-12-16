# 🚀 LangChain for LLM Application Development - Updated for 2024

> **✨ Modernized Version** - This repository has been updated from the original [DeepLearning.AI course](https://www.deeplearning.ai/short-courses/langchain-for-llm-application-development/) to work with **LangChain 1.2.0** and **OpenAI v1.0+** (December 2024)

A hands-on course on building LLM applications with LangChain, created by **Harrison Chase** (Creator of LangChain) and **Andrew Ng** (DeepLearning.AI), now updated with modern best practices and LCEL (LangChain Expression Language).

[![LangChain](https://img.shields.io/badge/LangChain-1.2.0-blue)](https://python.langchain.com/)
[![OpenAI](https://img.shields.io/badge/OpenAI-v1.0+-green)](https://github.com/openai/openai-python)
[![Python](https://img.shields.io/badge/Python-3.9+-yellow)](https://www.python.org/)

## 🆕 What's New in This Update

This repository brings the original course materials up to date with current LangChain standards:

### Major Updates
- ✅ **LangChain 1.2.0** - Updated all deprecated imports and patterns
- ✅ **OpenAI v1.0+** - New client-based API pattern
- ✅ **Pydantic v2** - Modern output parsers with type safety
- ✅ **LCEL Support** - Added new notebook (L2b) demonstrating LangChain Expression Language

### Migration Highlights
- `langchain.chat_models` → `langchain_openai`
- `langchain.chains` → `langchain_classic.chains`
- `langchain.prompts` → `langchain_core.prompts`
- `ResponseSchema` → Pydantic `BaseModel` with `Field`
- Added session-based memory with `RunnableWithMessageHistory`

## 📦 Quick Start

### Installation

```bash
# Core packages
pip install langchain langchain-openai langchain-community langchain-experimental langchain-classic openai pydantic python-dotenv

# Optional dependencies
pip install docarray tiktoken wikipedia
```

### Environment Setup

Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your-api-key-here
```

## 📚 Course Content

### L1 - Models, Prompts and Parsers
Learn to call LLMs, craft prompts, and parse responses using modern patterns.

**Key Updates:**
- ✨ New OpenAI v1.0+ client pattern
- ✨ Pydantic models for structured output
- ✨ Type-safe parsing with `JsonOutputParser`

```python
# Modern approach
from openai import OpenAI
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import JsonOutputParser
from pydantic import BaseModel, Field

class ReviewResponse(BaseModel):
    gift: bool = Field(description="...")
    delivery_days: int = Field(description="...")
    price_value: str = Field(description="...")

client = OpenAI()
parser = JsonOutputParser(pydantic_object=ReviewResponse)
```

### L2 - Memory (Classic Approach)
Conversation memory using the classic `ConversationChain` pattern.

**Note:** This approach is deprecated but included for understanding legacy code.

### L2b - Memory with LCEL ⭐ **NEW**
Modern memory management using **LCEL (LangChain Expression Language)**.

**Key Features:**
- 🔗 Pipe operator (`|`) for intuitive chain composition
- 💬 `RunnableWithMessageHistory` for session-based conversations
- 🚀 Built-in streaming, batch, and async support
- 🗂️ Multi-user session management

```python
# LCEL approach (Recommended)
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.runnables.history import RunnableWithMessageHistory

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant."),
    MessagesPlaceholder(variable_name="history"),
    ("human", "{input}")
])

chain = prompt | llm  # Pipe operator!

chain_with_history = RunnableWithMessageHistory(
    chain,
    get_session_history,
    input_messages_key="input",
    history_messages_key="history",
)

# Session-based conversations
response = chain_with_history.invoke(
    {"input": "Hello!"},
    config={"configurable": {"session_id": "user123"}}
)
```

### L3 - Chains
Sequential chains, router chains, and complex workflows.

**Updated:**
- ✨ Uses `langchain_classic` for legacy patterns
- ✨ Modern prompts with `langchain_core`

### L4 - Question Answering over Documents
RAG (Retrieval-Augmented Generation) for document Q&A.

**Updated:**
- ✨ `langchain_community` loaders and vectorstores
- ✨ `OpenAIEmbeddings` from `langchain_openai`
- ✨ Modern text splitters

### L5 - Evaluation
Evaluate LLM application quality systematically.

**Updated:**
- ✨ Evaluation chains from `langchain_classic`

### L6 - Agents
Build autonomous agents with tools and reasoning capabilities.

**Updated:**
- ✨ `langchain_experimental` for Python agent
- ✨ `langchain_community` for tool loading
- ✨ Modern tool decorator from `langchain_core`

## 🔄 Migration Guide

### Key Package Structure Changes

| Old (Deprecated) | New (Current) |
|------------------|---------------|
| `langchain.chat_models` | `langchain_openai` |
| `langchain.llms` | `langchain_openai` |
| `langchain.embeddings` | `langchain_openai` |
| `langchain.chains` | `langchain_classic.chains` |
| `langchain.memory` | `langchain_classic.memory` |
| `langchain.prompts` | `langchain_core.prompts` |
| `langchain.output_parsers` | `langchain_core.output_parsers` |
| `langchain.document_loaders` | `langchain_community.document_loaders` |
| `langchain.vectorstores` | `langchain_community.vectorstores` |
| `langchain.agents` | `langchain_community` / `langchain_experimental` |

### OpenAI API Changes

**Before (v0.28):**
```python
import openai
openai.api_key = "..."
response = openai.ChatCompletion.create(...)
```

**After (v1.0+):**
```python
from openai import OpenAI
client = OpenAI(api_key="...")
response = client.chat.completions.create(...)
```

### Output Parsers Changes

**Before:**
```python
from langchain.output_parsers import ResponseSchema, StructuredOutputParser
schemas = [ResponseSchema(name="field", description="...")]
parser = StructuredOutputParser.from_response_schemas(schemas)
```

**After:**
```python
from langchain_core.output_parsers import JsonOutputParser
from pydantic import BaseModel, Field

class MyOutput(BaseModel):
    field: str = Field(description="...")

parser = JsonOutputParser(pydantic_object=MyOutput)
```

## 💡 Classic vs LCEL

### Classic Chains (Deprecated)
```python
from langchain_classic.chains import ConversationChain
from langchain_classic.memory import ConversationBufferMemory

memory = ConversationBufferMemory()
conversation = ConversationChain(llm=llm, memory=memory)
response = conversation.predict(input="Hello")
```

**Limitations:**
- ❌ Implicit behavior ("magic")
- ❌ No session management
- ❌ Limited streaming support
- ❌ Difficult to debug

### LCEL (Current Standard)
```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.runnables.history import RunnableWithMessageHistory

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are helpful."),
    MessagesPlaceholder(variable_name="history"),
    ("human", "{input}")
])

chain = prompt | llm  # Clear composition

chain_with_history = RunnableWithMessageHistory(
    chain, get_session_history, ...
)

response = chain_with_history.invoke(
    {"input": "Hello"},
    config={"configurable": {"session_id": "abc"}}
)
```

**Advantages:**
- ✅ Explicit and transparent
- ✅ Session-based conversations
- ✅ Auto-support: streaming, batch, async
- ✅ Better debugging and monitoring
- ✅ Type safety

## 📊 Installed Package Versions

```
langchain                1.2.0
langchain-classic        1.0.0
langchain-community      0.4.1
langchain-core           1.2.1
langchain-experimental   0.4.1
langchain-openai         1.1.3
langchain-text-splitters 1.1.0
openai                   2.12.0
pydantic                 2.12.3
```

## 🎓 About the Original Course

**Instructors:**
- **Harrison Chase** - Co-Founder and CEO at LangChain
- **Andrew Ng** - Co-founder of Coursera, Founder of DeepLearning.AI

**Course Link:** [DeepLearning.AI - LangChain for LLM Application Development](https://www.deeplearning.ai/short-courses/langchain-for-llm-application-development/)

## 📖 Additional Resources

- 📚 [LangChain Documentation](https://python.langchain.com/)
- 🔗 [LangChain GitHub](https://github.com/langchain-ai/langchain)
- 📘 [LCEL Documentation](https://python.langchain.com/docs/expression_language/)
- 🚀 [OpenAI Python SDK](https://github.com/openai/openai-python)
- 🐍 [Pydantic Documentation](https://docs.pydantic.dev/)

## 🤝 Contributing

This is an educational repository. If you find issues with the modernization:

1. Check compatibility with package versions listed above
2. Review [QUICK_START.md](QUICK_START.md) for setup instructions
3. Open an issue with detailed error messages

## 📄 License

This repository contains course materials from DeepLearning.AI, updated for modern LangChain compatibility.

## 🙏 Acknowledgments

- **Original Course:** DeepLearning.AI and LangChain
- **Modernization:** Updated for LangChain 1.2.0 compatibility
- **Community:** LangChain and OpenAI development teams

---

**Happy Learning! 🎉**
