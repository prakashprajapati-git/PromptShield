# PromptShield — Build Progress

## Phase Checklist

- [ ] **Phase 0 — Scaffold**: monorepo tree, .gitignore, .env.example, CI workflow, README skeleton, frontend Vite+TS+Tailwind+shadcn init, backend venv + pinned requirements. DoD: pytest collects 0 tests OK; npm run build succeeds; CI file valid.
- [ ] **Phase 1 — Backend foundation**: app factory, lifespan warmup, config, /health, audit models + SQLite store, metrics module. DoD: uvicorn boots, /health returns all-ready.
- [ ] **Phase 2 — Detection L1+L2**: Presidio engine + all 9 recognizers + Verhoeff/Luhn + tests. DoD: pytest backend/app/tests/test_recognizers*.py green; manual check on support_ticket_full.
- [ ] **Phase 3 — Risk engine + Layer 3**: data_gen → train → joblib artifacts + classifier with top_features + risk engine + tests. DoD: fixture scores ≥81; acquisition_memo triggers semantic.
- [ ] **Phase 4 — Sanitization**: 5 modes + vault + detokenizer + offset-safe apply + tests. DoD: round-trip test green.
- [ ] **Phase 5 — REST API**: analyze, sanitize, audit, metrics, rate limiting, audit writes + smoke tests. DoD: curl both endpoints, verify latency breakdown + audit rows.
- [ ] **Phase 6 — Policy engine**: YAML, loader, hot-reload, endpoints + tests.
- [ ] **Phase 7 — LLM + WS chat**: providers, factory, ws_chat with full event stream, response scanner, detokenize integration + tests (mock provider for CI). DoD: websocat/script round-trip shows all 6 stages.
- [ ] **Phase 8 — Frontend core**: design system, router, stores, api client, ws client, Inspect screen complete (highlighting, chips, dial, tabs, latency HUD). DoD: full analyze flow live in browser.
- [ ] **Phase 9 — Chat screen**: timeline, payload inspector, badges, BlockedCard, RAG context input. DoD: full tokenize round-trip visible end-to-end.
- [ ] **Phase 10 — Policies + Audit screens**: wired to backend. DoD: hot-reload demo works in UI.
- [ ] **Phase 11 — Hardening**: all §12 edge cases, obfuscation alternates, corrupted-token tolerance, 5KB input test, error toasts, provider-unavailable handling.
- [ ] **Phase 12 — Benchmark + fixtures + README/model card/RESULTS.md + final QA**: full pytest, ruff, tsc, eslint, vite build all green; print the manual smoke-test checklist.

---

## Notes

_Updated as phases complete._
