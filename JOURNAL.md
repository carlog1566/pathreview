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


## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
I identified that the review processing workflow in core/services/review_service.py always executes the ingestion pipeline, agent orchestration, and RAG generation regardless of whether an identical portfolio has already been reviewed. I investigated the review pipeline and confirmed that no cache lookup currently exists before processing begins. I also determined the files that will be modified to implement a content hash lookup and review reuse.

**Next steps:**
- Implement a deterministic content hash for portfolio data.
- Add a cache lookup before the ingestion pipeline begins.
- Reuse completed review results when a matching content hash is found.
- Add or update unit tests to verify cache hits and cache misses.
- Run make test-unit and make check before opening the pull request.

**Blockers:**
Need to verify whether the Review model already contains a field for storing a content hash. If not, a database migration will be required.

---

### Check-in 2 (end of week)

**PR link:** [https://github.com/ascherj/pathreview/pull/981](https://github.com/ascherj/pathreview/pull/981)

**Branch:** feat/32-caching-layer

**What you built:**
Implemented review result caching to prevent duplicate processing of unchanged
portfolio submissions. The system generates a deterministic SHA256 hash from
portfolio data and uses it to identify previously completed reviews, allowing
cached results to be reused instead of rerunning the ingestion, agent
orchestration, and RAG pipeline.

**Tests added or updated:**
Added pytest coverage in the review service tests:

- Tested that identical portfolio data consistently produces the same profile
  hash.
- Tested that changes to portfolio data generate a different profile hash.
- Tested that `process_review()` correctly uses a cached completed review and
  restores cached sections and overall score.

**Self-review confirmation:** [x] make check passes  [x] make test-unit passes

**Draft PR feedback received from:** none