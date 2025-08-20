# Ragas Experimental to Main Migration Plan

## Executive Summary

Migration of experimental features into main ragas codebase using a cycle-based approach.

**Current Status (Aug 2025):**
- **Cycle 1**: 75% complete (3/4 items done, 1 removed)  
- **Cycle 2**: 50% in progress (4 active PRs)
- **Cycle 3**: Pending (complex merges awaiting Cycle 2)

## Migration Status Overview

| Feature | Status | PR | Notes |
|---------|--------|----|-------|
| Backend System | **Completed** | [#2174](https://github.com/explodinggradients/ragas/pull/2174) | Tests migrated |
| Experiment Management | **Completed** | [#2175](https://github.com/explodinggradients/ragas/pull/2175) | Enhanced utils.py |
| CLI Tool | **Completed** | [#2183](https://github.com/explodinggradients/ragas/pull/2183) | Entry point added |
| User Simulation | **Removed** | [#2187](https://github.com/explodinggradients/ragas/pull/2187) | Cleanup pending |
| Tracing Integrations | **Ready for Review** | [#2188](https://github.com/explodinggradients/ragas/pull/2188) | Tests added |
| Prompts Migration | **Ready for Review** | [#2197](https://github.com/explodinggradients/ragas/pull/2197) | Simple prefix approach |
| LLM Refactor | **In Development** | [#2191](https://github.com/explodinggradients/ragas/pull/2191) | InstructorLLM |
| Embeddings Refactor | **In Development** | [#2184](https://github.com/explodinggradients/ragas/pull/2184) | Backward compatible |
| Enhanced Metrics | **Pending** | - | Depends on other work |
| Dataset Integration | **Pending** | - | Needs backend + metrics |
| Dynamic Prompts | **Pending** | - | Coordinate with #2197 |
| Utils & Exceptions | **Pending** | - | Utility integration |

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

**Pending:**
| Feature | Dependencies | Action Required |
|---------|-------------|-----------------|
| Utils & Exceptions | None | Merge with main utilities |
| Dynamic Prompts | Prompts migration (#2197) | Coordinate integration |
| Dynamic Prompts | Prompts migration (#2197) | Coordinate integration |

### Cycle 3: Complex Integrations
Remaining features requiring coordination with Cycle 2 completions:

|---------|-------------|-----------------|
| Enhanced Metrics | None | Create migration plan |
| Dataset Integration | Backends (done), Enhanced Metrics | Design backend integration |

## Test Migration Status

| Test File | Status | Action |
|-----------|--------|---------|
| Backend tests | **Migrated** | Complete |
| CLI tests | **Migrated** | Complete |  
| Experiment tests | **Migrated** | Complete |
| test_datatable.py | **Pending** | Migrate to enhanced_dataset |
| test_metric_base.py | **Pending** | Migrate with metrics |
| test_dynamic_few_shot_prompt.py | **Pending** | Migrate with prompts |
| MockLLM/MockEmbedding utilities | **Pending** | Move to main conftest |

## Success Criteria

1. All existing tests pass without regression
2. No breaking changes to public APIs
3. Complete removal of `experimental` directory

## Key Principles

- **Backward Compatibility**: Never break existing functionality
- **Deprecation Warnings**: Clear migration paths for API changes