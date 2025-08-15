# Ragas Experimental to Main Migration Plan

## Executive Summary
This document outlines a phased approach to migrate experimental features into the main ragas source code without breaking existing functionality.

## Migration Phases

### Phase 1: Non-Conflicting Additions (Week 1-2)
These features can be added directly to main ragas without conflicts.

#### 1.1 Backend System
- **Action**: Copy entire `backends/` directory to main ragas
- **Location**: `ragas/src/ragas/backends/`
- **Dependencies**: None (self-contained)
- **Steps**:
  1. Copy `experimental/backends/` → `ragas/backends/`
  2. Update imports in backend files
  3. Add backend exports to main `__init__.py`
  4. Write integration tests
  5. Update documentation
  6. Remove experimental/backends/ directory
  7. Update examples/ to use new backend imports (if any)
- **Test Migration**:
  - **From**: `tests/experimental/unit/backends/`
    - `test_inmemory.py` (975 lines, 28 tests)
    - `test_local_csv.py`
    - `test_local_jsonl.py`
    - `test_gdrive_backend.py`
  - **To**: `tests/unit/backends/`
  - **Changes needed**:
    - Update imports from `ragas.experimental.backends` to `ragas.backends`
    - Ensure test fixtures work with main ragas structure
    - Add to main test suite markers
  - **Test coverage**: Comprehensive coverage for all backend operations including save, load, list, delete, exceptions

#### 1.2 Experiment Management
- **Action**: Add experiment functionality as new module
- **Location**: `ragas/src/ragas/experiment.py`
- **Dependencies**: gitpython (add to pyproject.toml)
- **Steps**:
  1. Copy `experimental/experiment.py` → `ragas/experiment.py`
  2. Update imports
  3. Add exports to main `__init__.py`
  4. Add gitpython dependency
  5. Write tests and documentation
  6. Remove experimental/experiment.py
- **Test Migration**:
  - **From**: No existing tests found - needs new tests
  - **To**: `tests/unit/test_experiment.py`
  - **New tests needed**:
    - Test `Experiment` class functionality
    - Test `experiment` decorator
    - Test `version_experiment()` with mock git repo
    - Test async experiment support
    - Test experiment naming and tracking
  - **Test fixtures needed**: Mock git repo, sample experiment functions

#### 1.3 User Simulation
- **Action**: Add as new module
- **Location**: `ragas/src/ragas/simulation.py`
- **Dependencies**: None
- **Steps**:
  1. Copy `experimental/simulation.py` → `ragas/simulation.py`
  2. Update imports
  3. Add to main exports
  4. Write comprehensive tests
  5. Remove experimental/simulation.py 
- **Test Migration**:
  - **From**: No existing tests found - needs new tests
  - **To**: `tests/unit/test_simulation.py`
  - **New tests needed**:
    - Test `Message` and `ConversationHistory` classes
    - Test `UserSimulator` class
    - Test `validate_agent_function()`
    - Test conversation flow and message validation
    - Test prompt-based simulation
  - **Test fixtures needed**: Mock LLM, sample agent functions, conversation histories

#### 1.4 CLI Tool
- **Action**: Add as new optional module
- **Location**: `ragas/src/ragas/cli.py`
- **Dependencies**: typer, rich (add as optional dependencies)
- **Steps**:
  1. Copy `experimental/cli.py` → `ragas/cli.py`
  2. Add CLI dependencies as optional extras in pyproject.toml
  3. Create entry point in pyproject.toml
  4. Update imports and test CLI commands
- **Test Migration**:
  - **From**: No existing tests found - needs new tests
  - **To**: `tests/unit/test_cli.py`
  - **New tests needed**:
    - Test CLI commands (run, list, etc.)
    - Test CLI output formatting
    - Test error handling
    - Test with mock experiments
  - **Test approach**: Use Typer's testing utilities, mock filesystem operations

### Phase 2: Enhanced Features (Week 3-4)
These features enhance existing functionality and need careful integration.

#### 2.1 Tracing Integrations
- **Action**: Add to existing callbacks system
- **Location**: `ragas/src/ragas/integrations/tracing/`
- **Dependencies**: langfuse, mlflow (optional)
- **Steps**:
  1. Create `integrations/tracing/` directory
  2. Copy langfuse.py and mlflow.py
  3. Integrate with existing callback system
  4. Add optional dependencies
  5. Ensure backward compatibility
- **Test Migration**:
  - **From**: No existing tests found - needs new tests
  - **To**: `tests/unit/integrations/test_tracing.py`
  - **New tests needed**:
    - Test Langfuse integration
    - Test MLflow integration
    - Test callback integration
    - Test with missing dependencies (should fail gracefully)
  - **Test approach**: Mock external services, test data flow

#### 2.2 Factory Functions
- **Action**: Add alongside existing instantiation methods
- **Location**: `ragas/src/ragas/llms/factory.py` and `ragas/src/ragas/embeddings/factory.py`
- **Steps**:
  1. Extract factory functions to dedicated modules
  2. Keep existing instantiation methods
  3. Export factories from main init
  4. Document both approaches
- **Test Migration**:
  - **From**: `tests/experimental/unit/test_llm.py` (partial coverage)
  - **To**: `tests/unit/llms/test_factory.py` and `tests/unit/embeddings/test_factory.py`
  - **Changes needed**:
    - Extract factory-specific tests
    - Add tests for all supported providers
    - Test error handling for unknown providers
  - **Test coverage**: All providers, error cases, configuration options

#### 2.3 Enhanced Metrics
- **Action**: Add new metric types without breaking existing ones
- **Location**: `ragas/src/ragas/metrics/experimental/`
- **Steps**:
  1. Create experimental subdirectory in metrics
  2. Copy new metric base classes
  3. Add decorators for metric creation
  4. Ensure existing metrics continue to work
  5. Provide migration guide for metric authors
- **Test Migration**:
  - **From**: `tests/experimental/unit/test_metric_base.py`
  - **To**: `tests/unit/metrics/experimental/`
  - **Changes needed**:
    - Update imports
    - Add integration tests with existing metrics
    - Test decorator functionality
  - **Test fixtures**: Use `mock_llm` from experimental conftest
  - **Additional tests**: Compatibility tests between old and new metric systems

### Phase 3: Complex Merges (Week 5-6)
These require careful design to merge with existing functionality.

#### 3.1 Dataset/DataTable Integration
- **Action**: Merge DataTable functionality into existing dataset classes
- **Strategy**: Add backend support to existing classes
- **Steps**:
  1. Add backend parameter to `RagasDataset` and `EvaluationDataset`
  2. Implement save/load methods using backend system
  3. Keep existing API unchanged (backward compatible)
  4. Add DataTable as alias for compatibility
  5. Extensive testing for both old and new usage
- **Test Migration**:
  - **From**: `tests/experimental/unit/test_datatable.py` (287 lines)
  - **To**: `tests/unit/test_enhanced_dataset.py`
  - **Test strategy**:
    - Keep all existing dataset tests unchanged
    - Add new test file for enhanced functionality
    - Test both old API (without backend) and new API (with backend)
    - Test DataTable alias works correctly
  - **Critical tests**:
    - Backward compatibility tests
    - Backend integration tests
    - Type preservation tests (from test_datatable.py)
    - Iterator and slicing behavior

### Implementation Guidelines

#### For Each Migration:
1. **Preserve existing API**: Never break existing functionality
2. **Add deprecation warnings**: If changing behavior, warn users
3. **Write migration guides**: Document how to use new features
4. **Test extensively**: Both unit and integration tests
5. **Update documentation**: Both API docs and tutorials

#### Testing Strategy:
```python
# Example test structure for each migrated feature
def test_backward_compatibility():
    # Ensure old usage still works
    pass

def test_new_functionality():
    # Test new features
    pass

def test_integration():
    # Test interaction with existing features
    pass
```

#### Import Strategy:
```python
# Main ragas __init__.py additions
# Phase 1
from ragas.backends import BaseBackend, get_registry
from ragas.experiment import experiment, Experiment
from ragas.simulation import UserSimulator

# Phase 2 (alongside existing)
from ragas.llms.factory import llm_factory
from ragas.embeddings.factory import embedding_factory

# Phase 3 (careful aliasing)
Dataset = DataTable  # For compatibility
```

## Risk Mitigation

### 1. Dependency Conflicts
- Add new dependencies as optional extras
- Use try/except for optional imports
- Clear documentation on installation extras

### 2. API Breaking Changes
- Never remove existing functionality
- Use deprecation warnings for changes
- Provide clear migration paths

### 3. Performance Impact
- Profile before and after migration
- Ensure lazy imports for heavy dependencies
- Benchmark critical paths

## Test Migration Summary

### Existing Tests to Migrate
1. **Backend tests** (4 files, ~1000+ lines total)
   - Comprehensive coverage for all backend operations
   - Each backend has its own test file
   
2. **DataTable tests** (287 lines)
   - Type preservation and inheritance tests
   - Iterator and slicing behavior tests

3. **Metric base tests** (103 lines)
   - Custom metric creation
   - Variable extraction from prompts

4. **Other experimental tests**
   - `test_dynamic_few_shot_prompt.py`
   - `test_prompt_save_load.py`
   - `test_llm.py` (partial - factory-related tests)

### New Tests Needed
1. **Experiment management** - No existing tests
2. **User simulation** - No existing tests  
3. **CLI tool** - No existing tests
4. **Tracing integrations** - No existing tests

### Shared Test Utilities to Migrate
From `tests/experimental/conftest.py`:
- `MockLLM` class - Mock LLM for testing
- `MockEmbedding` class - Mock embedding model
- Experimental test markers (`experimental_ci`, `e2e`)
- Common fixtures (`mock_llm`, `mock_embedding`)

### Test Migration Strategy
1. **Phase 1**: Direct copy with import updates
2. **Phase 2**: Adapt tests to work with enhanced features
3. **Phase 3**: Add compatibility tests between old and new APIs

### Test Coverage Goals
- Maintain 100% backward compatibility
- Achieve >90% coverage for new features
- Add integration tests between old and new systems
- Include performance regression tests

## Success Criteria

1. All existing tests pass
2. New features have >90% test coverage
3. No breaking changes to public API
4. Documentation updated for all features
5. Performance benchmarks show no regression
6. All migrated tests pass in main test suite

# Test Migration Verification Checklist

## Phase 1: Non-Conflicting Additions

### Backend System
- [ ] Migrate `test_inmemory.py` (28 tests)
  - [ ] Update imports from `ragas.experimental.backends` to `ragas.backends`
  - [ ] Verify all 28 tests pass
  - [ ] Check coverage remains at 100%
- [ ] Migrate `test_local_csv.py`
  - [ ] Update imports
  - [ ] Verify CSV operations work correctly
  - [ ] Test with different data types
- [ ] Migrate `test_local_jsonl.py`
  - [ ] Update imports
  - [ ] Verify JSONL operations work correctly
  - [ ] Test with nested data structures
- [ ] Migrate `test_gdrive_backend.py`
  - [ ] Update imports
  - [ ] Mark as optional test (requires credentials)
  - [ ] Verify skip conditions work properly
- [ ] Add integration test for backend registry
- [ ] Verify backend auto-discovery works

### Experiment Management
- [ ] Create `test_experiment.py`
  - [ ] Test Experiment class inheritance from DataTable
  - [ ] Test experiment decorator functionality
  - [ ] Test async experiment support
  - [ ] Test version_experiment with mock git
  - [ ] Test experiment naming (memorable names)
- [ ] Add fixtures for mock git repository
- [ ] Test git operations are properly isolated

### User Simulation  
- [ ] Create `test_simulation.py`
  - [ ] Test Message class validation
  - [ ] Test ConversationHistory operations
  - [ ] Test UserSimulator initialization
  - [ ] Test validate_agent_function
  - [ ] Test conversation flow
- [ ] Add fixtures for mock conversations
- [ ] Test with MockLLM from conftest

### CLI Tool
- [ ] Create `test_cli.py`
  - [ ] Test CLI initialization
  - [ ] Test run command
  - [ ] Test list command
  - [ ] Test error handling
  - [ ] Test output formatting
- [ ] Use Typer's testing client
- [ ] Mock filesystem operations
- [ ] Test with missing optional dependencies

## Phase 2: Enhanced Features

### Tracing Integrations
- [ ] Create `test_tracing.py`
  - [ ] Test Langfuse integration
  - [ ] Test MLflow integration
  - [ ] Test callback integration
  - [ ] Test missing dependencies handling
- [ ] Mock external services
- [ ] Verify data flow to tracing services

### Factory Functions
- [ ] Create `test_llm_factory.py`
  - [ ] Extract relevant tests from `test_llm.py`
  - [ ] Test all supported providers
  - [ ] Test configuration options
  - [ ] Test error handling
- [ ] Create `test_embedding_factory.py`
  - [ ] Test all supported providers
  - [ ] Test configuration options
  - [ ] Test error handling

### Enhanced Metrics
- [ ] Migrate `test_metric_base.py`
  - [ ] Update imports to experimental subdir
  - [ ] Verify decorator functionality
  - [ ] Test metric types (Discrete, Numeric, Ranking)
- [ ] Add compatibility tests
  - [ ] Test old metrics work with new system
  - [ ] Test new metrics integrate properly

## Phase 3: Complex Merges

### Dataset/DataTable Integration
- [ ] Migrate `test_datatable.py` tests
  - [ ] Update to test enhanced dataset classes
  - [ ] Verify type preservation works
  - [ ] Test iterator behavior
  - [ ] Test slicing operations
- [ ] Create backward compatibility tests
  - [ ] Test old dataset API still works
  - [ ] Test new backend features
  - [ ] Test DataTable alias
- [ ] Integration tests
  - [ ] Test dataset with different backends
  - [ ] Test save/load operations
  - [ ] Test type safety

## Shared Test Utilities

### From experimental conftest.py
- [ ] Migrate `MockLLM` class
  - [ ] Add to main conftest or test utils
  - [ ] Update all tests using it
- [ ] Migrate `MockEmbedding` class  
  - [ ] Add to main conftest or test utils
  - [ ] Update all tests using it
- [ ] Update test markers
  - [ ] Remove `experimental_ci` marker
  - [ ] Integrate with main CI markers

## Test Verification Steps

### For Each Test File:
1. [ ] Run tests in isolation
2. [ ] Check coverage report
3. [ ] Verify no import errors
4. [ ] Check for deprecated warnings
5. [ ] Ensure fixtures work correctly

### Integration Testing:
1. [ ] Run all migrated tests together
2. [ ] Check for test interdependencies
3. [ ] Verify no resource leaks
4. [ ] Test with different Python versions

### Performance Testing:
1. [ ] Benchmark test execution time
2. [ ] Compare with experimental baseline
3. [ ] Check memory usage
4. [ ] Verify no performance regression

## Success Metrics

- [ ] All tests passing (100% success rate)
- [ ] Code coverage ≥ 90% for migrated code
- [ ] No increase in test execution time
- [ ] All backward compatibility tests pass
- [ ] CI/CD pipeline runs successfully

## Next Steps

1. Review and approve migration plan
2. Set up feature branches for each phase
3. Begin Phase 1 implementation
4. Regular progress reviews