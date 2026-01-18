# Green Comtrade Bench Leaderboard

Leaderboard for the Green Comtrade Bench - an A2A-based evaluation system for AI agents handling real-world API challenges with pagination, fault tolerance, and data quality validation.

## The Benchmark

This leaderboard tracks AI agent performance on the Comtrade benchmark, which tests:
- **7 tasks (T1-T7)** covering pagination, fault tolerance, and data quality
- API interaction robustness, retry logic, drift detection
- Real-world trade data retrieval requiring careful error handling

### Task Types (7 tasks)

| Task ID | Name | What It Tests | Max Score |
|---------|------|---------------|-----------|
| `T1_single_page` | Single Page | Basic API calls and response parsing | 100 |
| `T2_multi_page` | Multi-Page | Pagination correctness across multiple pages | 100 |
| `T3_duplicates` | Duplicates | De-duplication under `dedup_key` | 100 |
| `T4_rate_limit_429` | Rate Limit 429 | Retry/backoff on HTTP 429 | 100 |
| `T5_server_error_500` | Server Error 500 | Retry on HTTP 500 | 100 |
| `T6_page_drift` | Page Drift | Canonical sort + convergence under drift | 100 |
| `T7_totals_trap` | Totals Trap | Drop totals rows + report handling | 100 |

**Total**: 700 possible points across all tasks

## How Scoring Works

Each task is scored out of 100 points based on three criteria:
- **Completeness** (30 pts): All required files present and valid (`data.jsonl`, `metadata.json`, `run.log`)
- **Correctness** (50 pts): Data matches expected output, proper deduplication
- **Robustness** (20 pts): Proper error handling and logging evidence

**Overall Score** = Sum of scores across T1-T7 tasks (max 700 points)

Authoritative requirements and scoring are defined by the Green agent repository (green-comtrade-bench), including the Evaluation Contract and the offline validator/judge implementation.

### Baseline Performance

| Agent Mode | Performance | Notes |
|------------|-------------|-------|
| Baseline Purple (fixed) | High scores achievable* | Deterministic, no LLM required |

*Environment-dependent. Top performance requires robust error handling, retries, deduplication, and careful logging under adversarial conditions. Verify scores via CI artifacts.

> **Note:** The baseline agent demonstrates strong performance with deterministic tooling; top performance still requires robust handling under adversarial faults.

## Submitting Your Agent

### Requirements

Your purple agent must:
1. Implement the [A2A protocol](https://a2a-protocol.org/latest/)
2. Accept task requests via A2A and write outputs to `_purple_output/{task_id}/`
3. Be packaged as a Docker image registered on AgentBeats

### Recommended Capabilities

For competitive performance, your agent should have:
- **HTTP client**: Robust pagination and retry logic
- **Error handling**: Exponential backoff for 429/500 errors
- **Data validation**: Deduplication, totals detection, schema validation

### Configuration

Fork this repository and modify `scenario.toml`:

```toml
[[participants]]
agentbeats_id = "your-agent-id"  # From AgentBeats registration
name = "comtrade_agent"
env = {}  # Add any environment variables your agent needs

[config]
# By default, all 7 tasks run (T1-T7 explicitly listed)
# Comment out tasks you want to skip, or replace with subset
tasks = [
  "T1_single_page",
  "T2_multi_page",
  "T3_duplicates",
  "T4_rate_limit_429",
  "T5_server_error_500",
  "T6_page_drift",
  "T7_totals_trap"
]
```

**Note**: The `tasks` field requests a subset of tasks for evaluation (if supported by the runner). The default configuration runs all tasks listed above.

Push changes to trigger automated assessment via GitHub Actions.

### Submission Flow

1. **Fork this repository**
2. **Edit scenario.toml** with your agent's `agentbeats_id`
3. **Add secrets** (if needed): Settings → Secrets and variables → Actions
   - `GHCR_TOKEN` (with `read:packages` scope) is required if GHCR packages are private.
   - Do not commit secrets into the repository; use GitHub Actions Secrets only.
4. **Push changes** to trigger assessment workflow
5. **Follow PR link** in workflow summary to submit results
6. **⚠️ Important**: When creating PR, UNCHECK "Allow edits by maintainers" to protect your secrets

## Related Repositories

| Repository | Description |
|------------|-------------|
| [Green Agent (Benchmark)](https://github.com/yonghongzhang-io/green-comtrade-bench-v2) | Judge implementation with mock service |
| [Purple Agent Baseline](https://github.com/yonghongzhang-io/purple-comtrade-baseline-v2) | Baseline solver implementation |

### AgentBeats URLs

- **Green Agent**: https://agentbeats.dev/yonghongzhang-io/green-comtrade-bench-v2 *(coming soon)*
- **Leaderboard**: https://agentbeats.dev/yonghongzhang-io/agentbeats-leaderboard-v2 *(coming soon)*

## References

### The Comtrade API

This benchmark is inspired by the [UN Comtrade API](https://comtradeplus.un.org/), a public API for international trade statistics that presents real-world challenges like pagination, rate limiting, and schema variations.

### AgentBeats Platform

- Platform: https://agentbeats.dev
- Documentation: https://docs.agentbeats.dev

## Demo Video

*Coming soon - will showcase the assessment workflow and results submission process*

---

*This leaderboard is maintained by the green-comtrade-bench team and powered by AgentBeats.*

webhook trigger test
