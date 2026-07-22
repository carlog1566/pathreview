## Week 7 — Issue selection

**Issue link:** [https://github.com/ascherj/pathreview/issues/32](https://github.com/ascherj/pathreview/issues/32)

**Issue title:** Implement a caching layer for repeated identical portfolio queries

**Tier:** [ ] Tier 1  [x] Tier 2  [ ] Tier 3

**Problem summary:**
Currently, every time a user requests a portfolio review, the system reruns the entire ingestion, RAG retrieval, and LLM generation pipeline, even if the portfolio content has not changed. This results in unnecessary processing time and repeated LLM calls for identical inputs. The fix should add a caching mechanism in the review generation workflow, using a hash of the profile's content to detect unchanged portfolios and return a previously generated review instead of regenerating it. This primarily affects the review processing logic in core/services/review_service.py and the RAG generation pipeline in rag/generator/review_generator.py. Implementing this cache will improve performance, reduce API costs, and provide faster response times for users submitting unchanged portfolios


**Branch name:** feat/32-caching-layer

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger