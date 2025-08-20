# Ragas Experimental to Main Migration Plan

## Executive Summary

Migration of experimental features (~4,448 lines) into main ragas codebase using a cycle-based approach.

**Current Status (Aug 2025):**
- **Cycle 1**: 75% complete (3/4 items done, 1 removed)  
- **Cycle 2**: 50% in progress (4 active PRs)
- **Cycle 3**: Pending (complex merges awaiting Cycle 2)

## Migration Status Overview

| Feature | Status | PR | Lines | Notes |
|---------|--------|----| ------|-------|
| Backend System | **Completed** | [#2174](https://github.com/explodinggradients/ragas/pull/2174) | 0 | Tests migrated |
| Experiment Management | **Completed** | [#2175](https://github.com/explodinggradients/ragas/pull/2175) | 0 | Enhanced utils.py |
| CLI Tool | **Completed** | [#2183](https://github.com/explodinggradients/ragas/pull/2183) | 0 | Entry point added |
| User Simulation | **Removed** | [#2187](https://github.com/explodinggradients/ragas/pull/2187) | 326 | Cleanup pending |
| Tracing Integrations | **Ready for Review** | [#2188](https://github.com/explodinggradients/ragas/pull/2188) | 227 | Tests added |
| Prompts Migration | **Ready for Review** | [#2197](https://github.com/explodinggradients/ragas/pull/2197) | ~100 | Simple prefix approach |
| LLM Refactor | **In Development** | [#2191](https://github.com/explodinggradients/ragas/pull/2191) | 440 | InstructorLLM |
| Embeddings Refactor | **In Development** | [#2184](https://github.com/explodinggradients/ragas/pull/2184) | 1000 | Backward compatible |
| Enhanced Metrics | **Pending** | - | 1300 | Depends on other work |
| Dataset Integration | **Pending** | - | 460 | Needs backend + metrics |
| Dynamic Prompts | **Pending** | - | 430 | Coordinate with #2197 |
| Utils & Exceptions | **Pending** | - | 400 | Utility integration |

## Migration Cycles

### Cycle 1: Foundation (Completed)
Direct migrations with no conflicts. Backend system, experiment management, and CLI now in main package.

### Cycle 2: Active Development 
Enhanced features requiring careful integration. 4 PRs currently active:

**Ready for Merge:**
- [#2188](https://github.com/explodinggradients/ragas/pull/2188) - Tracing integrations (Langfuse, MLflow)
- [#2197](https://github.com/explodinggradients/ragas/pull/2197) - Prompts with Simple prefix

**In Development:**
- [#2191](https://github.com/explodinggradients/ragas/pull/2191) - LLM structure refactor + InstructorLLM
- [#2184](https://github.com/explodinggradients/ragas/pull/2184) - Embeddings with dual API support

### Cycle 3: Complex Integrations
Remaining features requiring coordination with Cycle 2 completions:

| Feature | Dependencies | Action Required |
|---------|-------------|-----------------|
| Enhanced Metrics (1300 lines) | None | Create migration plan |
| Dataset Integration (460 lines) | Backends (done), Enhanced Metrics | Design backend integration |
| Dynamic Prompts (430 lines) | Prompts migration (#2197) | Coordinate integration |
| Utils & Exceptions (400 lines) | None | Merge with main utilities |

## Immediate Actions (Next 2 Weeks)

1. **Review and merge ready PRs**: #2188, #2197
2. **Complete active refactors**: #2191, #2184  
3. **Clean up simulation remnants**: Remove 326 lines from experimental
4. **Plan Cycle 3**: Define approach for remaining 2,590 lines

## Test Migration Status

| Test File | Status | Lines | Action |
|-----------|--------|-------|--------|
| Backend tests | **Migrated** | 1000+ | Complete |
| CLI tests | **Migrated** | - | Complete |  
| Experiment tests | **Migrated** | - | Complete |
| test_datatable.py | **Pending** | 287 | Migrate to enhanced_dataset |
| test_metric_base.py | **Pending** | 103 | Migrate with metrics |
| test_dynamic_few_shot_prompt.py | **Pending** | - | Migrate with prompts |
| MockLLM/MockEmbedding utilities | **Pending** | - | Move to main conftest |

## Success Criteria

1. All existing tests pass without regression
2. No breaking changes to public APIs
3. Complete removal of `experimental` directory

## Key Principles

- **Backward Compatibility**: Never break existing functionality
- **Deprecation Warnings**: Clear migration paths for API changes