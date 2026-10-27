# LLM Agent Cheatsheet · 01: How LLMs Learned to Use Tools (2015 → 2026)

**English** | [中文](README_zh.md)

> Most "awesome agent" lists give you links. This one tells the story: each stage below starts with **the problem people were stuck on**, then shows **which papers solved it**, and ends with **the new problem that solution created**.

![Timeline of LLM tool calling](assets/timeline_en.png)

📄 **Cheatsheet (4-page PDF, Chinese):** [cheatsheet_zh.pdf](cheatsheet_zh.pdf) — formulas, formats, key numbers, self-test questions.

## TL;DR

**Retrieval (tools wired in) → learn to use one tool (WebGPT) → route between many tools (MRKL) → interleave thinking and acting (ReAct) → learn *when* to call a tool on your own (Toolformer) → scale to thousands of APIs (ToolLLM) → standard interfaces (function calling, MCP) → agents that build tools and act in code → RL-trained models that call tools while they think.**


## Contents

| Stage | Years | Question it answered |
|---|---|---|
| [0. Prehistory](#stage-0--prehistory-modules-and-retrieval-20152020) | 2015–2020 | Can a model draw on something outside its weights? |
| [1. One tool](#stage-1--learning-to-use-one-tool-20212022) | 2021–2022 | Can a model operate a tool over several steps? |
| [2. Many tools](#stage-2--the-llm-as-a-router-2022) | 2022 | How do we plug in many tools at once? |
| [3. Code as a tool](#stage-3--code-as-the-tool-2022) | 2022 | How do we stop the model from doing arithmetic in its head? |
| [4. Think + act](#stage-4--interleaving-reasoning-and-acting-2022) | 2022 | How should tool calls fit into reasoning? |
| [5. Self-taught](#stage-5--teaching-itself-when-to-call-2023) | 2023 | Can a model learn *when* to call a tool without labels? |
| [6. Models as tools](#stage-6--llms-that-direct-other-models-2023) | 2023 | Can the LLM direct other AI models? |
| [7. Scale](#stage-7--thousands-of-apis-2023) | 2023 | How do we pick from thousands of APIs? |
| [8. Standards](#stage-8--products-and-standards-20232024) | 2023–2024 | How do we make tool use a reliable product feature? |
| [9. Self-checking](#stage-9--using-tools-to-check-yourself-2023) | 2023 | Can tools help a model catch its own mistakes? |
| [10. Tool makers](#stage-10--agents-that-make-tools-2023) | 2023 | What if the right tool doesn't exist yet? |
| [11. Code as action](#stage-11--code-as-action-and-agent-friendly-interfaces-2024) | 2024 | Is JSON the best way for an agent to act? |
| [12. Benchmarks](#stage-12--how-do-we-measure-it-20232024) | 2023–2024 | How do we measure all this? |
| [13. Security](#stage-13--tools-open-a-new-attack-surface-20232024) | 2023–2024 | What can go wrong when tools return untrusted text? |
| [14. 2025–2026](#stage-14--20252026-rl-and-tools-inside-reasoning) | 2025–2026 | Can we *train* models to use tools while they think? |

---

## Stage 0 · Prehistory: modules and retrieval (2015–2020)

**Problem:** a language model only knows what is stored in its weights.

| Paper | First release | Org | What it did |
|---|---|---|---|
| [Neural Module Networks](https://arxiv.org/abs/1511.02799) | 2015-11 | UC Berkeley | Assembles small neural modules into a network built for each question (visual QA). An early version of "modular reasoning." |
| [REALM](https://arxiv.org/abs/2002.08909) | 2020-02 | Google Research | Pre-trains a language model together with a learned retriever over Wikipedia. |
| [RAG](https://arxiv.org/abs/2005.11401) | 2020-05 | Facebook AI / UCL | Pairs a generator with a dense document index; retrieval becomes the first widely used "tool." |

**What changed:** the model can look things up. **What was still missing:** the retriever is wired in permanently. The model never *decides* to use a tool.

## Stage 1 · Learning to use one tool (2021–2022)

**Problem:** can a model operate a real tool on its own, over several steps?

| Paper | First release | Org | What it did |
|---|---|---|---|
| [WebGPT](https://arxiv.org/abs/2112.09332) | 2021-12 | OpenAI | Fine-tunes GPT-3 to use a text-only browser (search, click, scroll, quote) and write answers with citations. Trained by imitating human demonstrations, then picking the best of *n* answers with a reward model. On ELI5, its answers were preferred over human demonstrators' 56% of the time and over top Reddit answers 69% of the time. |
| [LaMDA](https://arxiv.org/abs/2201.08239) | 2022-01 | Google | A dialogue model fine-tuned to consult a "toolset" (retrieval system, translator, calculator) to ground its answers. |
| [Internet-augmented LMs via few-shot prompting](https://arxiv.org/abs/2203.05115) | 2022-03 | DeepMind | No fine-tuning: few-shot prompts let a model answer from Google Search results. |

**What changed:** models can drive one tool through many steps. **Cost:** expensive human demonstrations, one tool at a time.

## Stage 2 · The LLM as a router (2022)

**Problem:** real assistants need many tools (calculators, databases, APIs). How do we connect them all?

| Paper | First release | Org | What it did |
|---|---|---|---|
| [MRKL Systems](https://arxiv.org/abs/2205.00445) | 2022-05 | AI21 Labs | The LLM acts as a **router** that sends each request to a neural or symbolic expert module. Includes an arithmetic case study (Jurassic-X). |
| [TALM](https://arxiv.org/abs/2205.12255) | 2022-05 | Google | A text-to-text tool interface plus iterative "self-play" that grows tool-use training data from a handful of demonstrations. |

**What changed:** "LLM + pluggable tools" becomes a system design, not a one-off.

## Stage 3 · Code as the tool (2022)

**Problem:** LLMs make arithmetic slips even when their reasoning is right.

| Paper | First release | Org | What it did |
|---|---|---|---|
| [PAL](https://arxiv.org/abs/2211.10435) | 2022-11 | CMU | The model writes Python and an interpreter computes the answer. [Code](https://github.com/reasoning-machines/pal) |
| [Program of Thoughts](https://arxiv.org/abs/2211.12588) | 2022-11 | Waterloo / Vector Institute | Separates reasoning (written as a program) from computation (executed externally). [Code](https://github.com/wenhuchen/Program-of-Thoughts) |

**What changed:** the interpreter becomes the most general tool of all.

## Stage 4 · Interleaving reasoning and acting (2022)

**Problem:** earlier systems either reasoned *or* called tools. Real problems need both, in alternation.

| Paper | First release | Org | What it did |
|---|---|---|---|
| [ReAct](https://arxiv.org/abs/2210.03629) | 2022-10 | Princeton / Google | The **Thought → Action → Observation** loop: think a step, act, read the result, think again. Nearly every agent framework since builds on it. [Code](https://github.com/ysymyth/ReAct) |
| [Self-Ask](https://arxiv.org/abs/2210.03350) (paper: *Measuring and Narrowing the Compositionality Gap in Language Models*) | 2022-10 | UW / MosaicML | The model asks itself follow-up questions that a search engine can answer, then combines them. [Code](https://github.com/ofirpress/self-ask) |
| [IRCoT](https://arxiv.org/abs/2212.10509) | 2022-12 | Stony Brook | Alternates retrieval and chain-of-thought, so each reasoning step guides the next search. [Code](https://github.com/stonybrooknlp/ircot) |

```
Thought: I need the population of the city where X was born.
Action:  search("X birthplace")
Observation: X was born in Lyon.
Thought: Now find Lyon's population.
Action:  search("Lyon population")
...
```

**What changed:** tool calls live *inside* the reasoning trace. This is the shape of a modern agent.

## Stage 5 · Teaching itself when to call (2023)

**Problem:** labeling where tool calls belong is expensive. Can a model learn this itself?

| Paper | First release | Org | What it did |
|---|---|---|---|
| [Toolformer](https://arxiv.org/abs/2302.04761) | 2023-02 | Meta AI | The model samples candidate API calls inside ordinary text and keeps a call only if inserting it **plus its result** lowers the loss on the following tokens by a threshold. Tools: calculator, QA system, two search engines, translator, calendar. Base model: GPT-J (6.7B). |

**What changed:** "when should I call a tool?" can be learned self-supervised, at scale.

## Stage 6 · LLMs that direct other models (2023)

**Problem:** vision, speech, and other specialist models already exist. Can an LLM orchestrate them?

| Paper | First release | Org | What it did |
|---|---|---|---|
| [Visual ChatGPT](https://arxiv.org/abs/2303.04671) | 2023-03 | Microsoft Research Asia | Connects ChatGPT to visual foundation models for multi-step image tasks. [Code](https://github.com/microsoft/visual-chatgpt) |
| [ViperGPT](https://arxiv.org/abs/2303.08128) | 2023-03 | Columbia | A code LLM writes Python that composes vision modules. [Code](https://github.com/cvlab-columbia/viper) |
| [HuggingGPT](https://arxiv.org/abs/2303.17580) | 2023-03 | Zhejiang Univ. / MSRA | The LLM plans, picks Hugging Face models, runs them and summarizes. [Code](https://github.com/microsoft/JARVIS) |
| [Chameleon](https://arxiv.org/abs/2304.09842) | 2023-04 | UCLA | An LLM planner composes tools (vision, search, Python) into programs. [Code](https://github.com/lupantech/chameleon-llm) |

**What changed:** "tools" now include other AI models.

## Stage 7 · Thousands of APIs (2023)

**Problem:** with thousands of tools, the hard part is choosing the right one and filling in arguments without inventing them.

| Paper | First release | Org | What it did |
|---|---|---|---|
| [API-Bank](https://arxiv.org/abs/2304.08244) | 2023-04 | Alibaba DAMO | One of the first runnable tool-use benchmarks (73 APIs, 314 dialogues). |
| [Tool Learning with Foundation Models](https://arxiv.org/abs/2304.08354) | 2023-04 | Tsinghua | Survey and general framework for tool learning. |
| [Gorilla](https://arxiv.org/abs/2305.15334) | 2023-05 | UC Berkeley | LLaMA-7B fine-tuned with **retriever-aware training** on API documentation; introduces APIBench. [Code](https://github.com/ShishirPatil/gorilla) |
| [ToolLLM](https://arxiv.org/abs/2307.16789) | 2023-07 | Tsinghua | ToolBench: **16,464 real RapidAPI APIs** in 49 categories, plus a depth-first tree search (DFSDT) for finding solution paths. [Code](https://github.com/OpenBMB/ToolBench) |

**What changed:** tool retrieval and argument accuracy become research problems of their own.

## Stage 8 · Products and standards (2023–2024)

**Problem:** every system had its own ad hoc format for tool calls.

| Release | Date | Org | What it did |
|---|---|---|---|
| [ChatGPT plugins](https://openai.com/index/chatgpt-plugins/) | 2023-03 | OpenAI | Third-party tools inside ChatGPT (browsing, code interpreter, plugins). |
| [Function calling](https://openai.com/index/function-calling-and-other-api-updates/) | 2023-06 | OpenAI | Developers describe functions with JSON Schema; the model returns structured JSON arguments. This became the industry's default interface. |
| [Model Context Protocol (MCP)](https://www.anthropic.com/news/model-context-protocol) | 2024-11 | Anthropic | An open protocol for connecting models to tools and data sources, with a spec, SDKs and reference servers. [Spec](https://modelcontextprotocol.io) |

**What changed:** tool calling turns from a research trick into a standard model capability.

## Stage 9 · Using tools to check yourself (2023)

**Problem:** agents fail and repeat the same mistakes.

| Paper | First release | Org | What it did |
|---|---|---|---|
| [Reflexion](https://arxiv.org/abs/2303.11366) | 2023-03 | Northeastern | After a failure, the agent writes a verbal reflection into memory and tries again, without updating weights. [Code](https://github.com/noahshinn/reflexion) |
| [CRITIC](https://arxiv.org/abs/2305.11738) | 2023-05 | Tsinghua / MSRA | Uses tools (search, code execution) to verify and revise its own outputs. |
| [Language Agent Tree Search](https://arxiv.org/abs/2310.04406) | 2023-10 | UIUC | Combines Monte Carlo tree search with ReAct-style acting and self-reflection. [Code](https://github.com/lapisrocks/LanguageAgentTreeSearch) |

**What changed:** tools are used not only to *do* things but to *check* things.

## Stage 10 · Agents that make tools (2023)

**Problem:** sometimes the right tool doesn't exist.

| Paper | First release | Org | What it did |
|---|---|---|---|
| [LATM: LLMs as Tool Makers](https://arxiv.org/abs/2305.17126) | 2023-05 | Princeton / Google DeepMind | A strong model writes reusable Python tools that a cheaper model then uses. [Code](https://github.com/ctlllll/LLM-ToolMaker) |
| [CREATOR](https://arxiv.org/abs/2305.14318) | 2023-05 | Tsinghua | The model creates a tool as code, then executes it to solve the problem. [Code](https://github.com/qiancheng0/CREATOR) |
| [Voyager](https://arxiv.org/abs/2305.16291) | 2023-05 | NVIDIA / Caltech | A Minecraft agent that stores working code in a growing **skill library**. [Code](https://github.com/MineDojo/Voyager) |

**What changed:** agents begin to accumulate capabilities over time. This is the seed of "self-evolving" agents.

## Stage 11 · Code as action and agent-friendly interfaces (2024)

**Problem:** JSON tool calls are rigid; one call does one thing.

| Paper | First release | Org | What it did |
|---|---|---|---|
| [CodeAct (Executable Code Actions Elicit Better LLM Agents)](https://arxiv.org/abs/2402.01030) | 2024-02 | UIUC | Makes executable Python the agent's action space; up to 20% higher success than JSON/text actions across 17 LLMs. [Code](https://github.com/xingyaoww/code-act) |
| [SWE-agent](https://arxiv.org/abs/2405.15793) | 2024-05 | Princeton | Designs an **agent-computer interface (ACI)**, commands built for agents to navigate and edit code repositories. [Code](https://github.com/SWE-agent/SWE-agent) |

**What changed:** the focus shifts from "how does the model call tools" to "**how should tools be designed for the model**."

## Stage 12 · How do we measure it? (2023–2024)

| Benchmark | First release | Org | What it tests |
|---|---|---|---|
| [WebArena](https://arxiv.org/abs/2307.13854) | 2023-07 | CMU | Realistic self-hosted websites; best GPT-4 agent 14.41% vs. humans 78.24%. |
| [AgentBench](https://arxiv.org/abs/2308.03688) | 2023-08 | Tsinghua | LLMs as agents across 8 environments. |
| [GAIA](https://arxiv.org/abs/2311.12983) | 2023-11 | Meta / Hugging Face | 466 real-world assistant questions; humans 92% vs. GPT-4 with plugins 15%. |
| [Berkeley Function Calling Leaderboard](https://gorilla.cs.berkeley.edu/leaderboard.html) | 2024-02 | UC Berkeley | Function-calling accuracy via AST matching and execution; later versions add multi-turn and agentic tests. |
| [τ-bench](https://arxiv.org/abs/2406.12045) | 2024-06 | Sierra | A tool agent, a simulated user, and business policies; introduces the pass^k reliability metric. [Code](https://github.com/sierra-research/tau-bench) |

## Stage 13 · Tools open a new attack surface (2023–2024)

**Problem:** tool outputs (web pages, emails, files) are untrusted text, and the model reads them as instructions.

| Paper | First release | Org | What it did |
|---|---|---|---|
| [Indirect Prompt Injection (Not what you've signed up for)](https://arxiv.org/abs/2302.12173) | 2023-02 | Saarland Univ. / sequire | Shows that instructions hidden in retrieved content can hijack LLM-integrated apps. |
| [InjecAgent](https://arxiv.org/abs/2403.02691) | 2024-03 | UIUC | 1,054 test cases of indirect injection against tool-using agents. [Code](https://github.com/uiuc-kang-lab/InjecAgent) |
| [AgentDojo](https://arxiv.org/abs/2406.13352) | 2024-06 | ETH Zurich | An extensible environment for injection attacks and defenses (97 tasks, 629 security cases). [Code](https://github.com/ethz-spylab/agentdojo) |

**What changed:** the more an agent can do, the more an attacker can make it do.

## Stage 14 · 2025–2026: RL and tools inside reasoning

**Problem:** prompting and imitation only go so far. Can we *train* a model, with rewards, to decide when and how to call tools while it reasons?

### 14a. Reinforcement learning for tool use ("agentic RL")

| Paper | First release | Org | What it did |
|---|---|---|---|
| [R1-Searcher](https://arxiv.org/abs/2503.05592) | 2025-03 | Renmin Univ. of China | Two-stage RL with an outcome-only reward teaches a model to call a search engine during reasoning. |
| [Search-R1](https://arxiv.org/abs/2503.09516) | 2025-03 | UIUC / UMass Amherst / Google Cloud | RL with a simple outcome reward teaches a model to interleave several search queries with step-by-step reasoning. |
| [ToRL](https://arxiv.org/abs/2503.23383) | 2025-03 | SJTU / GAIR | RL straight from base models with a code interpreter; the 7B model reaches 43.3% on AIME24. |
| [ReTool](https://arxiv.org/abs/2504.11536) | 2025-04 | ByteDance Seed | Interleaves live code execution with long reasoning; a 32B model reaches 67% on AIME after 400 RL steps, vs. 40% for text-only RL. |
| [ToolRL](https://arxiv.org/abs/2504.13958) | 2025-04 | UIUC | First systematic study of reward design for tool selection and use. |
| [ARPO (Agentic Reinforced Policy Optimization)](https://arxiv.org/abs/2507.19849) | 2025-07 | Renmin Univ. et al. | Branches more at the uncertain step right after each tool call; matches baselines with about half the tool calls. |
| [The Landscape of Agentic RL for LLMs](https://arxiv.org/abs/2509.02547) | 2025-09 | Multi-institution | Survey that defines "agentic RL" as a field. |
| [ToolVerse](https://arxiv.org/abs/2607.15660) | 2026-07 | — | Builds RL environments automatically from hundreds of real MCP servers, with long-horizon tasks from tool-dependency graphs. |

### 14b. Reasoning models that call tools while they think

| Release | Date | Org | What changed |
|---|---|---|---|
| [OpenAI deep research](https://openai.com/index/introducing-deep-research/) | 2025-02 | OpenAI | A version of o3 trained end-to-end with RL on browsing and Python tasks, writing cited research reports. |
| [o3 and o4-mini](https://openai.com/index/introducing-o3-and-o4-mini/) | 2025-04 | OpenAI | Reasoning models that use and combine every ChatGPT tool (search, Python, images) inside their chain of thought. |
| [Claude 4](https://www.anthropic.com/news/claude-4) | 2025-05 | Anthropic | Extended thinking with tool use and "interleaved thinking" between tool calls. |
| [Tongyi DeepResearch](https://arxiv.org/abs/2510.24701) | 2025-10 | Alibaba Tongyi Lab | Open-source 30.5B-parameter MoE (3.3B active) agent for long-horizon web research. |
| [DeepSeek-V3.2](https://api-docs.deepseek.com/news/news251201) | 2025-12 | DeepSeek | Integrates thinking directly into tool use; agent data synthesized across 1,800+ environments. |

### 14c. The interface layer standardizes

| Release | Date | Org | What changed |
|---|---|---|---|
| [Agent2Agent (A2A)](https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/) | 2025-04 | Google | An open protocol for agents to talk to other agents, positioned as a complement to MCP. Moved to the Linux Foundation in 2025-06. |
| [Remote MCP in the Responses API](https://openai.com/index/new-tools-and-features-in-the-responses-api/) | 2025-05 | OpenAI | OpenAI adopts MCP and joins its steering committee. |
| [Agent Skills](https://claude.com/resources/articles/skills) | 2025-10 | Anthropic | Folders of instructions, scripts and resources that load only when relevant; published as an open standard in 2025-12. |
| [Agentic AI Foundation](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation) | 2025-12 | Anthropic, OpenAI, Block / Linux Foundation | MCP is donated to vendor-neutral governance; by then it had 10k+ public servers and support in ChatGPT, Gemini, Copilot, VS Code and Cursor. |
| [MCP Apps](https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/) | 2026-01 | MCP project | First official MCP extension: tools can return interactive UIs. |
| [MCP 2026-07-28 spec](https://modelcontextprotocol.io/specification/2026-07-28/changelog) | 2026-07 | MCP project | A stateless redesign that drops sessions and the initialize handshake. |

### 14d. Too many tools: context becomes the bottleneck

| Release | Date | Org | What changed |
|---|---|---|---|
| [Code execution with MCP](https://www.anthropic.com/engineering/code-execution-with-mcp) | 2025-11 | Anthropic | Agents call MCP tools by writing code instead of loading every tool definition; one example drops from about 150k to 2k tokens. CodeAct's idea, at production scale. |
| [Advanced tool use](https://www.anthropic.com/engineering/advanced-tool-use) | 2025-11 | Anthropic | A tool-search tool that loads definitions on demand, plus programmatic tool calling. |
| [GPT-5.4](https://openai.com/index/introducing-gpt-5-4/) | 2026-03 | OpenAI | On-demand tool search (47% fewer tokens across 36 MCP servers at the same accuracy) and native computer use. |

### 14e. Computer-use agents catch up with humans

| Release | Date | Org | OSWorld |
|---|---|---|---|
| [Computer-Using Agent (Operator)](https://openai.com/index/computer-using-agent/) | 2025-01 | OpenAI | 38.1% (human 72.4%) |
| [Claude Sonnet 4.5](https://www.anthropic.com/news/claude-sonnet-4-5) | 2025-09 | Anthropic | 61.4% |
| [GPT-5.4](https://openai.com/index/introducing-gpt-5-4/) | 2026-03 | OpenAI | 75.0% on OSWorld-Verified, above the 72.4% human baseline |

### 14f. New benchmarks

| Benchmark | First release | Org | What it tests |
|---|---|---|---|
| [BrowseComp](https://arxiv.org/abs/2504.12516) | 2025-04 | OpenAI | 1,266 hard-to-find, easy-to-check questions for persistent web browsing. |
| [τ²-bench](https://arxiv.org/abs/2506.07982) | 2025-06 | Sierra et al. | The user can act on the environment too, so the agent must guide the user. |
| [MCP-Universe](https://arxiv.org/abs/2508.14704) | 2025-08 | Salesforce AI Research | 11 real MCP servers; the best model at release scored 43.7%. |
| [Toolathlon](https://arxiv.org/abs/2510.25726) | 2025-10 | Multi-institution | 32 apps, 604 tools, 108 long-horizon tasks; best model at release 38.6%. |
| [Terminal-Bench](https://arxiv.org/abs/2601.11868) | 2026-01 | Stanford / Laude et al. | 89 hard command-line tasks, each in its own container. |

### 14g. Security gets harder

| Work | Date | Org | What it showed |
|---|---|---|---|
| [CaMeL: Defeating Prompt Injections by Design](https://arxiv.org/abs/2503.18813) | 2025-03 | Google / Google DeepMind / ETH Zurich | Separates control flow from data flow; solves 77% of AgentDojo tasks with provable security. |
| [MCP tool poisoning](https://invariantlabs.ai/blog/mcp-security-notification-tool-poisoning-attacks) | 2025-04 | Invariant Labs | Hidden instructions in tool descriptions, and descriptions that change after the user approves them. |
| [Design Patterns for Securing LLM Agents](https://arxiv.org/abs/2506.08837) | 2025-06 | ETH / IBM / Google / Microsoft et al. | A catalogue of architectures with provable injection resistance and their utility costs. |
| [The Attacker Moves Second](https://arxiv.org/abs/2510.09023) | 2025-10 | OpenAI, Anthropic, Google DeepMind, ETH et al. | Adaptive attacks break 12 published defenses, mostly with over 90% success. |
| [Malicious agent skills on ClawHub](https://unit42.paloaltonetworks.com/openclaw-ai-supply-chain-risk/) | 2026-02 | Palo Alto Unit 42 et al. | Hundreds of malicious "skills" in an agent skill marketplace: the supply-chain problem reaches agents. |

**What changed:** tool use is now something models are *trained* to do with rewards, inside their reasoning, through shared protocols. The open problems are cost (too many tools in context), long-horizon reliability, and security.

---

## Start here: 6 papers for beginners

If you only read six, read these in order:

1. [WebGPT](https://arxiv.org/abs/2112.09332): the first full demo of multi-step tool use.
2. [ReAct](https://arxiv.org/abs/2210.03629): the Thought → Action → Observation loop.
3. [Toolformer](https://arxiv.org/abs/2302.04761): learning when to call a tool without labels.
4. [ToolLLM](https://arxiv.org/abs/2307.16789): what changes at the scale of thousands of APIs.
5. [Executable Code Actions (CodeAct)](https://arxiv.org/abs/2402.01030): why code beats JSON as an action format.
6. [Search-R1](https://arxiv.org/abs/2503.09516): training tool use with reinforcement learning.

## Further reading

- [Tool Learning with Foundation Models](https://arxiv.org/abs/2304.08354) (survey, 2023)
- [The Landscape of Agentic Reinforcement Learning for LLMs](https://arxiv.org/abs/2509.02547) (survey, 2025)
- [Agentic Tool Use in Large Language Models](https://arxiv.org/abs/2604.00835) (survey, 2026): frames the field as prompting → supervised tool learning → reward-driven tool policies.

## Notes

- **Dates** are the month of the first public release: arXiv v1 for papers, the official announcement for products. Some papers changed titles after v1; the links point to the current versions.
- Checked against arXiv and official sources in **October 2026**. This field moves fast. If you spot an error or a missing milestone, please open an issue or a pull request.
- Organizations listed are the first author's main affiliations, shortened.

## License

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Share and adapt freely with attribution.

