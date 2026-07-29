## Week 7 — Issue selection

**Issue link:** [https://github.com/ascherj/pathreview/issues/32](https://github.com/ascherj/pathreview/issues/32)

**Issue title:** Implement a caching layer for repeated identical portfolio queries

**Tier:** [ ] Tier 1  [x] Tier 2  [ ] Tier 3

**Problem summary:**
Currently, every time a user requests a portfolio review, the system reruns the entire ingestion, RAG retrieval, and LLM generation pipeline, even if the portfolio content has not changed. This results in unnecessary processing time and repeated LLM calls for identical inputs. The fix should add a caching mechanism in the review generation workflow, using a hash of the profile's content to detect unchanged portfolios and return a previously generated review instead of regenerating it. This primarily affects the review processing logic in core/services/review_service.py and the RAG generation pipeline in rag/generator/review_generator.py. Implementing this cache will improve performance, reduce API costs, and provide faster response times for users submitting unchanged portfolios


**Branch name:** feat/32-caching-layer

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger


## Week 8 — Reproduction & solution planning

**Reproduction commit link:** [https://github.com/carlog1566/pathreview/commit/b2a878043a61822a479b45e94ae1159a49aedf2b](https://github.com/carlog1566/pathreview/commit/b2a878043a61822a479b45e94ae1159a49aedf2b)

**Reproduction summary:**
I reproduced the issue by creating two portfolio profiles with identical GitHub usernames and portfolio URLs and generating a review for each. Although the portfolio content was the same, the server logs showed that the ingestion pipeline, agent orchestration, RAG retrieval, and review generation were executed both times. This confirms that the application does not currently reuse previously generated reviews for identical portfolio content.

**PLAN.md link:** [https://github.com/carlog1566/pathreview/blob/feat/32-caching-layer/PLAN.md](https://github.com/carlog1566/pathreview/blob/feat/32-caching-layer/PLAN.md)

### Issue Reproduction

Reproduced issue #32 by generating reviews for identical portfolio content.

**Reproduction Steps**

1. Start the application and log in.
2. Create a new portfolio review with the following information:
   - GitHub username
   - Resume PDF
   - Portfolio URL
3. Generate a portfolio review.
4. After the review completes, submit the same portfolio information again and generate another review.
5. Observe the backend logs during both review requests.

**Observed behavior**
- Agent orchestration executes.
- RAG retrieval executes.
- Review generation executes.

**Expected behavior:**
- A cached review should be returned for identical portfolio content instead of rerunning the pipeline.