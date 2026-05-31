# LangChain Advanced

## Guardrails 守卫

守卫通过在 Agent 执行的关键节点验证和过滤内容，帮助构建安全、合规的人工智能应用。他们能够检测敏感信息，执行内容政策，验证输出，并在不安全行为引发问题前预防。常见的使用场景包括：

- 防止 PII 泄露
- 检测和阻挡即时注入攻击
- 屏蔽不当或有害内容
- 执行业务规则和合规要求
- 验证输出质量和准确性

通常使用中间件阶段设置守卫实现节点拦截执行。内置有 PII 守卫和人机交互守卫。

#### 自定义守卫

###### Before agent guardrails

在每次调用开始时，使用“before agent”钩子验证一次请求。这对于会话级检查非常有用，比如认证、速率限制或在处理开始前阻止不当请求。

```python
from typing import Any
from langchain.agents.middleware import AgentMiddleware, AgentState, hook_config
from langchain.agents import create_agent

class ContentFilterMiddleware(AgentMiddleware):
    """确定性防护：拦截包含禁用关键词的请求"""
    def __init__(self, banned_keywords: list[str]):
        super().__init__()
        self.banned_keywords = [kw.lower() for kw in banned_keywords]  # 关键词转小写

    @hook_config(can_jump_to=["end"])
    def before_agent(self, state: AgentState, runtime) -> dict[str, Any] | None:
        if not state["messages"]: return None  # 检查是否有消息

        first_msg = state["messages"][0]
        if first_msg.type != "human":  # 只检查用户消息
            return None
        content = first_msg.content.lower()

        # 检测禁用关键词
        for kw in self.banned_keywords:
            if kw in content:
                return {
                    "messages": [{
                        "role": "assistant",
                        "content": "无法处理包含不当内容的请求，请重新表述。"
                    }],
                    "jump_to": "end"  # 直接跳转到结束
                }
        return None

# 创建智能体
agent = create_agent(
    model="gpt-4.1",
    middleware=[
        ContentFilterMiddleware(banned_keywords=["hack", "exploit", "malware"])
    ]
)
```



###### After agent guardrails

使用“after agent”钩子验证一次最终输出，然后返回给用户。这对于基于模型的安全检查、质量验证或对整个代理响应的最终合规扫描非常有用。

(这个和上面的案例类似，从检查用户输入变成检查 agent/LLM 输出)

**结合多个守卫**：可以通过将多个守卫添加到中间件数组来堆叠它们。它们按顺序执行，允许构建分层保护。



## Runtime 运行时

LangChain的`create_agent`在底层运行于 LangGraph 的 Runtime 运行时。LangGraph 暴露的 `Runtime` 具有以下信息的对象：

1. **context**：静态信息，如用户ID、数据库连接或其他代理调用的依赖关系
2. **Store**：一个 BaseStore 实例用于 长期记忆
3. **Stream writer**：通过流模式用于流式信息流的对象`"custom"`

#### 访问方式

使用 create_agent 创建智能体时，你可以指定一个 context_schema 来定义存储在智能体上下文中的 Runtime 结构。 调用智能体时，传入 context 参数并携带本次运行的相关配置：

```python
from dataclasses import dataclass
from langchain.agents import create_agent

@dataclass
class Context:
    user_name: str
        
agent = create_agent(
    model="gpt-5-nano",
    tools=[...],
    context_schema=Context  # 指定上下文结构
)
agent.invoke(
    {"messages": [{"role": "user", "content": "What's my name?"}]},
    context=Context(user_name="John Smith")  # 传入上下文数据
)
```

你可以在工具内部访问运行时信息，以实现访问上下文（context）/读取或写入长期记忆/写入自定义流（例如：工具执行进度/更新）。在工具内部使用 runtime 参数访问 ToolRuntime 对象：

```python
from dataclasses import dataclass
from langchain.tools import tool, ToolRuntime  

@dataclass
class Context:
    user_id: str

@tool
def fetch_user_email_preferences(runtime: ToolRuntime[Context]) -> str:
    """从存储中获取用户的邮件偏好设置。"""
    user_id = runtime.context.user_id  # 从上下文中获取用户ID

    preferences: str = "该用户希望你撰写简洁且礼貌的邮件。"
    if runtime.store:  # 检查是否有存储可用
        if memory := runtime.store.get(("users",), user_id):
            preferences = memory.value["preferences"]
    return preferences
```

你也可以在中间件中访问运行时信息，以根据用户上下文生成动态提示词、修改消息或控制智能体行为。

在节点式钩子（node-style hooks）中使用 runtime 参数访问 Runtime 对象；对于包装式钩子（wrap-style hooks），该对象可在 ModelRequest 参数中获取。



## Long-term memory 长期记忆

LangChain代理使用 **LangGraph 持久化** 以实现长期记忆。这是一个更高级的话题，需要懂LangGraph才能使用。

#### 内存存储

LangGraph 将长期记忆存储为 JSON 文档，存储在 store 中。每个内存分别被保存在自定义（类似文件夹）和独立文件（类似文件名）下。命名空间通常包含用户或组织 ID 或其他标签，以便于信息分类。

这种结构使记忆能够进行层级存储。通过内容筛选支持跨命名空间搜索。

```python
from langgraph.store.memory import InMemoryStore

def embed(texts: list[str]) -> list[list[float]]:
    # 模拟嵌入函数，实际使用时请替换为真实的嵌入模型
    return [[1.0, 2.0]]

# 初始化内存存储（生产环境建议使用数据库-backed存储）
store = InMemoryStore(index={"embed": embed, "dims": 2})

# 定义命名空间（用户ID + 应用场景）
namespace = ("用户123", "闲聊场景")
# 存入一条记忆
store.put(
    namespace,
    "记忆ID-001",
    {"偏好规则": ["喜欢简洁直接的表达", "只说中文和Python"],"自定义键": "自定义值"}
)

# 根据ID获取记忆
item = store.get(namespace, "记忆ID-001")
# 在命名空间内搜索记忆（按内容过滤 + 向量相似度排序）
items = store.search(
    namespace, 
    filter={"自定义键": "自定义值"}, 
    query="用户的语言偏好"
)
```



## Retrival 检索

大型语言模型（LLM）功能强大，但它们有两个关键限制：

- **有限的上下文**——他们无法一次性吞入整个语料库。
- **静态知识**——他们的训练数据被冻结在某个时间点。

检索通过在查询时获取相关外部知识来解决这些问题。这就是检索**增强生成（RAG）**的基础：通过上下文特定信息增强LLM的答案。

#### 知识库与检索

**知识库**是检索时使用的文档 / 结构化数据存储库。若已有知识库（如 SQL 数据库、内部文档），无需重建，可直接：

- 作为**工具**连接到 Agentic RAG 代理；
- 查询后将内容作为上下文喂给 LLM。

若需自定义知识库，可用 LangChain 的文档加载器 + 向量存储实现。

**检索**允许LLM在运行时访问相关上下文。但大多数现实应用更进一步：**它们将检索与生成结合**起来，产生扎实、上下文感知的答案。这就是检索**增强生成（RAG）**的核心理念。检索流程成为更广泛系统基础，结合搜索与生成。

典型的检索工作流程如下：

```plaintext
来源（Google Drive/Slack/Notion等）→ 文档加载器 → 文档 → 分割成块 → 转嵌入 → 向量存储
用户查询 → 查询嵌入 → 向量存储 → 检索器 → LLM用检索信息生成回答
```

每个组件都是模块化的：你可以切换加载器、分流器、嵌入或向量存储，而无需重写应用逻辑。

#### 常见 RAG 架构

|      架构       |                       描述                       | 控制 | 灵活性 | 延迟 |       适用场景       |
| :-------------: | :----------------------------------------------: | :--: | :----: | :--: | :------------------: |
| **2-Step RAG**  |       检索总是发生在生成之前。简单且可预测       |  高  |   低   |  快  |   FAQ、文档机器人    |
| **Agentic RAG** | LLM驱动的 agent 决定在推理过程中何时以及如何检索 |  低  |   高   | 可变 |    多工具研究助理    |
| **Hybrid RAG**  |         将两种方法的特点与验证步骤相结合         | 中等 |  中等  | 可变 | 需质量验证的领域问答 |

