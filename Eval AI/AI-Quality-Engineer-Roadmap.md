# Roadmap: QA / SDET → AI Quality Engineer (LLM Evaluation)

| | |
|---|---|
| **Starting point** | 4.6 years in QA: manual testing, API automation (Rest Assured), UI automation (Selenium), basic APM |
| **Target roles** | AI Quality Engineer · LLM Evaluation Engineer · GenAI QA Engineer · SDET – AI |
| **Duration** | ~24 weeks (6 months) at **8–10 hours/week** alongside a full-time job |
| **Weekly rhythm** | 2 weekday sessions × 1.5 h + 1 weekend block of 4–5 h |
| **Cost** | Nearly everything is free. For LLM calls, run a model locally with Ollama (free) or add a small prepaid credit to an LLM API account |

---

## How this roadmap works

- **One project grows through every phase.** This project is called the *capstone*: a small RAG chatbot plus its full test and eval suite. Nothing is throwaway. By week 24 it becomes the portfolio piece.
- Every phase has a **goal**, **topics**, **hands-on work**, a **"Done when"** exit check, and **resources**.
- Don't move on until the "Done when" box is ticked. If a phase runs over, that's fine. The timeline has buffer built in.

---

## Timeline at a glance

| Phase | Weeks | Focus | Key output |
|---|---|---|---|
| 0 | Week 0 | Setup | Repo + working local LLM |
| 1 | 1–4 | Python for testers | Rest Assured tests ported to pytest |
| 2 | 5–8 | LLM fundamentals | Capstone v0: a working RAG chatbot |
| 3 | 9–11 | Thinking in evals | Golden dataset + eval strategy doc |
| 4 | 12–15 | Eval frameworks | Automated eval suite (DeepEval, Ragas, promptfoo) |
| 5 | 16–17 | Evals in CI/CD | Pipeline that fails on quality regressions |
| 6 | 18–19 | LLM observability | Tracing + latency/token/cost dashboard |
| 7 | 20–21 | Safety & red-teaming | Adversarial test suite + red-team report |
| 8 | 22–24 | Portfolio & job readiness | Polished repo, resume, applications |

---

## Where existing skills carry over

| Already has | Where it shows up in this roadmap |
|---|---|
| **Rest Assured / API testing** | Calling LLM APIs, asserting on responses, testing RAG endpoints (Phases 1, 2, 4) |
| **Manual testing / test design** | Building golden datasets, edge cases, grading rubrics, error analysis (Phase 3) |
| **Selenium / UI automation** | Testing AI features end-to-end through a chat UI (Phase 4 stretch goal) |
| **APM** | Tracing LLM calls and monitoring latency, tokens and cost (Phase 6) |
| **CI pipelines** | Running evals as quality gates on every change (Phase 5) |

---

## Phase 0 — Setup (Week 0)

**Goal:** Get the tools working so week 1 starts with learning, not installing.

- Install Python 3.12+, VS Code (with the Python extension) and Git.
- Create a GitHub repo, e.g. `rag-eval-lab`, with a `LEARNING_LOG.md`. Write 3–5 lines after every session about what was learned and what was confusing.
- Install Ollama and pull a small open model, *or* create an API key with an LLM provider.

✅ **Done when:** the repo exists, and a local model (or API) answers a question from the terminal.

**Resources**
- [Ollama](https://ollama.com): runs open models locally, free
- [Ollama documentation](https://docs.ollama.com)
- [Anthropic API fundamentals course (GitHub)](https://github.com/anthropics/courses/tree/master/anthropic_api_fundamentals): if using a hosted API

---

## Phase 1 — Python for Testers (Weeks 1–4)

[[Phase 1 Notes]]


**Goal:** Be as comfortable writing test code in Python as in Java. Most AI eval tooling is Python-first.

**Topics**
- Python syntax, data types, functions, classes, list/dict comprehensions
- Virtual environments and `pip`; reading and writing JSON and files
- `pytest`: fixtures, `@pytest.mark.parametrize`, markers, `conftest.py`
- `requests` for HTTP calls, which plays the role Rest Assured plays in Java

**Hands-on**
| Week | Work |
|---|---|
| 1–2 | Python basics through the official tutorial. Solve small exercises in the repo |
| 3 | pytest fundamentals: fixtures, parametrize, markers |
| 4 | **Port 10–15 existing Rest Assured–style tests** against a public API into pytest + requests |

> 💡 Skip the 40-hour "complete Python" courses. Learning by porting tests he already understands is faster and sticks better.

✅ **Done when:** the ported suite passes with one command (`pytest`) and uses fixtures and parametrization.

**Resources**
- [The Python Tutorial (official)](https://docs.python.org/3/tutorial/)
- [Real Python: pytest Tutorial](https://realpython.com/pytest-python-testing/)
- [pytest: Get Started](https://docs.pytest.org/en/stable/getting-started.html)
- [Requests: Quickstart](https://requests.readthedocs.io/en/latest/user/quickstart/)

---

## Phase 2 — LLM Fundamentals (Weeks 5–8)

[[Phase 2 Notes]]

**Goal:** Understand the thing being tested well enough to predict how it will fail.

**Topics**
- How LLMs work at a high level: tokens, context window, temperature and sampling (this is why outputs vary run to run)
- Prompting: system prompts, few-shot examples, asking for structured JSON output
- Embeddings and vector search
- **RAG (Retrieval-Augmented Generation):** retrieve relevant documents, then generate an answer from them
- Tool use and agents (concepts only for now)

**Hands-on**
| Week | Work |
|---|---|
| 5 | Watch the Karpathy talk. Make first LLM calls from Python |
| 6 | **Variance experiment:** run the same prompt 10× at temperature 0 and 10× at a high temperature, and log the differences. This is the key insight for a tester |
| 7–8 | **Start the capstone:** build a small RAG chatbot over ~20 public documents (a product FAQ, public docs, etc.). It should return its answer *and* the chunks it retrieved |

✅ **Done when:** capstone v0 answers questions from the documents and shows which chunks it used.

**Resources**
- [Andrej Karpathy: Intro to Large Language Models (1-hour talk)](https://www.youtube.com/watch?v=zjkBMFhNj_g)
- [Hugging Face LLM Course](https://huggingface.co/learn/llm-course/chapter1/1): free; chapter 1 is enough at this stage
- [Anthropic Prompt Engineering Interactive Tutorial](https://github.com/anthropics/courses/tree/master/prompt_engineering_interactive_tutorial)
- [LangChain: Build a semantic search engine](https://docs.langchain.com/oss/python/langchain/knowledge-base): covers chunking, embeddings, vector stores and retrieval, plus a minimal RAG example to base the capstone on
- [Anthropic Tool Use course](https://github.com/anthropics/courses/tree/master/tool_use): optional, for agents

---

## Phase 3 — Thinking in Evals (Weeks 9–11)

[[Phase 3 Notes]]

**Goal:** Make the core mindset shift from "pass/fail assertions" to "measured quality". **This is the most important phase.**

**Topics**
- Why exact-match assertions break on LLM output
- The three ways to grade, and when to use each:
  - **Code-based:** string contains, regex, JSON schema, length. Fast and cheap; use wherever possible
  - **LLM-as-judge:** a second model grades against a rubric. Flexible, but it must be validated
  - **Human grading:** the gold standard, but slow; use it to calibrate the other two
- **Golden datasets:** happy paths, edge cases, ambiguous questions, out-of-scope questions, adversarial inputs
- **Error analysis:** read lots of outputs, sort failures into categories, and fix the biggest category first
- Thresholds and pass rates are *product decisions*. A 100% pass rate is not the goal
- Checking whether an LLM judge agrees with human labels before trusting it

**Hands-on**
1. Build a **golden set of 40–60 questions** for the capstone, tagged by category.
2. Run the bot and **manually grade every answer** in a spreadsheet: good/bad plus a one-line reason.
3. Write a **one-page eval strategy**: what "good" means for this bot, which metrics, which grading method per metric, and the thresholds.

> 💡 This is where manual testing experience pays off directly. Test design, edge-case thinking and clear defect descriptions are exactly what this work needs.

✅ **Done when:** the golden set, the graded spreadsheet and the strategy doc are all committed to the repo.

**Resources**
- [Hamel Husain: Your AI Product Needs Evals](https://hamel.dev/blog/posts/evals/): the best single article on this topic
- [Eugene Yan: Task-Specific LLM Evals that Do & Don't Work](https://eugeneyan.com/writing/evals/)
- [Anthropic docs: Define success criteria and build evaluations](https://platform.claude.com/docs/en/test-and-evaluate/develop-tests)
- [Anthropic Prompt Evaluations course (GitHub)](https://github.com/anthropics/courses/tree/master/prompt_evaluations)

---

## Phase 4 — Eval Frameworks, Hands-on (Weeks 12–15)

[[Phase 4 Notes]]

**Goal:** Automate everything from Phase 3 with the tools companies actually use.

**Hands-on**
| Week | Tool | Work |
|---|---|---|
| 12–13 | **DeepEval** | Plugs into pytest, so it will feel familiar. Turn the golden set into test cases. Use answer relevancy, faithfulness, and a custom G-Eval metric built from the Phase 3 rubric |
| 14 | **Ragas** | RAG-specific metrics. Evaluate the **retriever** (context precision/recall) separately from the **generator** (faithfulness) |
| 15 | **promptfoo** | Config-driven (YAML). Compare two prompt versions or two models side by side and produce a regression report |

**Stretch goal (uses Selenium skills):** put a simple chat UI on the bot, drive it with Selenium or Playwright, and feed the captured responses into the eval suite. This is true end-to-end testing of an AI feature.

✅ **Done when:**
- `tests/evals/` runs DeepEval over the full golden set
- a promptfoo report compares two prompt versions
- he can explain, for any failure, whether it came from **retrieval** or **generation**

**Resources**
- [DeepEval: 5-min Quickstart](https://deepeval.com/docs/getting-started)
- [DeepEval: RAG Evaluation Quickstart](https://deepeval.com/docs/getting-started-rag)
- [Ragas: Documentation home](https://docs.ragas.io/en/stable/)
- [Ragas: Quick Start](https://docs.ragas.io/en/stable/getstarted/quickstart/)
- [Ragas: Evaluate a simple RAG system](https://docs.ragas.io/en/stable/tutorials/rag/)
- [Ragas: Align an LLM as a Judge](https://docs.ragas.io/en/stable/howtos/applications/align-llm-as-judge/)
- [promptfoo: Intro](https://www.promptfoo.dev/docs/intro/)
- [promptfoo: Getting started with evals](https://www.promptfoo.dev/docs/getting-started/)

---

## Phase 5 — Evals in CI/CD (Weeks 16–17)

[[Phase 5 Notes]]


**Goal:** Make evals run like a regression suite, automatically, on every change.

**Topics**
- **Tiered evals:** fast code-based checks on every commit; LLM-judged evals on pull requests or nightly
- Controlling cost and time: a small smoke set vs. the full set, and caching responses
- Failing the build when a metric drops below its threshold
- Tracking results over time to spot gradual drift

**Hands-on**
- A GitHub Actions workflow that runs pytest + DeepEval on every push
- The promptfoo GitHub Action on pull requests that touch prompt files
- API keys stored as repository secrets, never committed

✅ **Done when:** deliberately making the prompt worse turns the pipeline **red**.

**Resources**
- [DeepLearning.AI: Automated Testing for LLMOps](https://www.deeplearning.ai/courses/automated-testing-llmops): about 1 hour, free to enroll at the time of writing; directly on-topic
- [GitHub Docs: Building and testing Python](https://docs.github.com/en/actions/tutorials/build-and-test-code/python)
- [DeepEval: Unit Testing in CI/CD](https://deepeval.com/docs/evaluation-unit-testing-in-ci-cd)
- [promptfoo: Testing Prompts with GitHub Actions](https://www.promptfoo.dev/docs/integrations/github-action/)

---

## Phase 6 — LLM Observability (Weeks 18–19)

[[Phase 6 Notes]]


**Goal:** Apply APM experience to LLM apps. This is a differentiator most QA candidates don't have.

**Topics**
- Traces and spans for LLM calls, retrieval steps and tool calls
- Latency, token usage and **cost per request**
- OpenTelemetry basics; both tools below are built on it
- Online evals: scoring production traces automatically
- **Closing the loop:** turning real failures into new golden test cases

**Hands-on**
- Add tracing to the capstone with **Langfuse** (self-host with Docker, or use Langfuse Cloud) **or** **Arize Phoenix** (runs locally)
- Build a simple dashboard of latency, tokens and cost
- Pick 5 bad traces and add them to the golden set

✅ **Done when:** the README shows a trace screenshot and a dashboard, and the golden set has grown from observed failures.

**Resources**
- [Langfuse: Docs overview](https://langfuse.com/docs)
- [Langfuse: Get started with tracing](https://langfuse.com/docs/observability/get-started)
- [Langfuse: Self-hosting](https://langfuse.com/self-hosting)
- [Arize Phoenix: Documentation](https://arize.com/docs/phoenix)
- [Arize Phoenix: Quickstart overview](https://arize.com/docs/phoenix/get-started)

---

## Phase 7 — Safety & Red-Teaming Basics (Weeks 20–21)

[[Phase 7 Notes]]


**Goal:** Cover the security side of AI testing, which almost every AI QA interview now touches on.

**Topics**
- **OWASP Top 10 for LLM Applications (2025).** Focus on prompt injection, sensitive information disclosure, system prompt leakage and misinformation
- **Indirect prompt injection:** malicious instructions hidden inside RAG documents
- Manual vs. automated red-teaming

**Hands-on**
1. Play Lakera's prompt-hacking game to build intuition for how attacks work.
2. Run a promptfoo red-team scan against the capstone.
3. Add **15–20 adversarial test cases** to the eval suite: prompt injection, PII leakage, off-topic requests, jailbreak attempts.

✅ **Done when:** a red-team report is in the repo and the adversarial suite runs in CI.

**Resources**
- [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/)
- [OWASP LLM01:2025 Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)
- [DeepLearning.AI: Red Teaming LLM Applications](https://www.deeplearning.ai/courses/red-teaming-llm-applications): beginner-friendly, about 1.5 hours
- [promptfoo: Red teaming quickstart](https://www.promptfoo.dev/docs/red-team/quickstart/)
- [Lakera Gandalf: prompt-injection game](https://gandalf.lakera.ai)

---

## Phase 8 — Portfolio & Job Readiness (Weeks 22–24)

[[Phase 8 Notes]]


**Week 22: Polish the capstone**
- README sections: architecture diagram, how to run it, the eval strategy, results tables, and *what failed and how it was fixed*. Hiring managers care most about that last section
- Write a short LinkedIn or blog post titled something like "What I learned testing a RAG chatbot as a QA engineer"

**Week 23: Resume and LinkedIn**
- Retitle the headline, e.g. "SDET | AI Quality & LLM Evaluation"
- Add capstone bullets. Adapt these templates to what was actually built:
  - *Designed a 60+ case golden dataset and automated LLM evaluation suite (DeepEval, Ragas) for a RAG chatbot, separating retrieval and generation failures*
  - *Built CI quality gates in GitHub Actions that block merges when faithfulness or relevancy drops below threshold*
  - *Instrumented LLM tracing (Langfuse/Phoenix) to monitor latency, token usage and cost per request*
  - *Created an adversarial test suite covering prompt injection and data leakage, aligned to the OWASP LLM Top 10*

**Week 24: Interview prep and applications.** He should be able to answer these confidently:
- How do you test something that gives a different answer every time?
- When would you use an LLM-as-judge vs. code-based checks vs. human review? How do you know the judge is reliable?
- A RAG bot gives a wrong answer. How do you find out whether retrieval or generation is at fault?
- How would you stop eval costs from exploding in CI?
- What is prompt injection, and how would you test for it?
- Walk me through your capstone: what broke, and what did you change?

---

## 🚀 The fastest shortcut: go internal

From **Phase 3 onward**, he should ask whether his current company is building any GenAI feature, chatbot or AI-assisted workflow, and **volunteer to own its testing**: the golden set, the eval suite, the red-team pass. Even 2–3 months of real production experience outweighs any certificate, and it can become the headline of his resume.

---

## Adjusting the pace

- **Faster (15+ hrs/week):** about 12–14 weeks. Merge Phases 1+2 and 5+6.
- **Slower (5 hrs/week):** about 9 months. Keep the phase order; just stretch each one.
- **Never skip:** Phases 1, 3, 4 and 5. They form the core of the role. Phases 6 and 7 can be lighter if time is short, but even a basic version is a strong differentiator.

---

## Milestone checklist

- [ ] Phase 0: Repo created, local LLM or API working
- [ ] Phase 1: Rest Assured tests ported to pytest + requests
- [ ] Phase 2: Capstone v0, a RAG chatbot returning answers and sources
- [ ] Phase 3: Golden dataset (40–60 cases), graded spreadsheet, eval strategy doc
- [ ] Phase 4: DeepEval suite, Ragas retriever/generator split, promptfoo comparison
- [ ] Phase 5: CI pipeline that fails on quality regressions
- [ ] Phase 6: Tracing plus a latency/token/cost dashboard; golden set grown from failures
- [ ] Phase 7: Adversarial test suite and red-team report
- [ ] Phase 8: Polished README, published post, updated resume, applications sent

---

*Tool and course details were checked in October 2026. AI tooling changes quickly, so if a link moves, search the tool name plus "docs".*
