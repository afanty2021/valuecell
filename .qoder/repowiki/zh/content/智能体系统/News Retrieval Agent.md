# News Retrieval Agent

<cite>
**本文档引用的文件**   
- [core.py](file://python/valuecell/agents/news_agent/core.py)
- [tools.py](file://python/valuecell/agents/news_agent/tools.py)
- [prompts.py](file://python/valuecell/agents/news_agent/prompts.py)
- [akshare_adapter.py](file://python/valuecell/adapters/assets/akshare_adapter.py)
- [yfinance_adapter.py](file://python/valuecell/adapters/assets/yfinance_adapter.py)
- [manager.py](file://python/valuecell/adapters/assets/manager.py)
- [types.py](file://python/valuecell/adapters/assets/types.py)
- [factory.py](file://python/valuecell/adapters/models/factory.py)
- [markdown-renderer.tsx](file://frontend/src/components/renderer/markdown-renderer.tsx)
</cite>

## 目录
1. [简介](#简介)
2. [核心组件](#核心组件)
3. [数据适配器集成](#数据适配器集成)
4. [工具调用逻辑](#工具调用逻辑)
5. [摘要生成提示词设计](#摘要生成提示词设计)
6. [处理流程](#处理流程)
7. [结果呈现](#结果呈现)
8. [事件驱动交易策略中的作用](#事件驱动交易策略中的作用)
9. [结论](#结论)

## 简介
News Retrieval Agent 是一个专业的新闻智能体，旨在为用户提供实时、准确的金融市场新闻和信息。该智能体通过集成多种数据源和工具，能够高效地抓取、分析和呈现新闻内容，帮助用户及时了解市场动态。

**Section sources**
- [core.py](file://python/valuecell/agents/news_agent/core.py#L1-L134)

## 核心组件
News Retrieval Agent 的核心组件包括 `NewsAgent` 类，该类继承自 `BaseAgent`，并实现了新闻获取和分析的功能。`NewsAgent` 类在初始化时加载了代理配置，并根据配置加载了可用的工具。这些工具包括 `web_search`、`get_breaking_news` 和 `get_financial_news`，分别用于搜索一般新闻、获取突发新闻和获取金融新闻。

**Section sources**
- [core.py](file://python/valuecell/agents/news_agent/core.py#L17-L134)

## 数据适配器集成
News Retrieval Agent 通过集成的数据适配器（如 akshare 和 yfinance）来抓取金融市场新闻。这些适配器提供了与不同数据源的接口，使得智能体能够从多个渠道获取数据。

### AKShare 适配器
AKShare 适配器提供了与 AKShare 库的集成，用于获取全面的全球金融市场数据，包括股票、基金、债券和经济指标。该适配器支持 A 股、港股和美股市场，并能够处理不同市场的数据格式。

### YFinance 适配器
YFinance 适配器提供了与 Yahoo Finance API 的集成，通过 yfinance 库获取股票市场数据，包括实时价格和历史数据。该适配器支持主要的美国、香港和中国交易所。

**Section sources**
- [akshare_adapter.py](file://python/valuecell/adapters/assets/akshare_adapter.py#L1-L800)
- [yfinance_adapter.py](file://python/valuecell/adapters/assets/yfinance_adapter.py#L1-L750)

## 工具调用逻辑
News Retrieval Agent 的工具调用逻辑在 `tools.py` 文件中定义。这些工具通过 `web_search` 函数实现，该函数使用集中式配置系统创建模型实例，并支持多种搜索提供商，如 Google 和 Perplexity。

### Web Search 工具
`web_search` 函数根据环境配置选择搜索提供商。如果 `WEB_SEARCH_PROVIDER` 环境变量设置为 "google" 且 `GOOGLE_API_KEY` 存在，则使用 Google Gemini 进行搜索；否则，使用 Perplexity Sonar 通过 OpenRouter 进行搜索。

### 获取突发新闻
`get_breaking_news` 函数通过调用 `web_search` 函数，使用 "breaking news urgent updates today" 作为搜索查询，获取最新的突发新闻。

### 获取金融新闻
`get_financial_news` 函数根据提供的股票代码或行业，构建相应的搜索查询，获取金融和市场新闻。如果提供了股票代码，则搜索该股票的新闻；如果提供了行业，则搜索该行业的新闻。

**Section sources**
- [tools.py](file://python/valuecell/agents/news_agent/tools.py#L1-L116)

## 摘要生成提示词设计
News Retrieval Agent 的摘要生成提示词设计在 `prompts.py` 文件中定义。这些提示词指导智能体如何生成新闻摘要，确保内容的准确性和客观性。

### 提示词内容
```python
NEWS_AGENT_INSTRUCTIONS = """
You are a News Agent. Provide ONLY factual news content. Do not include any introductory phrases, explanations, or commentary.

## Tool Usage
- Use `get_breaking_news()` for urgent updates
- Use `get_financial_news()` for market and business news  
- Use `web_search()` for comprehensive information gathering

## Critical Output Rules

**NEVER include phrases like:**
- "我来为您查找..."
- "以下是..."
- "根据最新信息..."
- "让我为您搜索..."
- Any introductory or explanatory text

**ALWAYS start directly with news content.**

## Response Format

**[News Title/Headline]**
[Key facts: who, what, when, where]
[Source and date when available]

For multiple news items, repeat this format.

For financial news:
1. **Market Overview**: Key movements and indicators
2. **Individual Stocks**: Company news and price changes
3. **Economic Factors**: Economic data or policy changes

## Guidelines
- Start immediately with news headlines
- Be factual and objective
- Include dates and sources
- No personal interpretation
- No additional commentary
- No follow-up suggestions

Deliver pure news content only.
"""
```

**Section sources**
- [prompts.py](file://python/valuecell/agents/news_agent/prompts.py#L1-L45)

## 处理流程
News Retrieval Agent 的处理流程包括以下几个步骤：

1. **初始化**：加载代理配置和工具。
2. **接收查询**：接收用户的新闻查询请求。
3. **选择工具**：根据查询内容选择合适的工具。
4. **执行工具**：调用选定的工具进行新闻抓取。
5. **生成摘要**：根据抓取的新闻内容生成摘要。
6. **返回结果**：将生成的摘要返回给用户。

### 代码示例
```python
async def stream(
    self,
    query: str,
    conversation_id: str,
    task_id: str,
    dependencies: Optional[Dict] = None,
) -> AsyncGenerator[StreamResponse, None]:
    """Stream news responses."""
    logger.info(
        f"Processing news query: {query[:100]}{'...' if len(query) > 100 else ''}"
    )

    try:
        response_stream = self.knowledge_news_agent.arun(
            query,
            stream=True,
            stream_intermediate_steps=True,
            session_id=conversation_id,
        )
        async for event in response_stream:
            if event.event == "RunContent":
                yield streaming.message_chunk(event.content)
            elif event.event == "ToolCallStarted":
                yield streaming.tool_call_started(
                    event.tool.tool_call_id, event.tool.tool_name
                )
            elif event.event == "ToolCallCompleted":
                yield streaming.tool_call_completed(
                    event.tool.result, event.tool.tool_call_id, event.tool.tool_name
                )

        yield streaming.done()
        logger.info("News query processed successfully")

    except Exception as e:
        logger.error(f"Error processing news query: {str(e)}")
        logger.exception("Full error details:")
        yield {"type": "error", "content": f"Error processing news query: {str(e)}"}
```

**Section sources**
- [core.py](file://python/valuecell/agents/news_agent/core.py#L41-L79)

## 结果呈现
News Retrieval Agent 通过 `MarkdownRenderer` 将结果呈现给用户。`MarkdownRenderer` 是一个前端组件，负责将 Markdown 格式的新闻摘要渲染为用户友好的界面。

### MarkdownRenderer 组件
`MarkdownRenderer` 组件在 `frontend/src/components/renderer/markdown-renderer.tsx` 文件中定义。该组件接收 Markdown 格式的新闻摘要，并将其转换为 HTML 格式，以便在用户界面上显示。

**Section sources**
- [markdown-renderer.tsx](file://frontend/src/components/renderer/markdown-renderer.tsx#L1-L100)

## 事件驱动交易策略中的作用
News Retrieval Agent 在事件驱动交易策略中扮演着重要角色。它能够实时响应市场突发事件，并触发相关通知或建议。通过集成多种数据源和工具，智能体能够快速获取和分析新闻，帮助用户及时做出交易决策。

### 响应市场突发事件
当市场发生突发事件时，News Retrieval Agent 会立即调用 `get_breaking_news` 工具，获取最新的突发新闻。这些新闻会被迅速生成摘要，并通过 `MarkdownRenderer` 呈现给用户。用户可以根据这些信息及时调整交易策略。

### 触发通知或建议
智能体还可以根据新闻内容触发通知或建议。例如，如果新闻中提到某只股票的重大利好消息，智能体可以建议用户买入该股票；如果新闻中提到某只股票的重大利空消息，智能体可以建议用户卖出该股票。

**Section sources**
- [core.py](file://python/valuecell/agents/news_agent/core.py#L66-L70)
- [tools.py](file://python/valuecell/agents/news_agent/tools.py#L66-L82)

## 结论
News Retrieval Agent 通过集成多种数据适配器和工具，实现了高效的新闻获取和分析功能。该智能体不仅能够提供实时、准确的金融市场新闻，还能在事件驱动交易策略中发挥重要作用，帮助用户及时做出交易决策。通过 `MarkdownRenderer` 组件，智能体能够将新闻摘要以用户友好的方式呈现，提升用户体验。

**Section sources**
- [core.py](file://python/valuecell/agents/news_agent/core.py#L1-L134)
- [tools.py](file://python/valuecell/agents/news_agent/tools.py#L1-L116)
- [prompts.py](file://python/valuecell/agents/news_agent/prompts.py#L1-L45)
- [markdown-renderer.tsx](file://frontend/src/components/renderer/markdown-renderer.tsx#L1-L100)